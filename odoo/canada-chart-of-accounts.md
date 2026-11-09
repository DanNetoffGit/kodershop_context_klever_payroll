# The Canadian chart of accounts in Odoo 19

What the standard Odoo chart for Canada contains for payroll, so nobody has to guess. Read from the
`l10n_ca` module, release 19.0.20260731: the account list
[account.account-ca_2023.csv](source/l10n_ca/data/template/account.account-ca_2023.csv), its groups
[account.group-ca_2023.csv](source/l10n_ca/data/template/account.group-ca_2023.csv) and the template
settings [template_ca.py](source/l10n_ca/models/template_ca.py).

## The template

| Fact | Value |
| --- | --- |
| Module | `l10n_ca` ("Canada - Accounting"), LGPL-3 |
| Template | `ca_2023` |
| Accounts | 341 (one header row in the CSV) |
| Numbering | Six digits; the first digit is the group: `1` Assets, `2` Liabilities, `3` Equity, `4` Revenue, `5` Expenses, `6` Gains/Losses, Corporate Taxes and Other Items, `7` Extraordinary Gains/Losses |
| Default payable / receivable | `221110` Accounts Payable - Retail and Wholesale Trade Accounts / `112110` |
| Default expense account | `511210` |
| New bank journals | Get an account under the prefix `11131`; cash journals under `11121` |

## Payroll accounts it already has

| Code | Name | Odoo type | Reconcilable | Fits |
| --- | --- | --- | --- | --- |
| 512100 | Labour Expenses | `expense` | no | Parent-level labour cost |
| 512110 | Salaries, Wages and Commissions | `expense` | no | Gross wages |
| 512120 | Employee Benefits | `expense` | no | Employer contributions, benefits |
| 512121 | Employee Current Benefits | `expense` | no | Employer contributions, benefits |
| 512122 | Employee Future and Post-retirement Benefits | `expense` | no | Pensions, post-retirement |
| 221203 | Wage payable and Accrued | `liability_payable` | yes | Net pay payable |
| 221205 | Bonus Payable and Accrued | `liability_payable` | yes | Bonuses owed |
| 221206 | Employee Deduction Payable | `liability_payable` | yes | Withholdings owed |
| 221210 | Owing to Governments | `liability_payable` | yes | Remittances to the CRA |
| 221207 | Items in Transit and Outstanding Cheque | `liability_payable` | yes | A clearing account for money in transit |

The "Fits" column is a reading of the names, not a decision: which account each payroll row uses is
decided by Mariana ([chart of accounts task](../tasks/2026-11-09-payroll-chart-of-accounts.md)).

## What it does not have

- No separate expense accounts for **employer CPP, CPP2 or EI**.
- No separate payable accounts for **CPP, CPP2, EI, federal or provincial income tax**: only the
  generic `221206` Employee Deduction Payable and `221210` Owing to Governments.
- No account for **vacation pay accrual, EHT or WCB**.
- No payroll bank or payroll clearing account; bank accounts are created with each bank journal.

`231000`–`235000` (GST, PST/QST, HST to pay) and `241000` (Future Income Tax) are sales tax and
corporate income tax accounts, not payroll withholdings.

## Things to know about the types

- Every `liability_payable` account above is reconcilable; Odoo requires that for payable and
  receivable accounts ([accounting-model.md §2](accounting-model.md#2-accounts--accountaccount)).
- Items on `liability_payable` accounts are what the Aged Payable and Partner Ledger reports list, by
  partner, so payroll liabilities booked there would show up in them. How a line without a partner is
  shown was not checked.
- The expense accounts are not reconcilable, which is normal for expenses.

## Adding accounts

A client company's chart is an ordinary list of `account.account` records, created from the template
when the company's accounting is set up. Payroll accounts the template lacks can be added to it like
any other account, within the numbering above.
