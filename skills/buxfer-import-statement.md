---
name: Import a bank statement into Buxfer
description: Authenticate, choose an account, and upload a bank or credit-card statement to import its transactions.
api: openapi/buxfer-openapi.yml
operations: [login, listAccounts, uploadStatement]
---

# Import a bank statement into Buxfer

Use this to bulk-import transactions from a downloaded statement.

## Auth
1. `login` (POST) with `email` + `password`; read `response.token`.
2. Pass `token` on every call.

## Steps
1. `listAccounts` (GET, `token`) — choose the destination account `id`.
2. `uploadStatement` (POST) with:
   - `accountId` (required)
   - `statement` (required — the raw statement text content)
   - optional `dateFormat` (`MM/DD/YYYY` or `DD/MM/YYYY`)
3. On success `response.status` == `OK`, with `response.uploaded` and the new `response.balance`.

## Rules
- Send the statement in the POST form body; GET parameters are ignored on POST.
- Set `dateFormat` explicitly when the statement's dates are ambiguous.
- Errors surface as `response.status` = `ERROR: <description>` (`errors/buxfer-problem-types.yml`).
