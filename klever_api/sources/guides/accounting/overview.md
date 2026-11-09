---
source_url: https://docs.nmbr.co/guides/accounting/overview
kind: vendor-doc
captured: 2026-11-09
captured_by: Daniil Honcharenko
---

# Vendor doc: Accounting → Overview

Verbatim copy of the vendor page, as pasted from the browser. Never edited; the digest is
[../../../accounting-overview.md](../../../accounting-overview.md).

```text
Overview

Copy page

How Nmbr’s accounting features fit together: accounting codes, journal entries, and export.

Businesses that run payroll have to record it in their accounting: what they paid in wages, what they withheld for tax, and what they still owe. This usually lives in accounting software such as Xero or QuickBooks. Nmbr gives you the tools to produce those records for each payroll and export them there.

How a payroll is recorded
You don’t need an accounting background for these guides. This section is a short introduction to the accounting concepts Nmbr’s features build on.
A payroll moves money twice. When it runs, the business takes on expenses (gross wages, employer contributions) and liabilities (net pay owed to employees, taxes owed to the CRA). When those amounts are later paid out, cash leaves the bank.
Accounting records each movement as a journal entry: a few lines, each assigning an amount to an account, like wages or taxes payable. A payroll produces two journal entries:
The accrual entry records the payroll when it runs: the expenses it created, and the liabilities now owed.
The payment entry records the cash leaving the bank to settle those liabilities.
Every line in those entries is recorded against an account in the business’s Chart of Accounts: the named accounts it tracks money in, such as “Wages Expense”, “Payroll Payable”, and its bank account.
Everything in these guides exists to produce those two entries: accounting codes represent those accounts in Nmbr (typed expense, liability, or bank), rules put the right codes on every payroll line, tags control how the entries break down, segment templates compose multi-part account references for systems that need them, and integrations deliver the entries into the client’s accounting software.

Concepts
Accounting code: an account from the business’s Chart of Accounts, typed as expense, liability, or bank. See Accounting Codes & Rules.
Accounting code rule: matches line items and assigns their expense and liability codes automatically. See Accounting Codes & Rules.
Journal entry: the accrual and payment entries Nmbr generates for each payroll. See Journal Entries.
Tag and tag group: dimensions for splitting a line item’s cost across departments, projects, or locations. See Tagging.
Segment template: composes a custom, multi-part code, such as 5000-ENG-TOR, on the Journal Entry report. See Journal Entry Segment Templates.
Integration: a connection to Xero or QuickBooks Online that imports a Chart of Accounts and exports journal entries. See Accounting Integrations.

What you can set up
Combine these pieces to match how a client keeps their books. The conventional flow runs on accounting codes, matched to the client’s Chart of Accounts: they drive the journal entries and the export. When a fixed code list doesn’t fit, segmented codes offer more flexibility on the journal report.
A complete journal entry for every payroll. Accounting codes represent the business’s accounts, rules resolve an expense and a liability code for every line item, and the default bank and payroll payable codes supply the bank and payable rows. Each payroll then generates its accrual and payment entries automatically. See Accounting Codes & Rules and Journal Entries.
Costs broken down by department, project, or location. Tags split line items across the dimensions a client tracks. Journal entry rows follow the splits and carry the tags as tracking dimensions, and rules can route tagged line items to their own accounts. See Tagging.
Entries delivered into the client’s accounting software. The built-in integration connects Xero or QuickBooks Online, imports the client’s Chart of Accounts and tracking dimensions, and exports each payroll’s entries back. For any other system, you can read journal entries through the API and deliver them yourself. See Accounting Integrations.
Account references in the format another system expects. Segment templates compose a segmented reference, like 5000-ENG-TOR (a base code plus department and location), on the Journal Entry report. They are carried by the same accounting code rules that route codes, but resolve on their own when the report is built. See Journal Entry Segment Templates.
Contractor Payments
Accounting Codes & Rules
```
