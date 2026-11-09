# Agent & Contributor Rules

Shared rules for people and AI agents working on the kleverPayroll Odoo integration: analysis, design
decisions, specifications and tasks, topic after topic. The code itself lives in the
`klever_payroll_19` repository; this repository holds the context around it, for the whole life of the
project.

Read this file before making any change in the repository.

## Language: English only

**Everything in this repository MUST be written in English — no exceptions.**
This covers documents, tasks, results, follow-ups, file and folder names,
commit messages, code samples, comments and front matter values.
If the source material or the instruction you received is in another language,
translate it to English before committing.

## Naming

- The product is written **kleverPayroll** — one word, lowercase `k`, capital `P` — everywhere,
  even at the start of a sentence. Never "Klever Payroll", "KleverPayroll" or "kleverPAYROLL".
- The vendor behind kleverPayroll is **Nmbr**, and this repository names it openly: the Nmbr API,
  the Nmbr sandbox, the Nmbr docs (`docs.nmbr.co`). Older documents that say "kleverPayroll API" mean
  the same Nmbr API.
- For the code repository `klever_payroll_19` only, not for this one: no vendor name in any module,
  model, field, method, class, XML id, string or file name. Code samples in a specification follow
  that rule.

## Repository layout

```
MAIN_kleverPayroll.md        # THE single source of truth: what we build, decisions, open questions
ACCOUNTING_kleverPayroll.md  # Everything about accounting (GL sync): decisions, entries, questions
ROADMAP_kleverPayroll.md     # Stages in order and the status of each step
README.md                    # Index of this repository
klever_api/                  # Verified facts about the kleverPayroll API and the sandbox: source material
  sources/                   # Vendor doc pages copied verbatim, one file per page; never edited
  samples/                   # Real sandbox requests and responses, one file per call
odoo/                        # How Odoo accounting works, read from the Odoo source: reference only
  source/                    # Unmodified Odoo Community (LGPL-3) files; never edited, never Enterprise
specs/                       # Implementation specifications, one file per feature
archive/                     # Earlier documents kept for history; never edited
images/                      # Screenshots, scans and photos only
tasks/                       # Tasks between people, one file per task
  _TEMPLATE.md
AGENTS.md                    # This file
CLAUDE.md                    # Pointer to this file for Claude agents
.claude/settings.json        # Claude Code settings shared by everyone (AI attribution off)
```

- `MAIN_kleverPayroll.md` — the only place where product decisions and design changes are written. A
  decision goes in only when its owner has made it: Hatem for payroll, Mariana for accounting, Dan
  for architecture and the rest of the product (see the team in `MAIN_kleverPayroll.md`). Drafts by Dan
  or an agent are marked *Proposed*. Accounting is the one exception: see
  below.
- `ACCOUNTING_kleverPayroll.md` — the only place for accounting decisions, agreed entries and open
  accounting questions. Other documents link to it instead of repeating it.
- A new large topic gets the same treatment: its own `<TOPIC>_kleverPayroll.md` (e.g.
  `TIMESHEETS_kleverPayroll.md`), a row in the Topics table of `MAIN_kleverPayroll.md`, a section in the
  roadmap and its specs in `specs/`. Material from finished topics stays where it is.
