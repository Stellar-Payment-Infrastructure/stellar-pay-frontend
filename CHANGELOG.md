# Changelog

All notable changes are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
and versions follow [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [0.1.0] - not yet tagged

First release, testnet only.

### Added

- Components: `PaymentButton`, `CheckoutPage`, `PaymentStatusTracker`, `PaymentLinkGenerator`, `QRCodeDisplay`.
- Wallet support: `WalletProvider`, `useWallet` and a Stellar Wallets Kit adapter.
- Hooks: `usePayment` (pay flow with typed errors) and `usePaymentStatus` (polling until a terminal state).
- `OnRampProvider` interface (types only).
- Theme with `--sp-*` CSS variables, light and dark.
- Demo storefront with hosted checkout (`demo/`), and Content Security Policy guidance (`docs/CSP.md`).
