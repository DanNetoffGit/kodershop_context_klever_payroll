# kleverPayroll API — what we know

Facts about the kleverPayroll API and its sandbox that our Odoo integration depends on. Each statement
is marked **(verified)** — read in the vendor docs or seen in the sandbox — or **(inferred)** —
reasoning still to be confirmed. Specs may only rely on verified facts as hard requirements.

| Environment | Base URL |
| --- | --- |
| Sandbox | `https://sandbox.nmbr.co/services/payroll` |
| Production | `https://app.nmbr.co/services/payroll` |

Authentication: `Authorization: Bearer <company-scoped access token>`. The Odoo client already
obtains and refreshes this token (`ks.klever.config._ensure_klever_token()`). **(verified)**

Pagination of list endpoints: 15 items per page, `page` query parameter, response carries
`links.next` and `meta.has_more`. **(verified)**

## Sources

Every **(verified)** statement rests on a source kept in this folder, in two kinds:

| Kind | Where | What it holds |
| --- | --- | --- |
| Vendor documentation | `sources/<path of the page on docs.nmbr.co>.md` | The page copied verbatim, with its URL and capture date in the front matter |
| Sandbox calls | `samples/YYYY-MM-DD-<endpoint>.md` | A real request and response from the sandbox, with the date and the Business Entity |

- A source file is never edited after it is committed. When the vendor page changes, capture it
  again under the same path and say in the commit what changed.
- The digests below summarise the sources and link to them; a statement with no source is
  **(inferred)**.
- Never put a token, secret key or real personal data into a sample.

Captured so far:

| Page | Source file |
| --- | --- |
| [Accounting → Overview](https://docs.nmbr.co/guides/accounting/overview) | [sources/guides/accounting/overview.md](sources/guides/accounting/overview.md) |
| Accounting Codes and Accounting Code Rules (API reference menu) | [sources/api-reference/accounting-codes-and-rules-menu.md](sources/api-reference/accounting-codes-and-rules-menu.md) |
| [Retrieve a journal entry](https://docs.nmbr.co/api-reference/journal-entries/retrieve-a-journal-entry) | [sources/api-reference/journal-entries/retrieve-a-journal-entry.md](sources/api-reference/journal-entries/retrieve-a-journal-entry.md) |

## Files

- [Accounting overview](accounting-overview.md) — the vendor's own summary of how accounting codes,
  rules, tags, journal entries and integrations fit together.
- [Accounting codes and rules](accounting-codes.md) — the chart of accounts as kleverPayroll knows it,
  and how payroll line items are routed to it.
- [Journal entries](journal-entries.md) — the entries kleverPayroll generates per payroll, their rows
  and their validation flags.
- [Payrolls and payments](payrolls-and-payments.md) — payroll statuses, types, dates and totals, and
  how money moves.
- [Reports and lookups](reports-and-lookups.md) — the Journal Entry report, segment templates, line
  item type lookups.
- [Sandbox state of 4 Nov 2026](sandbox-2026-11-04.md) — the test Business Entity and the payroll to
  test with, and what is still to check.
