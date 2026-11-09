# Contributing to stellar-pay-frontend

Thanks for helping. These components sit on the page where payers sign transactions, so security and accessibility
matter as much as looks.

## Setup

Node 22+ and pnpm 9.

```bash
pnpm install
pnpm run lint && pnpm run typecheck && pnpm run test && pnpm run build
cd demo && pnpm run lint && pnpm run typecheck && pnpm run test && pnpm run build
pnpm storybook   # component explorer on http://localhost:6006 (from the repository root)
```

## Wave issues

Planned contributor issues are listed in [ROADMAP.md](ROADMAP.md) and labeled `wave` on GitHub. Ask to be assigned
on the issue before starting, keep to its scope, and resolve its `TODO(wave: frontend-N)` marker.

## Code rules

- Function components, accessible by default: labels, roles, keyboard support, visible focus. Every component test
  runs axe (`expectNoA11yViolations`).
- Style with the `--sp-*` CSS variables in `src/theme.css`; no CSS-in-JS runtime.
- Nothing may touch `window` or `document` at import time (server rendering must work; see `ssr.test.tsx`).
- Never handle a secret API key in browser code. The browser talks only to the API's public checkout endpoints.
- Amounts are integer base units (`bigint` or decimal strings); format only for display with `formatAmount`.
- Every component needs tests for its main states and at least one failure state.

## Commits and pull requests

Use [Conventional Commits](https://www.conventionalcommits.org/) (`feat: add link branding options`), add a line to
[CHANGELOG.md](CHANGELOG.md) under `[Unreleased]`, and include a screenshot for visual changes.

By contributing you agree that your contributions are licensed under the [MIT License](LICENSE).
