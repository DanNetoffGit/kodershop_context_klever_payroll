---
source_url: https://docs.nmbr.co/api-reference/journal-entries/retrieve-a-journal-entry
kind: vendor-doc
captured: 2026-11-09
captured_by: Daniil Honcharenko
---

# Vendor doc: Retrieve a journal entry

Verbatim copy of the vendor API reference page, as pasted from the browser. Never edited; the digest
is [../../../journal-entries.md](../../../journal-entries.md).

```text
Retrieve a journal entry

Copy page

GET
/
journal_entries
/
{journal_entry}


Try it
Retrieve a journal entry

curl --request GET \
  --url https://sandbox.nmbr.co/services/payroll/journal_entries/{journal_entry} \
  --header 'Authorization: Bearer <token>'

200
{
  "id": "<id>",
  "object": "journal_entry",
  "data": {
    "type": "payroll_recording",
    "title": "Payroll Accrual Entry",
    "title_translations": {
      "en": "Payroll Accrual Entry",
      "fr": "Écriture de provision de la paie"
    },
    "title_translated": "Payroll Accrual Entry",
    "payroll": {
      "id": "<id>",
      "object": "payroll",
      "links": {
        "self": "/payrolls/<id>"
      }
    },
    "pay_stub": null,
    "is_balanced": true,
    "total": 0,
    "is_missing_line_item_accounting_codes": true,
    "is_missing_default_account_payable": true,
    "is_missing_default_bank_account": false,
    "posted_on": "2026-01-30T00:00:00.000000Z",
    "invalid_expense_types": [
      {
        "type": "employer_statutory_withholding",
        "subtype": "ei"
      },
      {
        "type": "employer_statutory_withholding",
        "subtype": "cpp"
      },
      {
        "type": "employer_statutory_withholding",
        "subtype": "cpp_2"
      }
    ],
    "invalid_liability_types": [
      {
        "type": "statutory_withholding",
        "subtype": "cpp"
      },
      {
        "type": "statutory_withholding",
        "subtype": "cpp_2"
      },
      {
        "type": "statutory_withholding",
        "subtype": "ei"
      },
      {
        "type": "statutory_withholding",
        "subtype": "federal_income_tax"
      },
      {
        "type": "statutory_withholding",
        "subtype": "provincial_income_tax"
      },
      {
        "type": "employer_statutory_withholding",
        "subtype": "ei"
      },
      {
        "type": "employer_statutory_withholding",
        "subtype": "cpp"
      },
      {
        "type": "employer_statutory_withholding",
        "subtype": "cpp_2"
      }
    ],
    "integrations": {
      "quickbooks": {
        "reference": null
      },
      "xero": {
        "reference": null
      }
    },
    "created_at": "2026-01-01T00:00:00.000000Z",
    "updated_at": "2026-01-01T00:00:00.000000Z"
  },
  "links": {
    "self": "/journal_entries/<id>"
  }
}
Authorizations

Authorization
stringheaderrequired
Bearer authentication header of the form Bearer <token>, where <token> is your auth token.

Path Parameters

journal_entry
stringrequired
Query Parameters

expand
enum<string>[]
Available options: journal_entry_rows, pay_stub, payroll 
Response
200 - application/json
OK


id
stringread-only
The unique identifier of the object in Nmbr.


object
stringread-only
The type of the object in Nmbr ("journal_entry").


data
Default · object
Default
With payroll expanded
With pay_stub expanded
With journal_entry_rows expanded
Hide child attributes


data.type
enum<string>
The type of journal entry (e.g. payroll recording, payroll payment).

Available options: pay_stub_recording, payroll_payment, payroll_recording 

data.title
string | null
Maximum string length: 255

data.title_translations
object | null
Optional translations for the title property.

Hide child attributes


data.title_translations.en
string
Maximum string length: 255

data.title_translations.fr
string
Maximum string length: 255

data.title_translated
stringread-only
The translation of the title property for the request locale. Computed using the values in title and title_translations and the value of the request's Accept-Language header.


data.payroll
object
The Payroll this journal entry belongs to.

Hide child attributes


data.payroll.id
stringread-only
The unique identifier of the object in Nmbr.


data.payroll.object
stringread-only
The type of the object in Nmbr ("payroll").


data.pay_stub
any
The Pay Stub this journal entry belongs to. Present only for Pay Stub-level journal entries.


data.is_balanced
boolean
Whether the journal entry is balanced (total debits equal total credits).


data.total
number<decimal> | null
The total amount for this journal entry.


data.is_missing_line_item_accounting_codes
integer
Whether any rows in this journal entry are missing an Accounting Code assignment.


data.is_missing_default_account_payable
integer
Whether the accounts payable row is missing an Accounting Code.


data.is_missing_default_bank_account
integer
Whether the bank row is missing an Accounting Code.


data.posted_on
string<date> | null
The date this journal entry was posted to the accounting system.


data.invalid_expense_types
object | null
Line Item type/subtype pairs that are missing an expense Accounting Code. Each entry contains type and subtype.


data.invalid_liability_types
object | null
Line Item type/subtype pairs that are missing a liability Accounting Code. Each entry contains type and subtype.


data.integrations
object | null
External integration references. Contains xero and quickbooks objects, each with a reference field holding the external ID if synced.

Hide child attributes


data.integrations.quickbooks
object | null
Hide child attributes


data.integrations.quickbooks.reference
any

data.integrations.xero
object | null
Hide child attributes


data.integrations.xero.reference
any

data.created_at
string<dateTime>read-only
The date and time the object was created in Nmbr.


data.updated_at
string<dateTime>read-only
The date and time the object was last updated in Nmbr.
```
