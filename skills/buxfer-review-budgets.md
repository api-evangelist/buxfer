---
name: Review Buxfer budgets and recent spending
description: Authenticate and pull budgets plus the recent transactions behind them to review spending against limits.
api: openapi/buxfer-openapi.yml
operations: [login, listBudgets, listTransactions]
---

# Review Buxfer budgets and recent spending

Use this to see how spending is tracking against budget limits.

## Auth
1. `login` (POST) with `email` + `password`; read `response.token`.
2. Pass `token` on every call.

## Steps
1. `listBudgets` (GET, `token`) — read `response.budgets[]`; each has `name`, `limit`, `remaining`, `period`, `currentPeriod`.
2. For a budget of interest, `listTransactions` (GET, `token`) filtered by `budgetName` (or `budgetId`) and optionally `startDate`/`endDate`.
3. `listTransactions` returns at most 100 rows per call and a `response.numTransactions` total — increment `page` to fetch more.

## Rules
- Pagination is page-number: `page` param, fixed 100/page, total in `numTransactions` (`conventions/buxfer-conventions.yml`).
- Read-only flow — all three operations are safe GETs (login is the only POST).
- Check `response.status` == `OK` on each call.
