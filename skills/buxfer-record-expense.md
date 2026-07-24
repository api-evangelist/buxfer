---
name: Record an expense in Buxfer
description: Authenticate to Buxfer, pick the right account, and record an expense transaction against it.
api: openapi/buxfer-openapi.yml
operations: [login, listAccounts, addTransaction]
---

# Record an expense in Buxfer

Use this to add a single expense to a Buxfer account.

## Auth
Buxfer uses a login-token model (no OAuth). See `authentication/buxfer-authentication.yml`.

1. Call `login` (POST) with `email` and `password`. Read `response.token`.
2. Pass that token as the `token` query parameter on every later call.

## Steps
1. `listAccounts` (GET, with `token`) — read `response.accounts[]` and choose the target account `id`.
2. `addTransaction` (POST) with:
   - `description` (required)
   - `amount` (required)
   - `accountId` (required — the id from step 1)
   - `type` = `expense`
   - optional `date` (YYYY-MM-DD), `tags` (comma-separated), `status` (`cleared`|`pending`)
3. Check `response.status` == `OK`; the created transaction id is `response.id`.

## Rules
- Success/failure is in the JSON envelope `response.status` (`OK` or `ERROR: <description>`), not the HTTP code. See `errors/buxfer-problem-types.yml`.
- POST requests ignore GET parameters — send fields in the form body.
- No idempotency key exists; do not blindly retry `addTransaction` or you may create duplicates (`conventions/buxfer-conventions.yml`).
