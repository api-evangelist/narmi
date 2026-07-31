---
name: Read account balances and transactions
description: Authenticate to the Narmi Public API and read the signed-in user's accounts, balances, and transaction history.
api: openapi/narmi-public-openapi-original.yml
operations: [me_list, accounts_list, account_balances_list, transactions_list]
---

# Read account balances and transactions

Use the Narmi Public API to retrieve a banking user's accounts and their activity. This is a read-only flow.

## Auth
- OAuth 2.0 authorization-code flow **with PKCE**.
- Authorize at `/v2/oauth/authorize/`, exchange the code at `/v2/oauth/token/`.
- Request scopes: `banking:accounts:read`, `banking:transactions:read` (and `banking:users:read` for the profile).
- Send `Authorization: Bearer <access_token>` on every call. Base URL: `https://api.sandbox.narmi.dev/` (sandbox).

## Steps
1. **Identify the user** — `GET /v1/me/` (`me_list`) to confirm the authenticated user.
2. **List accounts** — `GET /v1/accounts/` (`accounts_list`). Capture each account `uuid`.
3. **Get balances** — `GET /v1/account_balances/` (`account_balances_list`) for all accounts at once.
4. **List transactions** — `GET /v1/accounts/{account_uuids}/transactions/` (`transactions_list`) for a chosen account.

## Rules
- **Money is in minor units** — a balance of `1000` means $10.00. Never display raw integers as dollars.
- **Pagination** — list endpoints accept `page`, `limit`, and `offset`. Page through until results are exhausted.
- **Errors** — `401 authentication_failed` means refresh the token; `403 permission_denied` means the token lacks the needed `banking:*:read` scope. See `errors/narmi-problem-types.yml`.
