# Implementation plan

## Page design

### Dashboard

The dashboard will contain a month selector, summary totals, and category
spending. Each category should provide a route or link to the matching
transactions for the selected month.

### Transactions

The transactions page will contain search and filter controls, a transaction
table, and a form for adding or editing entries. Delete actions should require
clear user intent.

## Data model

The first version needs one transaction record with a unique ID, date,
description, category, type, and ISK amount.

## Build order

1. Define and inspect the starter data.
2. Create the SQLite database and import validated data.
3. Add read-only API responses for transactions and monthly summaries.
4. Build the dashboard.
5. Build the transaction list and filters.
6. Add create, edit, and delete operations.
7. Test month and category interactions.
8. Document setup and usage.

## Separate future upgrade

Category budgets should be designed after the first version is complete. The
upgrade will need monthly category limits and calculations for remaining
amounts, but it should not delay the core tracker.
