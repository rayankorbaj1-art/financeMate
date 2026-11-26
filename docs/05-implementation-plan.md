# Implementation plan

## Shared data and interaction rules

The dashboard and transactions page will use the same rules defined in
[requirements](./requirements.md):

- The twenty fictional starter transactions cover October and November 2026.
- Dates are stored and displayed as `YYYY-MM-DD`; month filters compare both
  year and month.
- Categories are trimmed, non-empty free text. The same category values are
  used for dashboard grouping and transaction filtering.
- Search is case-insensitive partial matching on descriptions and categories.
  Search and active filters combine with AND logic.
- Starter-data import runs explicitly during setup, after profiling. Repeated
  imports skip existing IDs, preserve existing records, and report the skipped
  count.

## Database setup and startup

The SQLite database will be created at `src/db/database.sqlite`, relative to
the project root. Setup will create the database and schema, profile the
starter data, then explicitly import it only if profiling succeeds. The import
will report how many existing IDs were skipped.

Normal application startup will open the existing database and will not
re-import starter data. Generated database files, including SQLite journal or
write-ahead log sidecars, should be excluded from Git.

## Page design

### Dashboard

The dashboard will allow selection of a year and month, including months with
no transactions. For the selected year-month it will show:

- Monthly income: the sum of `income` transactions.
- Monthly expenses: the sum of `expense` transactions.
- Difference: income minus expenses.
- Expense totals grouped by category; income is excluded from category totals.

Selecting a category total will navigate to the transactions page while
passing the selected year-month, category, and expense type (`expense`). The
destination list will apply all three filters and allow the user to change or
clear them.

### Transactions

The transactions page will show matching transaction dates, descriptions,
categories, types, and whole-ISK amounts. It will provide search and optional
year-month, category, and type filters, plus forms to add or edit transactions
and a confirmed delete action. Search and filters will follow the shared rules
above. Empty results, loading failures, validation errors, and save/delete
failures will be communicated without discarding entered form values.

## Planned API

These endpoints describe planned behavior; they are not statements that the
features have been implemented.

### `GET /api/summary?month=2026-11`

- **Input:** Required `month` query parameter in `YYYY-MM` format.
- **Output:** JSON containing the requested month, income total, expense
  total, difference, and expense totals grouped by category. Empty months
  return zero totals and an empty category list.
- **Validation and errors:** An absent or malformed month returns `400` with a
  JSON error. A database/read failure returns `500` with a JSON error.

### `GET /api/transactions`

- **Input:** Optional `month` (`YYYY-MM`), `category`, `type` (`income` or
  `expense`), and `search` query parameters. Month, category, and type filters
  are combined with AND logic. Search is a case-insensitive partial match on
  descriptions and categories, also combined with active filters.
- **Output:** JSON containing the matching transaction records, including
  each record's ID, date, description, category, type, and amount. No matches
  return an empty list.
- **Validation and errors:** A malformed month or unsupported type returns
  `400` with a JSON error. A database/read failure returns `500` with a JSON
  error.

### `POST /api/transactions`

- **Input:** JSON with `date`, `description`, `category`, `type`, and `amount`.
  The server assigns a unique, stable ID.
- **Output:** `201` with the created transaction, including its ID.
- **Validation and errors:** Date must be a valid calendar date in
  `YYYY-MM-DD`; description and category must be trimmed, non-empty text;
  type must be `income` or `expense`; amount must be a positive whole number
  of ISK. Invalid input returns `400` with field-level JSON errors. A
  persistence failure returns `500` with a JSON error and no success response.

### `PUT /api/transactions/:id`

- **Input:** The transaction ID in the path and JSON with the editable fields:
  `date`, `description`, `category`, `type`, and `amount`. The ID is not
  editable.
- **Output:** `200` with the updated transaction, retaining its original ID.
- **Validation and errors:** The same field rules as `POST` apply. Invalid
  input returns `400` with field-level JSON errors; an unknown ID returns
  `404` with a JSON error; a persistence failure returns `500` with a JSON
  error and no success response.

### `DELETE /api/transactions/:id`

- **Input:** The transaction ID in the path; the client will request deletion
  only after user confirmation.
- **Output:** `200` with a JSON confirmation identifying the deleted ID.
- **Validation and errors:** An unknown ID returns `404` with a JSON error. A
  persistence failure returns `500` with a JSON error and no success response.

## Build order

1. Define and inspect the twenty fictional starter transactions for October
   and November 2026.
2. Implement data profiling and validation.
3. During explicit setup, create the SQLite schema at
   `src/db/database.sqlite` and import validated starter data; exclude the
   generated database files from Git.
4. Implement summary and transaction read endpoints.
5. Build the dashboard with monthly calculations and category navigation.
6. Build transaction listing, search, and filters.
7. Implement create, edit, and delete endpoints and transaction forms.
8. Verify month/category navigation, filters, persistence, and repeat imports.
9. Document setup and usage.

## Separate future upgrade

Category budgets should be designed after the first version is complete. The
upgrade will need monthly category limits and calculations for remaining
amounts, but it should not delay the core tracker.
