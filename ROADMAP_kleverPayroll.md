# kleverPayroll roadmap

The stages in order and the status of each step. Status: Not started, In progress or Done, with the
date it changed and a short note. Whoever starts or finishes a step updates its row in the same commit
as the work, or right after it.

## Accounting: GL journal entries sync

### Stage 0: Analysis

| Step | Status | Date | Note |
| --- | --- | --- | --- |
| Read the API: accounting codes, rules, journal entries, payrolls, payments, reports | Done | 4 Nov | [klever_api/](klever_api/README.md) |
| Draft design and customer questions | Done | 4 Nov | [archive](archive/2026-11-04-gl-sync-discussion.md) |
| Vendor accounting overview | Done | 7 Nov | [overview](klever_api/accounting-overview.md); opened the tags conflict |
| Context repository and implementation spec | Done | 7 Nov | [spec](specs/gl-journal-entries-sync.md) |

### Stage 1: Confirm

| Step | Status | Date | Note |
| --- | --- | --- | --- |
| Sandbox checks 1–6 (settings, codes, rows, amounts, report, tags) | Not started | | [task](tasks/2026-11-07-gl-sandbox-checks.md) |
| Hatem and Mariana answer Q1–Q18, with Dan | Not started | | [task](tasks/2026-11-07-gl-team-review.md) |
| Decisions D1–D9 moved from Proposed to Decided by their owners | Not started | | [Accounting §3](ACCOUNTING_kleverPayroll.md#3-decisions) |
| Hatem and Mariana approve the spec: implementation scheme and steps | Not started | | [spec](specs/gl-journal-entries-sync.md); Dmytro signs off technically |

### Stage 2: Version 1 — pull journal entries

Implementation follows the approved spec. Parts no open question touches (module skeleton, API helper,
accounting code sync) may start earlier.

| Step | Status | Date | Note |
| --- | --- | --- | --- |
| Implement `ks_klever_payroll_account` | Not started | | [task](tasks/2026-11-07-gl-sync-implementation.md) |
| Automated tests T1–T30 green | Not started | | spec §14 |
| Manual acceptance in the sandbox | Not started | | spec §15 |
| Demo to Hatem and Mariana, then prod | Not started | | |

### Stage 3: Phase 2

| Step | Status | Date | Note |
| --- | --- | --- | --- |
| Push the Odoo chart of accounts to kleverPayroll | Not started | | D9, spec §18 |
| Analytic distribution from tags | Not started | | Q10, depends on sandbox check 6 |
