---
status: open        # open | done | closed
created_by: Daniil Honcharenko
assigned_to: Daniil Honcharenko
done_by:
created: 2026-11-07
related_docs: [../specs/gl-journal-entries-sync.md, ../ACCOUNTING_kleverPayroll.md, ../klever_api/README.md]
---

# Implement the GL journal entries sync

## Task

Build the new module `ks_klever_payroll_account` in `klever_payroll_19` exactly as described in the
[GL journal entries sync specification](../specs/gl-journal-entries-sync.md), once Hatem and Mariana
have approved it ([team task](2026-11-07-gl-team-review.md)). The spec is the task; this file tracks it.

Before starting:

1. Read the spec end to end, then [Accounting](../ACCOUNTING_kleverPayroll.md) §3–§5 and
   [klever_api/](../klever_api/README.md).
2. Read the client code it builds on: `ks.klever.payroll` (`models/klever_payroll.py`,
   `_upsert_from_klever`, `_fetch_klever_payroll`), `ks.klever.business.entity.action_sync_klever_payrolls`,
   `ks.klever.config._get_klever_api_base_url` / `_ensure_klever_token`, `models/klever_decorators.py`,
   and `ks.event.log` in `ks_klever_payroll_employee`.
3. Ask in the `Follow-up` section, not in chat, if a part of the spec is unclear or wrong — Hatem
   (payroll) or Mariana (accounting) answers, and the answer then goes into the spec for the next
   reader.

Order of work (each step reviewable on its own):

1. Module skeleton, manifest, security, menus (spec §2, §11, §12).
2. API helper (§4).
3. Accounting codes: model, sync, auto-map, mapping list with warnings (§3.1, §5).
4. BE accounting settings and the preconditions (§3.7, §6.2 steps 1–2).
5. Sync records, rows, payroll and move extensions (§3.2–§3.5).
6. The sync itself, with duplicate protection and transactions (§6, §7, §8, §9).
7. Scheduled action, run summary, notifications (§10).
8. Automated tests T1–T30 (§14).
9. Manual acceptance in the sandbox (§15).

Hard rules — never relaxed without a decision in Accounting:

- Never book an unbalanced entry and never add a balancing, rounding or suspense line.
- Never book an entry with an unmapped, empty, unknown or ambiguous code.
- Never import `pay_stub_recording`, or payrolls in `draft`/`failed`.
- Never change, reverse or re-create a move once imported.
- No vendor name in code; "kleverPayroll" in user text; no `_()`.

Done when every row of spec §16 is met.

## Result

<!-- Branch, module versions, commits, test output, the §15 comparison with the vendor's Summary
report, and every deviation from the spec with its reason. -->

## Follow-up

<!-- Questions about the spec and their answers -->
