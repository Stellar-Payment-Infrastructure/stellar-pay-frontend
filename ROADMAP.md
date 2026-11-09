# Roadmap

## v0.1.0 (current, testnet)

Checkout components (`PaymentButton`, `CheckoutPage`, `PaymentStatusTracker`, `PaymentLinkGenerator`,
`QRCodeDisplay`), wallet adapter and hooks, CSS-variable theming with light and dark themes, an on-ramp provider
interface, and a demo storefront with hosted checkout. See the [README](README.md#what-is-implemented).

## Open Wave issues

Deliberately left for contributors. Each has a `TODO(wave: frontend-N)` marker at its plug-in point.

| Id | Points | Issue | Starting point |
|---|---|---|---|
| frontend-1 | 4 | **Recurring payment UI:** `RecurringPaymentForm` plus an SDK stub against the [draft API](docs/RECURRING_DRAFT.md), behind a feature flag, with tests | `src/index.ts`, [docs/RECURRING_DRAFT.md](docs/RECURRING_DRAFT.md) |
| frontend-2 | 3 | **Payment link customization:** branding (logo, colors), custom fields, a message, link options and a live preview | `src/components/PaymentLinkGenerator.tsx` |
| frontend-3 | 3 | **Mobile responsiveness:** mobile-first pass, breakpoints, touch targets, mobile wallet deep links, device testing | `src/theme.css`, `src/components/` |
| frontend-4 | 2 | **Integration documentation:** full guide (React and plain-JS embed), theming, error handling, troubleshooting | README quick start, `docs/` |

## Later

- Push status updates (SSE) instead of polling, once the API offers them.
- More wallet adapters and a per-wallet support matrix.

## Not planned for v1

Mainnet, live fiat on-ramp integrations (only the `OnRampProvider` interface exists), Shopify/WooCommerce plugins,
i18n beyond English.
