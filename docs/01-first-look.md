# First look at the starter data

This document records the initial observations about the fictional transaction
dataset before database import.

## Review outcome

The inspected dataset in [`data/transactions.json`](../data/transactions.json)
contains exactly 20 fictional transactions:

| Year-month | Total | Income | Expense |
|---|---:|---:|---:|
| 2026-10 | 10 | 2 | 8 |
| 2026-11 | 10 | 2 | 8 |
| **Total** | **20** | **4** | **16** |

The nine categories represented are Dining (2), Entertainment (2), Freelance
(2), Groceries (4), Healthcare (2), Housing (2), Salary (2), Transport (2),
and Utilities (2).

All records contain the six required fields: `id`, `date`, `description`,
`category`, `type`, and `amount`. No required values are missing. IDs are
unique and use the `txn-NNN` format. All dates are valid calendar dates in
`YYYY-MM-DD` format and fall within October or November 2026. Dates cover
multiple days in each month. Descriptions and categories are readable,
non-empty, and have no surrounding whitespace. All types are `income` or
`expense`, and all amounts are positive whole numbers of ISK.
