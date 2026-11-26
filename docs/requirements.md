# Money Wise requirements

Money Wise is a small personal finance tracker for recording income and
expenses in Icelandic krónur (ISK). These requirements define the first
version. Category budgets, accounts, recurring transactions, authentication,
exports, and currencies other than ISK are out of scope.

## Missing or unclear requirements found

- The previous requirements did not define how income, expenses, difference,
  and category totals are calculated.
- Month filtering did not say that the year and month must be used together.
- Search matching, combined-filter behavior, and clearing filters were not
  precise enough to test.
- The effect of editing a transaction's month, category, type, or amount was
  not defined.
- Validation rules for IDs, dates, text, transaction types, and amounts were
  incomplete.
- SQLite persistence and repeatable starter-data import were not defined.
- Empty states, loading and saving failures, mobile use, and keyboard access
  were missing.

The requirements below add these details without adding a new product area.

## Functional requirements

### Dashboard and monthly calculations

F1. The dashboard lets the user select a year and month. It must support the
starter-data months and months with no transactions.

F2. Income for the selected year-month is the sum of all transactions of type
`income` in that year and month.

F3. Expenses for the selected year-month are the sum of all transactions of
type `expense` in that year and month.

F4. Difference for the selected year-month is income minus expenses. It may be
negative or zero.

F5. The dashboard groups expense totals by category for the selected
year-month. Income is not included in category spending.

F6. A category total can be selected. This opens the transaction view with
the selected year-month and category filters already applied.

F7. The destination view shows the active filters and contains only expense
transactions in that category and year-month. The user can change or clear
those filters.

### Transaction management

F8. The transaction view displays each matching transaction's date,
description, category, type, and amount in ISK.

F9. The user can add a transaction with a valid date, description, category,
type, and amount.

F10. The user can edit an existing transaction without changing its ID.

F11. The user can delete a transaction only after clear confirmation.

F12. Adding, editing, and deleting refreshes the transaction list and all
affected dashboard totals. An edit removes the transaction from its old
year-month/category/type totals and applies it to its new values.

### Search and filters

F13. Search is a case-insensitive partial match against the description and
category only.

F14. The user can filter by year-month, category, and transaction type
(`income` or `expense`).

F15. Search and all selected filters use AND logic. Every displayed record
must satisfy every active condition.

F16. The user can clear search and filters with one clearly labeled action.
Clearing restores the unfiltered transaction list.

F17. If no records match, the view displays a clear no-results message and
keeps the entered search and filter values visible.

### Starter data and profiling

F18. The project includes twenty fictional starter transactions across October
and November 2026, including both income and expenses.

F19. Starter data is profiled before SQLite import. Profiling checks record
count, year-month coverage, income and expense counts, category counts,
missing required values, duplicate IDs, allowed types, valid dates, and
positive whole-number amounts.

F20. A profiling failure is reported and prevents the invalid starter data
from being imported.

## Data requirements

D1. Every transaction has a unique, stable ID. IDs cannot be blank, duplicated,
reused, or changed by editing.

D2. Every transaction has a valid calendar date stored and displayed as
`YYYY-MM-DD`. Year and month are derived and compared together; October in
one year must never be combined with October in another year.

D3. Descriptions and categories are required and must contain non-whitespace
text. Input is trimmed before validation and storage. Categories accept
trimmed, non-empty free text; there is no fixed category vocabulary.

D4. The only allowed transaction types are `income` and `expense`.

D5. Amounts are positive whole numbers of ISK. Zero, negative, fractional,
non-numeric, and other-currency amounts are invalid. Values are displayed as
whole ISK amounts.

D6. Transactions are stored persistently in the SQLite database at
`src/db/database.sqlite`, relative to the project root. Transactions added,
edited, or deleted remain changed after the application is restarted.

D7. Starter-data import is repeatable and must not create duplicate records
when an existing starter transaction ID is encountered. Existing records are
left unchanged and the import reports how many records were skipped.

D8. Starter data must pass all profiling checks before its first import.
Invalid records, duplicate IDs, and missing required values are reported with
enough detail to correct the source data.

