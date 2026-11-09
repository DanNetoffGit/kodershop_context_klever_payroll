---
status: open        # open | done | closed
created_by: Daniil Honcharenko
assigned_to:        # Hatem; set to his full git name once he has committed here
done_by:
created: 2026-11-09
related_docs: [../klever_api/accounting-codes.md, ../klever_api/reports-and-lookups.md, ../klever_api/sources/api-reference/accounting-codes-and-rules-menu.md, ../ACCOUNTING_kleverPayroll.md]
---

# Accounting codes only, or accounting code rules too?

## Task

Hatem: decide how payroll line items get their accounts for the GL sync, so we know which part of the
kleverPayroll accounting API Odoo has to manage.

**Background.** kleverPayroll has two separate API resources
([menu](../klever_api/sources/api-reference/accounting-codes-and-rules-menu.md)):

- **Accounting codes** — the accounts themselves (`expense`, `liability` or `bank`), one list per
  Business Entity: list, create, retrieve, update, delete, merge.
- **Accounting code rules** — which code each payroll line item goes to: list, create, retrieve,
  update, delete, search. A rule gives a line item an expense code and a liability code, matched by
  line item type/subtype, business preset or tag, with a 12-level precedence; one catch-all rule per
  Business Entity ([accounting-codes.md](../klever_api/accounting-codes.md#accounting-code-rules)).

Codes alone are not enough: a journal entry row gets an account only through a rule, and a line item
with no matching code is reported in `invalid_expense_types` / `invalid_liability_types` of the
entry. So the question is not whether rules are used, but **who sets them and how detailed they are**.

**Options** (pick one, or describe another):

| # | Odoo manages | Rules are set | Fits when |
| --- | --- | --- | --- |
| A | Codes only (pushed from the Odoo chart of accounts) | By us or the client in the kleverPayroll web app | Rules rarely change and differ per client |
| B | Codes and rules | In Odoo, on a mapping screen (line item type → expense and liability account), pushed to kleverPayroll | Clients should manage everything from Odoo |
| C | Codes and one catch-all rule per side | Automatically by Odoo | All wages share one expense account and all withholdings one liability account |

**Please answer:**

1. Option A, B or C, and why.
2. Which line item types and subtypes a typical Canadian client books to **separate** accounts (e.g.
   regular wages, overtime, vacation pay, bonuses, taxable benefits; employee and employer CPP, CPP2,
   EI; federal and provincial income tax), and which can share one.
3. Whether tags or business presets are needed to route costs (departments, locations), or line item
   type alone is enough for version 1.
4. Anything in Canadian payroll that a rule cannot express and that the accountant would have to book
   by hand (relevant to Q11 in [ACCOUNTING_kleverPayroll.md](../ACCOUNTING_kleverPayroll.md#q11-items-outside-payroll-entries)).

Mariana may be asked to confirm the accounts in point 2; the routing logic is yours.

## Result

<!-- Filled in by Hatem -->

## Follow-up

<!-- Optional -->
