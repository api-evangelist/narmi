---
name: Manage card controls (lock, unlock, limits)
description: List a user's cards and apply controls — lock, unlock, and set spending limits — on the Narmi Public API.
api: openapi/narmi-public-openapi-original.yml
operations: [cards_list, cards_retrieve, cards_lock_create, cards_unlock_create, cards_limits_create]
---

# Manage card controls

Apply cardholder controls on the Narmi Public API: lock a lost card, unlock it, and set spending limits.

## Auth
- OAuth 2.0 authorization-code + PKCE. Scopes: `banking:accounts:read`, `banking:accounts:write`.
- `Authorization: Bearer <access_token>`. Base URL: `https://api.sandbox.narmi.dev/`.

## Steps
1. **List cards** — `GET /v1/cards/` (`cards_list`); capture the target `card_id`.
2. **Inspect a card** — `GET /v1/cards/{card_id}/` (`cards_retrieve`).
3. **Lock** — `POST /v1/cards/{card_id}/lock/` (`cards_lock_create`) to freeze a lost/stolen card.
4. **Unlock** — `POST /v1/cards/{card_id}/unlock/` (`cards_unlock_create`) to restore it.
5. **Set limits** — `POST /v1/cards/{card_id}/limits/` (`cards_limits_create`) for spending limits.

## Rules
- **Confirm intent** — locking/unlocking a card is a state change; confirm the specific card with the user first.
- **Errors** — `403 permission_denied` means the token lacks `banking:accounts:write`; `404` means the `card_id` is wrong. See `errors/narmi-problem-types.yml`.
- Related read-only helpers: `cards_limits_retrieve`, `cards_travel_note_list`, `cards_sensitive_retrieve`.
