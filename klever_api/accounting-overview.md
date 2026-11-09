# Accounting overview (vendor docs)

Summary of the vendor's "Accounting → Overview" page, received from Dan on 7 Nov 2026. Source:
[sources/guides/accounting/overview.md](sources/guides/accounting/overview.md). Everything here
is **(verified)** as vendor documentation; where it conflicts with another page, the conflict is
named.

## How a payroll is recorded

A payroll moves money twice:

1. When it runs, the business takes on **expenses** (gross wages, employer contributions) and
   **liabilities** (net pay owed to employees, taxes owed to the CRA).
2. When those amounts are paid out, cash leaves the bank.

So each payroll produces **two journal entries**:

| Vendor wording | API `type` | Records |
| --- | --- | --- |
| Accrual entry | `payroll_recording` | Expenses created and liabilities now owed |
| Payment entry | `payroll_payment` | Cash leaving the bank to settle the liabilities |

Every line is recorded against an account of the business's Chart of Accounts ("Wages Expense",
"Payroll Payable", the bank account).

## Concepts

| Concept | Meaning | Our page |
| --- | --- | --- |
| Accounting code | An account of the Chart of Accounts, typed `expense`, `liability` or `bank` | [accounting codes](accounting-codes.md) |
| Accounting code rule | Matches line items and assigns their expense and liability codes | [accounting codes](accounting-codes.md#accounting-code-rules) |
| Journal entry | The accrual and payment entries generated for each payroll | [journal entries](journal-entries.md) |
| Tag, tag group | Dimensions to split a line item's cost across departments, projects or locations | — (vendor "Tagging" page, not read yet) |
| Segment template | Composes a multi-part code such as `5000-ENG-TOR` on the Journal Entry report | [reports](reports-and-lookups.md#segment-templates) |
| Integration | Xero or QuickBooks Online: imports the Chart of Accounts and tracking dimensions, exports journal entries | — |

## What it confirms for the Odoo sync

- A complete entry needs: accounting codes for the business's accounts, rules resolving an expense
  and a liability code for every line item, and the **default bank and payroll payable codes**, which
  supply the bank and payable rows.
- Each payroll then generates its accrual and payment entries **automatically**.
- **"For any other system, you can read journal entries through the API and deliver them
  yourself."** — exactly what the Odoo sync does; there is no vendor-side Odoo integration.
- Segment templates resolve only when the report is built — confirms they are irrelevant to the
  import.

## Conflict: tags on journal entry rows

The overview says: *"Journal entry rows follow the splits and carry the tags as tracking dimensions,
and rules can route tagged line items to their own accounts."*

The journal entry row schema we read on 4 Nov lists only `id`, `type`, `amount`, `code`,
`description` — no tags ([journal entries](journal-entries.md#row-fields-expandjournal_entry_rows)).

Possible explanations: the tags come through another `expand` value, a field the row reference page
omits, or only in the Xero/QuickBooks export. **To check** in the sandbox and on the vendor's
Tagging page ([sandbox checks task](../tasks/2026-11-07-gl-sandbox-checks.md), check 6). If rows do
carry tags, analytic distribution becomes possible in Odoo ([Q10](../ACCOUNTING_kleverPayroll.md#q10-departments-and-analytics)),
and a payroll split by tags gives more rows per account than one per line item.
