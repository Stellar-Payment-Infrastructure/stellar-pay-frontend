# Content Security Policy for checkout pages

The page that hosts `CheckoutPage` asks a payer's wallet to sign a transaction. Injected script on that page could swap
the transaction or the displayed payment, and a page embedded in a hostile frame could trick the payer into clicking.
Send a Content Security Policy (CSP) and frame protection from every page that renders these components.

## Starting point

This is the policy the demo sends (`demo/next.config.mjs`), written for a Next.js app:

```
default-src 'self';
script-src 'self' 'unsafe-inline' 'wasm-unsafe-eval';
style-src 'self' 'unsafe-inline';
img-src 'self' data: https:;
connect-src 'self' <your API origin> https: wss:;
frame-ancestors 'none';
base-uri 'self';
form-action 'self'
```

plus `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin` and a
`Permissions-Policy` that disables camera, microphone and geolocation.

## What the components need

| Directive | Why |
|---|---|
| `connect-src` your API origin | `PublicClient` calls the API's public checkout endpoints (payment, build transaction, submit, status polling). The components make no other requests. |
| `img-src data:` | `QRCodeDisplay` renders the code locally as a `data:` image. Nothing is sent to a third-party QR service. |
| `style-src 'unsafe-inline'` (or `style-src-attr 'unsafe-inline'`) | A few components set small inline `style` props. When rendered on the server they become `style="…"` attributes, which a strict `style-src` blocks. Client-only rendering does not need this. |
| `frame-ancestors 'none'` | Stops clickjacking. If you must embed checkout in an iframe, list only your own origins. |

The components inject no scripts and use no `eval`.

## What your wallet setup may need

Wallet modules are loaded by your app, not by these components, and their needs differ:

- Some Stellar Wallets Kit modules use WebAssembly (`'wasm-unsafe-eval'` in `script-src`).
- Wallets that work through relays or popups need their origins in `connect-src` (often `wss:`) or `frame-src`.
  Check each wallet's documentation and test every wallet you enable with the policy in place.

The demo allows `https:` and `wss:` in `connect-src` so that any wallet works during testing. That is broad.

## Tightening it for production

1. Replace `https:` and `wss:` in `connect-src` with the exact origins: your API, plus what your enabled wallets
   need. Remove `https:` from `img-src` unless you load remote images.
2. Remove `'unsafe-inline'` from `script-src` by using nonces. In Next.js that means generating a nonce per request
   in middleware; see the Next.js CSP guide for the current API.
3. Add `report-to` (or `report-uri`) and watch reports before switching from `Content-Security-Policy-Report-Only`
   to an enforcing policy.
4. Serve checkout over HTTPS only, with HSTS.
