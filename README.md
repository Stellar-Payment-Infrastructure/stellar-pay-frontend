# stellar-pay-frontend

[![CI](https://github.com/Stellar-Payment-Infrastructure/stellar-pay-frontend/actions/workflows/ci.yml/badge.svg)](https://github.com/Stellar-Payment-Infrastructure/stellar-pay-frontend/actions/workflows/ci.yml)

**Status: testnet only · v0.1.0.**

React components and hooks for accepting payments through the
[Stellar Pay API](https://github.com/Stellar-Payment-Infrastructure/stellar-pay-api), plus a demo storefront with a
hosted checkout page (`demo/`, Next.js). The payer signs with their own wallet; the browser never holds a merchant
key.

## What is implemented

| Export | What it does |
|---|---|
| `PaymentButton` | Calls **your** server endpoint (which creates the payment with the secret key) and redirects to checkout |
| `CheckoutPage` | Hosted checkout: connect wallet → build `pay` transaction → sign → submit → live status |
| `PaymentStatusTracker` | Accessible status timeline (created → paid → settled, or refunded/cancelled) |
| `PaymentLinkGenerator`, `checkoutUrl` | Shows the `/pay/<id>` link with a copy button |
| `QRCodeDisplay` | QR code (rendered locally as a `data:` image) with a text alternative |
| `WalletProvider`, `useWallet`, `walletsKitAdapter` | Wallet connection through a small adapter interface; adapter for Stellar Wallets Kit |
| `usePayment`, `usePaymentStatus` | Pay flow state machine with typed errors; status polling that stops at terminal states |
| `OnRampProvider` (types only) | Interface for fiat on-ramp providers; no live integration |
| `formatAmount`, `parseAmount` | Integer-safe amount helpers (re-exported from `stellar-pay-sdk`) |

Styling uses CSS variables (`--sp-*`) with a light and a dark theme. Components render on the server without
touching `window`, and the tests run axe-core accessibility checks on every component (color contrast excluded,
because jsdom has no layout).

## Not implemented yet

- Recurring payments ([frontend-1](ROADMAP.md)); there is no contract support for pull payments yet.
- Payment link customization: branding, custom fields, messages ([frontend-2](ROADMAP.md)).
- A mobile-first pass: layouts are fluid and tested on desktop and tablet widths ([frontend-3](ROADMAP.md)).
- A full integration guide ([frontend-4](ROADMAP.md)); this README is the quick start.
- No live deployment of the demo yet; no i18n; no Shopify/WooCommerce plugins.

## Quick start

<!-- TODO(wave: frontend-4): move the full integration guide (React and plain-JS embed, theming, error handling,
     troubleshooting) to docs/ and keep this section as the short version. -->

```bash
npm install stellar-pay-frontend stellar-pay-sdk
```

Your server creates the payment (secret key, server-side only):

```ts
// e.g. POST /api/checkout
import { StellarPay } from 'stellar-pay-sdk';
const sp = new StellarPay({ apiKey: process.env.STELLAR_PAY_API_KEY!, baseUrl: process.env.STELLAR_PAY_API_URL! });
const payment = await sp.payments.create({ amount: 25_000_000n, reference: 'order-42' }); // 2.5 XLM
return Response.json({ checkoutUrl: payment.checkoutUrl });
```

Your page renders the button:

```tsx
import 'stellar-pay-frontend/theme.css';
import { PaymentButton } from 'stellar-pay-frontend';

<PaymentButton createCheckout={() => fetch('/api/checkout', { method: 'POST' }).then((r) => r.json())}>
  Pay with Stellar
</PaymentButton>;
```

The hosted checkout page (see `demo/app/pay/[id]/CheckoutClient.tsx`):

```tsx
import { CheckoutPage, WalletProvider } from 'stellar-pay-frontend';
import { PublicClient } from 'stellar-pay-sdk';

const client = new PublicClient({ baseUrl: 'https://api.example.com' });

<WalletProvider adapter={yourWalletAdapter}>
  <CheckoutPage client={client} paymentId={paymentId} />
</WalletProvider>;
```

`yourWalletAdapter` implements `connect()` and `signTransaction()`; `demo/lib/wallet.ts` shows one built on
Stellar Wallets Kit that also refuses wallets that are not on testnet.

## Demo storefront

`demo/` is a Next.js app: a product page, a server route that creates the payment with the SDK, and the hosted
checkout at `/pay/<id>`. It needs a running stellar-pay-api.

```bash
pnpm install && pnpm run build          # build the library the demo imports
cd demo && cp .env.example .env         # set STELLAR_PAY_API_KEY (server-only) and the API URLs
pnpm dev                                # http://localhost:3001
```

Browser tests (mock wallet, mocked API responses) run against a production build:
`pnpm run build && pnpm exec playwright install chromium && pnpm e2e` in `demo/`.

## Security

- Never put a secret API key in browser code or in a `NEXT_PUBLIC_*` variable. The browser only calls the API's
  public checkout endpoints.
- Send a Content Security Policy from the page that hosts checkout. See [docs/CSP.md](docs/CSP.md); the demo's
  policy is in `demo/next.config.mjs`.
- Report vulnerabilities as described in [SECURITY.md](SECURITY.md).

## Verify

```bash
pnpm install --frozen-lockfile && pnpm run lint && pnpm run typecheck && pnpm run test && pnpm run build
cd demo && pnpm run lint && pnpm run typecheck && pnpm run test && pnpm run build
```

## Compatibility

| stellar-pay-frontend | stellar-pay-sdk | Stellar Pay API | React |
|---|---|---|---|
| 0.1.x | 0.1.x | `/v1` | 18.2+ |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) and the open Wave issues in [ROADMAP.md](ROADMAP.md).

## License

[MIT](LICENSE)
