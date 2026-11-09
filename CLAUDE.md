# CLAUDE.md

All repository rules are in [AGENTS.md](AGENTS.md). Read and follow it before making any change.

Key rules:
- **English only.** Everything you write here — docs, tasks, results, file names, commit messages —
  must be in English, even if the user talks to you in another language.
- The product is written **kleverPayroll**, everywhere.
- At the start of every session: `git pull`, then check `tasks/` for open tasks assigned to the user
  and done ones waiting for their review, and tell the user (see AGENTS.md).
- `MAIN_kleverPayroll.md` is the single source of truth: decisions and design changes are written
  there and nowhere else. Accounting is the exception: everything about it goes into
  `ACCOUNTING_kleverPayroll.md`. Track stages in `ROADMAP_kleverPayroll.md`.
- Specs in `specs/` never make decisions; they link to them, and state their default for each open one.
- Mark every API statement **(verified)** or **(inferred)**.
- Work on `main` only: never create or push any other branch. If pushing to `main` is not possible,
  stop and tell the user instead of pushing elsewhere.
- `git pull --rebase` before starting and again right before pushing.
- On a merge conflict, stop and show it to the user; never resolve it silently (see AGENTS.md).
- Commit under the user's own git identity; never force-push `main`.
- Never add `Co-Authored-By` or any other AI attribution lines to commits or pull requests.
- Never commit secrets or personal data.
