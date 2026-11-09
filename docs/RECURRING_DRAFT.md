# Recurring payments: draft API (not implemented)

**Status: draft.** Nothing on this page exists in the contract, the API or the SDK. It defines the target for Wave
issue frontend-1 so the UI can be built behind a feature flag before the backend exists. The shapes may change when
the backend is designed.

## Why it is not implemented

The payment contract charges a payer only when the payer signs `pay`. Charging on a schedule needs either a token
allowance (the payer approves the contract to pull up to a limit) or the payer signing every cycle. Neither design
is decided, so v0.1.0 has no recurring payments.

## What frontend-1 builds

1. `RecurringPaymentForm`: an accessible form for a plan (amount, interval, start date, optional end), with
   validation that uses integer base units (`parseAmount`), never floats.
2. A typed SDK stub matching the draft below. `createRecurringPlan` must not call the network: it rejects with an
   error whose `code` is `NOT_IMPLEMENTED`, and the form shows that state clearly.
3. A feature flag: the form renders only when explicitly enabled (for example a `features={{ recurring: true }}`
   prop on a provider), and is off by default.
4. Tests (behavior, validation, accessibility with axe) and a Storybook story.

## Draft request and response

```http
POST /v1/recurring-plans            (draft; requires a secret key with payments:write)
Idempotency-Key: <uuid>

{
  "amount": "10000000",             // token base units, decimal string
  "interval": "month",              // "day" | "week" | "month"
  "intervalCount": 1,               // every N intervals, 1-12
  "startAt": "2027-01-01T00:00:00Z",
  "endAfterCount": 12,              // optional: stop after N charges
  "reference": "plan-42"            // optional merchant reference, stays off-chain
}
```

```json
{
  "id": "rp_…",
  "status": "draft",
  "amount": "10000000",
  "interval": "month",
  "intervalCount": 1,
  "startAt": "2027-01-01T00:00:00Z",
  "endAfterCount": 12,
  "reference": "plan-42"
}
```

Errors use the API's normal envelope: `{ "error": { "code", "message", "details" } }`.
