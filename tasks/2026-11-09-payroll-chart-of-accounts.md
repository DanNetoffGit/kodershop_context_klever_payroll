---
status: open        # open | done | closed
created_by: Daniil Honcharenko
assigned_to:        # Mariana; set to her full git name once she has committed here
done_by:
created: 2026-11-09
related_docs: [../ACCOUNTING_kleverPayroll.md, ../odoo/accounting-model.md, ../klever_api/accounting-codes.md, 2026-11-09-codes-only-or-rules.md]
---

# Chart of accounts for payroll: which accounts, and where they come from

## Task

Mariana: decide which accounts payroll entries need in Odoo, and whether the standard Odoo chart of
accounts for Canada is enough or a payroll-specific set has to be added.

**Background.** Every row of a payroll journal entry lands on an Odoo account
([ACCOUNTING §4](../ACCOUNTING_kleverPayroll.md#4-entries)), and the same accounts become Nmbr
accounting codes, typed `expense`, `liability` or `bank`
([accounting-codes.md](../klever_api/accounting-codes.md)). Today the entries in §4 use working names
only.

The standard Odoo 19 chart for Canada (`l10n_ca`, template `ca_2023`, 341 accounts) has few payroll
accounts — full list and what is missing in
[odoo/canada-chart-of-accounts.md](../odoo/canada-chart-of-accounts.md):

| Code | Name | Odoo type |
| --- | --- | --- |
| 512110 | Salaries, Wages and Commissions | expense |
| 512120 | Employee Benefits | expense |
| 512121 | Employee Current Benefits | expense |
| 512122 | Employee Future and Post-retirement Benefits | expense |
| 221203 | Wage payable and Accrued | liability_payable |
| 221206 | Employee Deduction Payable | liability_payable |

It has no separate accounts for employer CPP / EI expense, nor for CPP, EI or income tax payable to
the CRA. Both payable accounts are of type `liability_payable`, which Odoo always makes reconcilable
and lists in the Aged Payable and Partner Ledger reports
([odoo/accounting-model.md §2](../odoo/accounting-model.md#2-accounts--accountaccount)).

**Please answer:**

1. The list of accounts payroll needs: code, name, Odoo type (`expense`, `liability_current`,
   `liability_payable`, `asset_cash`, …) and whether it must allow reconciliation. At least: gross
   wages, employer CPP / CPP2 / EI expense, income tax, CPP / CPP2 / EI payable, net pay payable, and
   the bank or payroll clearing account the Nmbr payment is debited from.
2. Can the standard `l10n_ca` accounts above be used as they are, or should payroll get its own
   accounts (added to the standard chart, or a separate template)?
3. Where the accounts come from for a real client: their existing chart in Odoo, their accountant, or
   a default set we ship with the module and the client adjusts.
4. Which items stay outside these entries and are booked by the accountant (vacation accrual, EHT,
   WCB — Q11 in [ACCOUNTING](../ACCOUNTING_kleverPayroll.md#q11-items-outside-payroll-entries)).

How detailed the accounts are depends on how line items are routed to them: see the
[codes or rules task](2026-11-09-codes-only-or-rules.md) for Hatem.

## Result

<!-- Filled in by Mariana -->

## Follow-up

<!-- Optional -->
