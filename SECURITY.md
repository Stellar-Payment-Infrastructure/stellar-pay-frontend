# Security policy

## Status

stellar-pay-frontend is **testnet-only** and has not had a professional security review.

| Version | Supported |
|---|---|
| 0.1.x | Yes (testnet) |

## Reporting a vulnerability

Please report privately through GitHub:
[Report a vulnerability](https://github.com/Stellar-Payment-Infrastructure/stellar-pay-frontend/security/advisories/new).
Do not open a public issue. We aim to acknowledge reports within 3 business days.

Especially interesting: anything that could make a payer sign a transaction other than the one shown, leak data
across payments, or put a merchant secret into the browser.

## Using the components safely

- Keep secret API keys on your server. `PaymentButton` calls your own endpoint, which creates the payment.
- Serve checkout over HTTPS with a Content Security Policy and `frame-ancestors` protection: see
  [docs/CSP.md](docs/CSP.md).
- Check the wallet's network before signing; the demo's wallet adapter refuses wallets that are not on testnet.