## Usability and reliability requirements

U1. An empty year-month shows zero income, zero expenses, and zero difference,
with a clear message that there are no transactions for that month.

U2. A loading state is visible while transactions or dashboard data are being
loaded. A load failure shows an understandable error and a retry action.

U3. A save or delete failure shows an understandable error, does not claim
success, and does not discard entered form values. A save action must not
create accidental duplicate transactions from repeated clicks.

U4. Invalid or incomplete forms cannot be saved. Field-level messages identify
the missing or invalid date, description, category, type, or amount, and
entered values remain available for correction.

U5. Delete confirmation identifies the transaction being deleted and offers a
clear way to cancel.

U6. At approximately 320px wide, controls wrap or resize without clipping
text or actions. Transaction details remain usable, including by horizontal
scrolling where a table needs it.

U7. All core actions work with a keyboard: navigation, category links, search,
filters, clearing filters, forms, save, cancel, and delete confirmation.
Focus order is logical and the focused control is visible.

## User stories and pass/fail acceptance checks

### Review a month

As a user, I want to understand one year-month at a time.

- **Pass:** Selecting a year-month shows income, expenses, difference, category
  totals, and transactions for that year-month only.
- **Pass:** Difference equals income minus expenses, including when the result
  is negative.
- **Pass:** An empty year-month shows three zero totals and the empty-month
  message.
- **Fail:** A transaction from another year or month affects the result.

### Follow a category total

As a user, I want to see the transactions behind a category total.

- **Pass:** Selecting a dashboard category opens transactions with both the
  selected year-month and category active.
- **Pass:** The resulting list contains only expense transactions in that
  category and year-month.
- **Fail:** The user must reselect either filter or sees unrelated records.

### Find transactions

As a user, I want to search and narrow my transactions.

- **Pass:** Search finds case-insensitive partial matches in descriptions and
  categories.
- **Pass:** Search, year-month, category, and type filters combine so every
  result satisfies all active conditions.
- **Pass:** No matches show the no-results message, and clearing filters
  restores the expected broader list.
- **Fail:** Results match only some active conditions or clear the user's input.

### Manage a transaction

As a user, I want to add, change, and remove transactions.

- **Pass:** A valid transaction saves once and appears with the correct values.
- **Pass:** Editing a month, category, type, or amount updates both the old
  and new affected totals.
- **Pass:** Deleting after confirmation removes the record from the list and
  every affected total.
- **Pass:** Deletion can be cancelled without changing data.
- **Fail:** Invalid data is saved, an ID changes during editing, or a stale
  total remains.

### Keep data safe

As a user, I want my data to remain available and starter data to be safe to
reload.

- **Pass:** Added, edited, and deleted transactions remain correct after a
  restart.
- **Pass:** Repeating starter-data import creates no duplicate IDs or records
  and reports skipped existing IDs.
- **Pass:** Profiling reports invalid starter data and blocks its import.
- **Fail:** Restarting loses a change or re-import duplicates a starter record.

### Use the tracker accessibly

As a user, I want the tracker to work in common constrained situations.

- **Pass:** Invalid fields show understandable messages and preserve input.
- **Pass:** Loading and saving failures are visible, do not claim success, and
  offer retry where appropriate.
- **Pass:** Core flows work by keyboard and remain usable at approximately
  320px wide.
- **Fail:** A failure is silent, entered data is lost, or a core control cannot
  be reached or operated by keyboard.

## Resolved decisions

The following decisions are confirmed and apply consistently to the dashboard,
transactions page, and data setup:

1. The twenty fictional starter transactions cover October and November 2026.
2. Categories accept trimmed, non-empty free text; they are not restricted to
   a predefined list.
3. Dates are stored and displayed as `YYYY-MM-DD`. Month filtering compares
   both the year and month.
4. Search performs case-insensitive partial matching on descriptions and
   categories only.
5. Repeated starter-data imports skip existing IDs, preserve those records
   unchanged, and report the skipped count. Starter data must pass profiling
   before import.
6. The SQLite database is located at `src/db/database.sqlite`, relative to
   the project root.
