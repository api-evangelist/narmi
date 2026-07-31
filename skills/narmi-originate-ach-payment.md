---
name: Originate an ACH payment
description: Create an ACH payment from a Narmi account, safely, using an Idempotency-Key.
api: openapi/narmi-public-openapi-original.yml
operations: [accounts_list, ach_companies_list, ach_payments_create, ach_payments_retrieve]
---

# Originate an ACH payment

Create an outbound ACH payment on the Narmi Public API. This is a **money-movement** flow — treat it as safety-critical and require human confirmation before submitting.

## Auth
- OAuth 2.0 authorization-code + PKCE. Scopes: `banking:accounts:read`, `banking:transactions:write`.
- `Authorization: Bearer <access_token>`. Base URL: `https://api.sandbox.narmi.dev/`.

## Steps
1. **Choose the funding account** — `GET /v1/accounts/` (`accounts_list`); capture the source account `uuid`.
2. **Resolve the ACH company** (if required by the institution) — `GET /v1/ach_companies/` (`ach_companies_list`).
3. **Create the payment** — `POST /v1/ach_payments/` (`ach_payments_create`). Amounts are in **minor units** (cents).
4. **Confirm** — `GET /v1/ach_payments/{uuid}/` (`ach_payments_retrieve`) to read back the created payment's status.

## Rules
- **Always send an `Idempotency-Key` header** on `ach_payments_create`. Retrying with the same key is safe; a reused key returns `409 idempotency_key_used`. Generate a fresh key per distinct payment.
- **Validate before send** — a `422 invalid_request_error` returns field-level errors (e.g. routing number must be 9 digits).
- **Amounts in minor units** — $50.00 is `5000`.
- **Human-in-the-loop** — confirm payee, amount, and account with the user before calling `ach_payments_create`.
- See `conventions/narmi-conventions.yml` and `errors/narmi-problem-types.yml`.
