# kleverPayroll context

Context for the kleverPayroll Odoo integration built by Kodershop: decisions, discussions,
specifications and tasks. The code lives in the `klever_payroll_19` repository. Read
[AGENTS.md](AGENTS.md) before making any change: English only, work on `main`, one task per file in
[tasks/](tasks/).

## Index

- [kleverPayroll for Odoo](MAIN_kleverPayroll.md) — **the single source of truth**: what we build, the
  two-instance architecture, decisions and open questions. Decisions go here and nowhere else, except
  accounting.
- [Accounting](ACCOUNTING_kleverPayroll.md) — everything about accounting (GL sync): the task,
  decisions, entries and open questions.
- [Roadmap](ROADMAP_kleverPayroll.md) — the stages in order and the status of each step.
- [klever_api/](klever_api/README.md) — what the kleverPayroll API and sandbox actually do, each fact
  marked verified or inferred.
- [odoo/](odoo/README.md) — how Odoo 19 accounting works, read from its source: accounts, journals,
  journal entries, posting, lock dates, reconciliation, and what Enterprise adds.
- [specs/](specs/) — implementation specifications:
  [GL journal entries sync](specs/gl-journal-entries-sync.md).
- [archive/](archive/) — earlier documents kept for history: the
  [GL sync discussion of 4 Nov](archive/2026-11-04-gl-sync-discussion.md).
- [tasks/](tasks/) — tasks between people.
