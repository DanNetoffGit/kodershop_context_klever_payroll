# Reports and lookups

## Journal Entry report

- Asynchronous: `POST` create → `GET` retrieve status → `GET` download (`text/csv` or
  `application/vnd.ms-excel`) → `DELETE`. **(verified)**
- The vendor dashboard offers a Summary and a Detailed variant. **(verified)**
- Use: a reconciliation **oracle** for tests — compare the Summary of a payroll with the entries we
  imported. It is **not** an import source. **(inferred)**

## Segment templates

- Build composite codes such as `5000-ENG-TOR`. They appear **only in the Journal Entry report**
  (columns Generated Expense Code / Generated Liability Code). **(verified)**
- They are never exported and never change the accounting code synced to an accounting platform.
  Journal entry rows carry the plain accounting code. **(verified)**
- Consequence: segment templates are irrelevant to the GL import.

## Line item type lookups

Lookup pages list every `type`/`subtype` that accounting code rules route: Earning, Allowance,
Deduction, Employee Benefit, Employer Benefit, Reimbursement, Employee Statutory Withholding, Employer
Statutory Withholding. Holidays is irrelevant. **(verified)**

Which side of the entry each type needs a code on:

| Line item type | Expense | Liability |
| --- | :-: | :-: |
| earning, allowance, reimbursement, employer benefit, employer statutory withholding | ✓ | ✓ |
| deduction, employee benefit, employee statutory withholding | — | ✓ |

Statutory rows **(verified by the vendor example)**; the rest **(inferred)**. The exact `type` strings
must be read from the lookup pages.

Use in Odoo: turn `invalid_expense_types` / `invalid_liability_types` into readable error text; later,
a coverage check before approval.

## Other reports

- Liability report and CRA/RQ remittance reports — useful for reconciliation.
- Payroll register, YTD, T4/RL1, year-end forms, EI/CPP balance, Forms, ROE, Vacation Pay — not GL.

Most likely **not** in the journal entries: vacation pay accrual (earned, not yet paid), Employer
Health Tax (EHT), WCB premiums. **(inferred)**
