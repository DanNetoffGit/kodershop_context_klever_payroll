# Odoo source excerpts

Unmodified copies of selected files from Odoo 19.0 Community, modules `account` and `l10n_ca`,
release 19.0.20260731. They are here so a reader without an Odoo checkout can see the real logic behind
[accounting-model.md](../accounting-model.md).

| File | What to read it for |
| --- | --- |
| [account/models/account_move.py](account/models/account_move.py) | `account.move`: states, `_post`, `button_draft`, `_check_balanced`, `_check_fiscal_lock_dates`, `_reverse_moves`, hashing, `unlink` |
| [account/models/account_move_line.py](account/models/account_move_line.py) | `account.move.line`: debit/credit/balance, currencies, analytic distribution, `reconcile`, line constraints |
| [account/models/account_account.py](account/models/account_account.py) | `account.account`: account types, per-company codes, reconcile flag |
| [account/models/account_journal.py](account/models/account_journal.py) | `account.journal`: types, default and suspense accounts, outstanding payment accounts |
| [account/models/account_partial_reconcile.py](account/models/account_partial_reconcile.py) | `account.partial.reconcile`: one matched amount between a debit and a credit line |
| [account/models/account_full_reconcile.py](account/models/account_full_reconcile.py) | `account.full.reconcile`: the group of lines fully matched together |
| [account/models/account_bank_statement_line.py](account/models/account_bank_statement_line.py) | `account.bank.statement.line`: the journal entry behind each bank statement line |
| [account/models/company.py](account/models/company.py) | `res.company`: lock dates and their checks |
| [l10n_ca/data/template/account.account-ca_2023.csv](l10n_ca/data/template/account.account-ca_2023.csv) | The Canadian chart of accounts: every account with code, name, type and reconcile flag |
| [l10n_ca/data/template/account.group-ca_2023.csv](l10n_ca/data/template/account.group-ca_2023.csv) | The account groups (code prefixes) of that chart |
| [l10n_ca/models/template_ca.py](l10n_ca/models/template_ca.py) | The template's default accounts and bank/cash code prefixes |

## License

These files are part of Odoo, Copyright Odoo S.A., licensed under the GNU Lesser General Public
License version 3 (`'license': 'LGPL-3'` in the `account` and `l10n_ca` manifests). The license texts are in
[LICENSE-LGPL-3](LICENSE-LGPL-3) and [LICENSE-GPL-3](LICENSE-GPL-3). This repository's own
[LICENSE](../../LICENSE) does not apply to this folder.

Odoo Enterprise code (license OEEL-1) is never copied into this repository; Enterprise is only
described in [enterprise.md](../enterprise.md).

## Rules

- Never edit these files. To follow a newer Odoo release, replace them with the new release's
  copies and update the release number above.
- Never add a file from an Enterprise module here.
