# Odoo accounting reference

How Odoo 19 accounting works, for anyone working on kleverPayroll without an Odoo checkout. Written
from the Odoo 19.0 source, release 19.0.20260731: every statement here was read in the code, and the
file it comes from is named. Where the code was not read, the text says so.

| Document | Contents |
| --- | --- |
| [accounting-model.md](accounting-model.md) | The accounting data model: accounts, journals, journal entries and items, posting, lock dates, reconciliation, the bank side, and what this means for the GL sync |
| [canada-chart-of-accounts.md](canada-chart-of-accounts.md) | The standard Canadian chart (`l10n_ca`): which payroll accounts it has and which it lacks |
| [modules.md](modules.md) | Which modules make up Odoo accounting (Community and Enterprise) and how they depend on each other |
| [enterprise.md](enterprise.md) | What the Enterprise accounting modules add on top of Community, described without their code |
| [source/](source/README.md) | Unmodified Community source files of the `account` and `l10n_ca` modules, to read the real logic |

How the GL sync uses these models is decided in
[ACCOUNTING_kleverPayroll.md](../ACCOUNTING_kleverPayroll.md) and specified in
[specs/gl-journal-entries-sync.md](../specs/gl-journal-entries-sync.md). This folder only describes
Odoo; it makes no decision.
