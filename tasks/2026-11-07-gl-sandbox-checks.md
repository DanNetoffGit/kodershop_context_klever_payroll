---
status: open        # open | done | closed
created_by: Daniil Honcharenko
assigned_to:
done_by:
created: 2026-11-07
related_docs: [../klever_api/sandbox-2026-11-04.md, ../klever_api/journal-entries.md, ../ACCOUNTING_kleverPayroll.md]
---

# GL sync: checks in the kleverPayroll sandbox

## Task

On Business Entity "BE One" of the sandbox, with the test payroll **Period 22** (processing), answer:

1. **Settings** (Accounting Details → Settings): are the default bank and payroll payable codes set?
   Which API call returns `accounting_settings` (`GET /business_entities/{id}` or another)?
2. **Accounting codes:** is `code` filled with a number, or only `title`?
3. **Rules:** is there a catch-all, or rules covering `statutory_withholding` and
   `employer_statutory_withholding`?
4. **Entries of Period 22** via `GET /journal_entries?payroll_id=<id>&expand=journal_entry_rows`:
   - balanced, no `is_missing_*` flags, empty `invalid_*_types`?
   - what `code` holds on each row, also for a code with no number ([Q20](../ACCOUNTING_kleverPayroll.md#q20-code-when-the-accounting-code-has-no-number-sandbox));
   - amounts in dollars or cents, any negative or zero amount ([Q19](../ACCOUNTING_kleverPayroll.md#q19-amount-format-sandbox));
   - one row per line item, or grouped per account;
   - does `payroll_payment` clear every liability with a single bank row equal to `cash_requirement`;
   - what `posted_on` holds, compared with `pay_date` and `debit_date`.
5. **Report:** download the Summary Journal Entry report of Period 22 and compare it with the API rows.
6. **Tags:** do rows carry tags / tracking dimensions (try other `expand` values; read the vendor
   Tagging page)? See the [conflict](../klever_api/accounting-overview.md#conflict-tags-on-journal-entry-rows).

Paste the raw JSON of both Period 22 entries (sandbox data, no real people) under Result. Update
`klever_api/` (verified/inferred marks) and the spec where an answer changes it, in the same commit.

## Result

<!-- Filled in by the person who does the task -->

## Follow-up

<!-- Optional -->
