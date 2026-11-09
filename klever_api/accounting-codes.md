# Accounting codes and rules

## Accounting code

One account of the chart of accounts, defined per Business Entity (BE). **(verified)**

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | kleverPayroll id of the accounting code |
| `business_entity_id` | string | The BE it belongs to |
| `title` | string | Account name, e.g. "Wages Expense" |
| `type` | enum | `expense`, `liability` or `bank` |
| `code` | string, optional | Account number, e.g. `6000` |
| `description` | string, optional | Free text |

- Accounting codes can be imported from a connected accounting platform (QuickBooks, Xero). There is
  no Odoo integration on the vendor side. **(verified)**
- Duplicates are merged with `POST /accounting_codes/{id}/merge`. **(verified)**
- The create/update/list endpoints exist (`/accounting_codes`), but their full reference — in
  particular whether a non-QuickBooks/Xero external identifier can be stored — is **still to read**.
- Deprecated and not to be used: the "GL Codes" entity and `fallback_mappings`. **(verified)**

## Accounting code rules

Rules route each payroll line item to an **expense** accounting code and a **liability** accounting
code. **(verified)**

- Matchers: line item `type` (plus a set of `subtypes`), `business_preset_id`, `tag_id`,
  `tag_group_id`.
- 12-level precedence, most specific first. The expense side and the liability side are resolved
  independently.
- One catch-all rule (no matchers) per BE.
- Rules are re-evaluated on every recalculation of a **draft** payroll. **An approved payroll keeps
  the codes resolved at approval.**

## Accounting settings of the Business Entity

`accounting_settings` on the BE: **(verified)**

| Field | Meaning |
| --- | --- |
| `default_bank_accounting_code` | Bank account the payroll is paid from |
| `default_payroll_payable_accounting_code` | Net pay payable |
| `primary_tag_group` | Tag group used as the main dimension |
| `resolve_journal_entry_stat_withholdings` | How statutory withholdings are shown in entries |
| `block_approval_on_incomplete_journal_entries` | Payroll cannot be approved while its entries are incomplete |
| `block_approval_on_missing_export_identifiers` | Payroll cannot be approved while codes lack an export identifier |

- Without the default bank and payroll payable codes, entries are still generated but are
  incomplete and flagged (see [journal entries](journal-entries.md)). **(verified)**
- The exact endpoint that returns `accounting_settings` (inside `GET /business_entities/{id}` or a
  separate one) is **still to confirm**.

## What "Accounting Information properly configured" means

Derived from the above: **(inferred)**

1. The default bank and payroll payable codes are set on the BE.
2. Rules, or the catch-all, cover every line item type on both sides it needs (see the side table in
   [reports and lookups](reports-and-lookups.md#line-item-type-lookups)).
3. The resulting journal entries carry no validation flags.

## Pay rates and overtime rates

The Odoo client already passes `expense_accounting_code_id` / `liability_accounting_code_id` through
on pay rates and overtime rates (raw `Char` fields on `ks.klever.pay.rate` and
`ks.klever.overtime.rate`). These are rule-level overrides on the vendor side; the GL import does not
need them. **(verified in our code)**
