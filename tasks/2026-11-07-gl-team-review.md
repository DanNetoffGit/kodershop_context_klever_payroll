---
status: open        # open | done | closed
created_by: Daniil Honcharenko
assigned_to:        # Mariana and Hatem; set to their full git names once they have committed here
done_by:
created: 2026-11-07
related_docs: [../ACCOUNTING_kleverPayroll.md, ../specs/gl-journal-entries-sync.md, ../klever_api/README.md]
---

# GL sync: decide and shape the implementation scheme

## Task

Dan has filled this repository with the context of the kleverPayroll → Odoo Accounting import: what
the kleverPayroll API gives us ([klever_api/](../klever_api/README.md)), proposed decisions and
questions ([Accounting](../ACCOUNTING_kleverPayroll.md)), and a first draft of the
[spec](../specs/gl-journal-entries-sync.md).

Everything there is a **draft**. The goal of this task: Hatem and Mariana turn it into decisions and an
implementation scheme with steps that matches how they see payroll and accounting, clear enough that
Dan can build it without guessing. Dan joins to explain the system and what is possible; ask him
anything in the Follow-up section.

**Mariana (accounting) — you own:**

1. [Accounting](../ACCOUNTING_kleverPayroll.md) §2–§4: the flow, decisions D3–D9 and the two example
   entries (accrual and payment). Correct them and mark each decision Decided, or rewrite it.
2. Your questions in the [owner table](../ACCOUNTING_kleverPayroll.md#5-open-questions), in particular:
   - Is one draft journal entry per kleverPayroll entry the right shape for the books (Q1)?
   - Which account should the payroll bank debit go through, so it reconciles with the bank statement
     (Q6, spec §7.4)?
   - Accounting dates: pay date for the accrual, debit date for the payment (Q18)?
   - Refuse an unbalanced or incompletely mapped entry, or book it to a suspense account (D4)?
3. Spec §5 (mapping), §7 (how the Odoo entry is built), §8 (errors): rewrite whatever an accountant
   would want differently.

**Hatem (payroll) — you own:**

1. [journal-entries.md](../klever_api/journal-entries.md) and
   [reports-and-lookups.md](../klever_api/reports-and-lookups.md#line-item-type-lookups): are the line
   item types and their expense/liability sides right for Canadian payroll?
2. Your questions: historical, off-cycle and correction payrolls (Q4, Q5), vacation pay, EHT, WCB
   (Q11), contractors (Q12), failed payrolls (Q16, with Mariana).
3. Decisions D1 and D2: which entries and which payroll statuses are imported.

**Together:** read the spec's implementation order (task
[Implement the GL sync](2026-11-07-gl-sync-implementation.md)) and change the steps to the order you
want to see working first.

Write decisions straight into Accounting and the spec (or ask your agent to), and note here what
changed. When both of you consider the spec right, write "approved" under Result with the date; Dmytro
then signs off technically.

## Result

<!-- Mariana: -->

<!-- Hatem: -->

## Follow-up

<!-- Questions to Dan and his answers -->