- `ROADMAP_kleverPayroll.md` — update a step's row (status, date, note) when its work starts or finishes.
- `klever_api/` — what the kleverPayroll API and its sandbox actually do. Every statement is marked
  **(verified)** — read in the vendor docs or seen in the sandbox — or **(inferred)** — reasoning
  still to be confirmed. Only verified facts may be used as a hard requirement in a spec.
  A verified statement links its source: a vendor page copied verbatim into `klever_api/sources/`
  (mirroring the page's path on docs.nmbr.co, with its URL and capture date) or a sandbox call
  recorded in `klever_api/samples/`. New vendor material is first saved there as is, then summarised
  in the digest files; a source file is never edited afterwards.
- `odoo/` — how Odoo itself works, for readers without an Odoo checkout. Every statement is read in
  the Odoo source and names its file; it describes Odoo and decides nothing. `odoo/source/` holds
  unmodified Odoo Community files under their own LGPL-3 license; Odoo Enterprise code (OEEL-1) is
  never copied into this repository, only described.
- `specs/` — how a feature is built: models, fields, algorithms, views, errors, tests and acceptance.
  A spec cites the decisions it depends on (link to `ACCOUNTING_kleverPayroll.md` or
  `MAIN_kleverPayroll.md`) and never makes a decision of its own. Where a decision is still open, the
  spec states the **default** it is built with and links the open question, so work can start
  without waiting.
- `tasks/` — one file per task. Never put several unrelated tasks into one file. A task that asks for
  a spec to be implemented links to the spec instead of copying it.

## Git workflow

Work directly on `main`. **Only `main` exists in this repository: never create, commit to or push any
other branch** (no feature branches, no `claude/...` or other tool-generated branches). Everything is
read from `main`, so work left on another branch is invisible to everyone else.

If your environment does not let you push to `main` (for example a session that may only push to its
own branch), do not push to another branch as a workaround. Stop and tell the user that the change
could not be published to `main`.

Someone else may push while you are working, so always take their changes before you publish yours:

1. Before starting work: `git pull --rebase --autostash origin main`
2. Make changes and commit with a short, clear message (stage exact files, never `git add .`).
3. Right before pushing: `git pull --rebase origin main` to pick up anything pushed in the meantime.
4. Push: `git push origin main`.
5. If the push is rejected because `main` moved again, repeat step 3, then push.

### Merge conflicts

Conflicts should be rare, but when `git pull --rebase` stops on one, an agent must not resolve it silently.

1. Stop and show the user the conflicting files and both versions of each conflicting part
   ("ours" = the local change, "theirs" = what is already on `main`).
2. Ask the user how to resolve it: keep theirs, keep ours, or let the agent merge both.
3. If the agent merges, show the merged result and wait for the user's approval.
4. Then `git add <files>`, `git rebase --continue`, and push.
5. If the user wants to give up: `git rebase --abort` restores the state before the pull.

### Commit author

- Commits are authored by the person doing the work, never by an AI tool.
- Before the first commit, check `git config user.name` and `git config user.email`. Treat them as
  missing when empty or when they belong to an AI tool (e.g. `Claude`, `noreply@anthropic.com`).
- When missing: if this person already has commits here (`git log --format='%an <%ae>'`), reuse that
  name and email exactly. Otherwise ask the user for their full name and email. Never guess them from
  a first name, an account or a task file.
- Set them for this repository only with `git config user.name "..."` and
  `git config user.email "..."` (no `--global`), and show the result to the user before committing.
- The full `user.name` is also the name used in `created_by` and `assigned_to` of tasks.
- Do not add `Co-Authored-By`, "Generated with" or any other AI attribution lines to commits or pull
  requests. For Claude Code this is also switched off in `.claude/settings.json`.

Rules:
- Never use `git push --force` on `main`.
- Never rewrite or delete someone else's commits.
- Keep commits small: one task or one logical doc change per commit.

## Tasks

`tasks/` is how people ask each other for something: information, a decision, a check in the
sandbox, or an implementation. One person writes a task, the assignee's agent notices it and helps
its user do it.

### Check for tasks at the start of every session

Right after the first `git pull`, before any other work, an agent checks the tasks and tells its user
what is waiting, even if the user asked about something else:

```bash
grep -l "^status: open" tasks/*.md | grep -v _TEMPLATE   # waiting to be done
grep -l "^status: done" tasks/*.md | grep -v _TEMPLATE   # done, waiting for review
```

- The user is their git `user.name` (set it first as described in [Commit author](#commit-author)).
  `created_by` and `assigned_to` hold exactly that full git name, so match them exactly.
- **Open tasks** with `assigned_to` = the user, or with `assigned_to` empty and `created_by` ≠ the user:
  say who created it, the topic and the date, and offer to do it now.
- **Done tasks** with `created_by` = the user: offer to review the result and close the task or add a
  follow-up.
- If nothing is waiting, say so in one line and continue.

### Creating a task

1. Copy `tasks/_TEMPLATE.md` to `tasks/YYYY-MM-DD-short-topic.md`. Use lowercase and hyphens.
2. Fill in the front matter and the `Task` section. Link related docs with relative paths.
3. Set `status: open`, commit and push.

### Doing a task

1. Write the result only in the `Result` section. Do not edit the `Task` section.
2. Set `status: done`, fill `done_by`, commit and push.
3. If the result changes documentation, update `MAIN_kleverPayroll.md`, `ACCOUNTING_kleverPayroll.md`,
   `klever_api/` or the spec in the same commit.
4. For an implementation task, the result names the `klever_payroll_19` branch, the module version
   and the commit, and lists every deviation from the spec with its reason.

### Follow-ups

- A follow-up on the same topic goes into the `Follow-up` section of the same file; set `status: open` again.
- A new topic means a new file.
- The person who created the task sets `status: closed` once the result is accepted.

### Status values

| Status   | Meaning                                  | Set by     |
|----------|------------------------------------------|------------|
| `open`   | Waiting to be done                       | Creator    |
| `done`   | Result provided, waiting for review      | Assignee   |
| `closed` | Result accepted, nothing more to do      | Creator    |

## Working with Claude Code on the web

The team works here mostly through Claude Code on the web (claude.ai/code), where every session
starts from a fresh clone of this GitHub repository. The team is listed in
[MAIN_kleverPayroll.md](MAIN_kleverPayroll.md#2-team).

- **Access:** each person needs collaborator access to this GitHub repository, and the Claude GitHub
  app must be allowed on it, so a session can clone and push.
- **Branch:** a web session may start on its own branch (e.g. `claude/...`). Do the work, then commit
  to and push `main` as described in [Git workflow](#git-workflow). If the session is not allowed to
  push to `main`, stop and tell the user; never leave the work on another branch.
- **Identity:** a fresh session may have no or a generic git identity. Check it before the first
  commit, as in [Commit author](#commit-author); the name must be the person's own.
- **Start of session:** pull, then check tasks (see [Tasks](#tasks)), and tell the user what is
  waiting for them.
- **Two repositories, one session:** the target setup connects this repository and the code
  repository to the same web environment. Then an agent reads the code to answer a question instead of
  guessing, and links to code as `klever_payroll_19/<path>`. Rules for the code (branches, versions,
  changelogs, naming) are the code repository's own and are never copied here; decisions and specs stay
  here and are never written into the code repository.
- **Until then:** `klever_payroll_19` lives on Azure DevOps and is not reachable from a web session.
  Questions about the code go into a task for Dan; do not guess code behaviour.
- **Experts answer in their own field:** an accounting question is answered by Mariana, a payroll
  question by Hatem. An agent never fills in an expert's answer on their behalf; it can draft the
  wording, and the person confirms it.

## Secrets and personal data

Never commit API tokens, secret keys, activation codes, passwords, real employee names, SINs, bank
account numbers or real payroll amounts. Sandbox ids (`payrl_…`, `be_…`) and made-up example numbers
are fine.

## Writing style

- English only (see the Language section above).
- Markdown only (`.md`), UTF-8.
- One topic per document; use headings and tables for field lists.
- For field/data descriptions prefer tables: name, type, required, description, example.

### Diagrams: Mermaid

Draw diagrams as [Mermaid](https://mermaid.js.org/) code blocks inside the `.md` file. GitHub renders
them as pictures, and because they are text, everyone can review and edit them in a normal commit.

- `erDiagram` or `flowchart` for data models, `flowchart` for processes, `sequenceDiagram` for API
  calls between systems, `stateDiagram-v2` for statuses.
- Quote node labels that contain dots or brackets: `P["ks.klever.payroll"]`; use `<br/>` for line breaks.
- Put a one-line italic caption under each diagram.
- Do not export diagrams to PNG/SVG. Use images (`images/`) only for screenshots, scans
  and photos.
