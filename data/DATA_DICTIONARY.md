# Transaction data dictionary

The starter dataset is a JSON array of transaction objects. Each object must
contain exactly the six fields below.

| Field | Meaning | Data type | Required | Validation |
|---|---|---|---|---|
| `id` | Stable identifier for one transaction. | String | Yes | Must be non-empty and unique across all records. Starter IDs use the `txn-NNN` format (for example, `txn-001`). IDs are not changed when a transaction is edited. |
| `date` | Calendar date on which the transaction occurred. | String | Yes | Must be a real calendar date formatted as `YYYY-MM-DD`. |
| `description` | Short explanation of the transaction. | String | Yes | Must contain non-whitespace text. Trim surrounding whitespace before validation and storage. |
| `category` | Free-text grouping for the transaction. | String | Yes | Must contain non-whitespace text. Trim surrounding whitespace before validation and storage; no fixed vocabulary is required. |
| `type` | Whether the transaction represents money received or spent. | String | Yes | Must be exactly `income` or `expense`. |
| `amount` | Transaction value in Icelandic krónur (ISK). | Integer | Yes | Must be a positive whole number. Zero, negative, fractional, non-numeric, and non-ISK values are invalid. |

Descriptions and categories are stored as trimmed, non-empty text. Dates use
the same `YYYY-MM-DD` representation in storage and display.
