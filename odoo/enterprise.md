# What Odoo Enterprise adds to accounting

A description of the Enterprise accounting modules in 19.0, from their manifests and file lists. Their
code is licensed OEEL-1 and is not copied into this repository.

## `account_accountant` — the accountant's tools

| Feature | What it does | Where (model or wizard) |
| --- | --- | --- |
| Bank reconciliation | The screen where each bank statement line is matched with open journal items; matching rewrites the statement line's suspense line into the chosen counterpart (see [accounting-model.md §7](accounting-model.md#7-the-bank-side)) | `account.bank.statement.line` extension, `set_line_bank_statement_line` |
| Reconciliation models | Rules that propose or apply counterparts automatically | `account.reconcile.model` extension |
| Reconcile wizards | Reconcile selected journal items manually, or in bulk by account and partner | `account.reconcile.wizard`, `account.auto.reconcile.wizard` |
| Lock dates | A wizard to set the lock dates of [accounting-model.md §5](accounting-model.md#5-lock-dates), with exceptions | `account.change.lock.date` |
| Fiscal years | Fiscal years of any length instead of the company's fixed last day | `account.fiscal.year` |
| Deferred revenue and expense | Spreads an invoice line over several periods with generated entries | `account.move` extension |

## `account_reports` — financial reports

Balance Sheet, Profit and Loss, Executive Summary, General Ledger, Trial Balance, Journal Report,
Partner Ledger, Aged Receivable and Payable, Cash Flow, Tax Report and tax returns, Multicurrency
Revaluation, Bank Reconciliation report, Deferred reports, Analytic report and customer statements,
with PDF and XLSX export. The reports read posted journal items only (`parent_state = 'posted'`)
unless the user switches on the option to include draft entries, so an entry the GL sync leaves in
draft is missing from them by default.

## `accountant` — the Accounting application

The installable "Accounting" app. It brings the two modules above, the full Accounting menu and the
access rights for advisers.

## What this means for kleverPayroll

- Everything the GL sync creates — `account.move` and `account.move.line` — is Community. The sync
  works the same with or without Enterprise.
- Reconciling the payroll payment with the bank statement line is done in the Enterprise bank
  reconciliation screen. Without Enterprise the statement line stays on the suspense account until
  it is matched by other means.
- Odoo Enterprise `hr_payroll_account` books payslips into accounting. It must not run next to the
  GL sync: see decision D6 in [ACCOUNTING_kleverPayroll.md](../ACCOUNTING_kleverPayroll.md#3-decisions).
