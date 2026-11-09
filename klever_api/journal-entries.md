# Journal entries

kleverPayroll generates journal entries per payroll. **(verified)**

Source: [Retrieve a journal entry](sources/api-reference/journal-entries/retrieve-a-journal-entry.md).

| `type` | Level | Meaning | Exported to accounting platforms |
| --- | --- | --- | --- |
| `payroll_recording` | payroll | Accrual: Dr expense / Cr liability | **yes** |
| `payroll_payment` | payroll | Payment: Dr liability / Cr bank | **yes** |
| `pay_stub_recording` | one pay stub | Accrual detail for one employee | no |

Only `payroll_recording` and `payroll_payment` are exported to QuickBooks/Xero (vendor docs).
`pay_stub_recording` is the same money split per employee: importing it as well would count the
payroll twice. **(verified)**

## Endpoints

| Call | Parameters | Notes |
| --- | --- | --- |
| `GET /journal_entries` | `payroll_id` (**required**), `type` (optional), `expand` = `journal_entry_rows`, `pay_stub`, `payroll` (comma-separated), `page` | Paginated, 15 per page |
| `GET /journal_entries/{id}` | `expand` as above | One entry |

**(verified)**

**Recommended by the vendor (meeting of 6 Nov 2026):** read each entry with
`GET /journal_entries/{id}?expand=journal_entry_rows`. Its id comes from the list call above, which is
the only way to find a payroll's entries. So: list per payroll → retrieve each recording/payment
entry by id with its rows.

### Response envelope

Both calls return each entry wrapped like every other object of the API: **(verified)**

```json
{
  "id": "<id>",
  "object": "journal_entry",
  "data": { "type": "payroll_recording", "title": "Payroll Accrual Entry", "...": "..." },
  "links": { "self": "/journal_entries/<id>" }
}
```

The fields below are inside `data`; `id` is outside it.

Vendor example (retrieve, without rows): `type` `payroll_recording`, `title` "Payroll Accrual Entry"
(fr "Écriture de provision de la paie"), `is_balanced: true`, `total: 0`,
`is_missing_line_item_accounting_codes: true`, `is_missing_default_account_payable: true`,
`posted_on: "2026-01-30T00:00:00.000000Z"`, and these invalid types — the real subtype strings:

| List | `type` | `subtype` values |
| --- | --- | --- |
| `invalid_expense_types` | `employer_statutory_withholding` | `ei`, `cpp`, `cpp_2` |
| `invalid_liability_types` | `statutory_withholding` | `cpp`, `cpp_2`, `ei`, `federal_income_tax`, `provincial_income_tax` |
| `invalid_liability_types` | `employer_statutory_withholding` | `ei`, `cpp`, `cpp_2` |

This confirms the side table in [reports and lookups](reports-and-lookups.md#line-item-type-lookups)
for statutory items: employer withholdings need both sides, employee withholdings only the liability
side. It also shows that `is_balanced` can be true on an entry with missing codes — the flags must be
checked separately.

## Entry fields

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | kleverPayroll id of the entry — our duplicate key |
| `type` | enum | See the table above |
| `title` | string | e.g. "Payroll recording" |
| `title_translations` | object | `en`, `fr` |
| `title_translated` | string | Title in the request's `Accept-Language` |
| `payroll.id` | string | The payroll it belongs to |
| `pay_stub` | object/null | Only on `pay_stub_recording` |
| `is_balanced` | boolean | Debits equal credits |
| `total` | decimal, nullable | Entry total (example shows `0`; do not use) |
| `is_missing_line_item_accounting_codes` | flag | A row has no accounting code |
| `is_missing_default_account_payable` | flag | BE has no default payroll payable code |
| `is_missing_default_bank_account` | flag | BE has no default bank code |
| `invalid_expense_types` | list of `{type, subtype}` | Line items with no expense code |
| `invalid_liability_types` | list of `{type, subtype}` | Line items with no liability code |
| `posted_on` | documented `string<date>`, example a datetime `…T00:00:00.000000Z`, nullable | "The date this journal entry was posted **to the accounting system**" — i.e. the export to Xero/QuickBooks, **not** the accounting date. Parse both shapes; do not use it as the move date |
| `integrations.quickbooks.reference`, `integrations.xero.reference` | string | Export references of those platforms; there is no Odoo slot |
| `created_at`, `updated_at` | datetime | ISO-8601 `…Z` |

**(verified)**

Documentation inconsistencies to tolerate: **(verified)**

- `is_missing_*` is documented as integer, but the examples show booleans. Treat any truthy value as
  set.
- One example shows `total: 0` on a balanced entry. Never use `total` to decide whether an entry is
  balanced or what it is worth; sum the rows.

Export readiness on the vendor side: the entry is balanced and has every accounting code. **(verified)**

## Row fields (`expand=journal_entry_rows`)

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | kleverPayroll id of the row |
| `type` | enum | `debit` or `credit` |
| `amount` | number | Amount of the row |
| `code` | string | The accounting **code** string of the routed account, e.g. `6000` |
| `description` | string | Row text |

**(verified)**

Rows carry **no accounting code id, no partner and no analytic dimension** in the row reference.
**(verified)** Tags: the row reference shows none, but the vendor's
[accounting overview](accounting-overview.md#conflict-tags-on-journal-entry-rows) says rows "carry the
tags as tracking dimensions". **Conflict, to check in the sandbox.**

Still to check in the sandbox (see [sandbox state](sandbox-2026-11-04.md)):

- What `code` contains when the accounting code has no `code` filled — the title, an empty string, or
  nothing. **(inferred: empty)**
- Whether `amount` is in dollars (`1234.56`) or cents (`123456`), and whether it can be negative.
- Whether rows of the same account are already grouped, or come one per line item.
- Whether `payroll_payment` clears all liabilities with a single bank row.
- Whether the entries exist as soon as the payroll is approved, and whether they change afterwards
  (`updated_at` after approval).
