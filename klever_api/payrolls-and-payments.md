# Payrolls and payments

## Payrolls

`GET /payrolls` — requires `business_entity_id` or `pay_schedule_id`. **(verified)**

| Filter | Values |
| --- | --- |
| `status` | comma-separated, several values allowed |
| `type` | one value |
| `period_start`, `period_end`, `pay_date` | exact date, or `[gt]`, `[gte]`, `[lt]`, `[lte]` |
| `external_ref` | free text |
| `next_payrolls` | upcoming payrolls |
| `sort` | `pay_date`, `period_start`, … |

There is **no `updated_at` filter**: a sync cannot ask "what changed since". **(verified)**

`GET /payrolls/{id}` returns one payroll. **(verified)**

### Status

| Status | Meaning for the GL |
| --- | --- |
| `draft` | Still being edited; codes are re-resolved on every recalculation. **Never import.** |
| `processing` | Approved, money is moving. Codes are fixed. Import. |
| `approved` | Approved. Codes are fixed. Import. |
| `paid` | Done. Import. |
| `failed` | Payment failed. **Do not import**; see open question in Accounting. |
| `partially_paid` | Deprecated, matches nothing. |

Status values **(verified)**; their meaning for the GL **(inferred)** from "approved payrolls keep
the codes resolved at approval".

### Type

`regular`, `off_cycle`, `historical`, `correction`. **(verified)** `historical` payrolls record pay
that was run before kleverPayroll — usually already in the GL from the previous provider.
**(inferred)**

### Dates and totals

- Dates: `pay_date`, `original_pay_date`, `period_start`, `period_end`, `debit_date`, `approved_at`.
- Totals: `employee_summary` (gross, subtractions, reimbursements, net), `contractor_summary`,
  `company_summary` (taxes and contributions, benefits, total), `liability`, `cash_requirement`.
- `external_ref` — free text for the integrator; not unique, not validated.

**(verified)**

The Odoo client already mirrors payrolls into `ks.klever.payroll` with all of these fields
(`ks.klever.business.entity.action_sync_klever_payrolls()` and
`ks.klever.payroll._fetch_klever_payroll()`). **(verified in our code)**

## Payments

| Field | Values |
| --- | --- |
| `destination` | `payroll_float` (PAD debit from the employer's bank), `employee`, `cra`, `rq`, `bill_pay`, `internal`, `no_op`, `on_mof` |
| `method` | `pad`, `direct_deposit`, `wire`, `manual` |

**(verified)**

Money flow: the employer's bank is debited **once**, for `cash_requirement`, into the vendor's payroll
float (PAD). The vendor then pays the employees and remits to CRA/RQ from that float. **(verified)**

Consequence: the `payroll_payment` entry (Dr liabilities / Cr bank) should match **one** line on the
employer's bank statement in Odoo, for `cash_requirement`, on `debit_date`. **(inferred, to check in
the sandbox)**
