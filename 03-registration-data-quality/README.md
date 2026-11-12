# Cleaning a Conference Registration List with Contracts and a Hybrid Cleaner

**Scenario:** The Midwest Analytics Summit, a fictional conference, exported 120 registrations from three sign-up forms built by different volunteers. The organizers need a clean list for badges, catering, room sizing and revenue reconciliation.

**Tools:** Python, pandas, regex, `dateutil`, Pydantic, Google Gemini (`gemini-3.5-flash-lite`)

## 1. Data quality profile

I wrote 16 rule-based checks across five quality dimensions. **Every one of the 120 rows had at least one problem.** The biggest issues:

- 100% of dietary notes were not in a standard category.
- 90% of companies had several spellings.
- 80% of dates were in mixed formats.
- 78% of state codes were invalid ("SoDak", "Nebr.", "Minnesota").
- 17% of emails were duplicates.

Rules cannot catch everything. A well-formed but misspelled email domain looks valid. The same person registering with a nickname needs fuzzy matching.

## 2. Data contract (Pydantic)

A `CleanRegistration` model defines what "clean" means:

- Allowed-value lists for seniority, state, ticket type, dietary category and workshop.
- Validators for the date and phone formats.
- A **cross-field rule:** the amount paid must match the ticket price (for example, Student = $49).

It rejected hand-made bad records with readable error messages, such as *"amount_paid 349 does not match the Student price of 49."*

## 3. Hybrid cleaner: code first, AI only where judgment is needed

| Handled by code (rules) | Handled by AI |
|---|---|
| Names, emails, phones, companies, states, dates, ticket types, amounts, workshops | Seniority from job titles; dietary notes that need judgment |

- The AI makes **one call per unique value**, not per row: **36 AI calls instead of 240**, with 0 failures.
- Every cleaned row is validated against the contract. All 120 passed.
- **Field accuracy on the 25 labeled dev rows: 100%** on all 12 fields.

## 4. Duplicate detection

- **Find candidates** with three keys: email, phone, and last name plus company. This produced 22 candidate pairs.
- **Verify each pair** with an explicit rule: same last name, matching first name or initial, and at least one shared contact detail. **10 pairs were confirmed** and 12 were rejected.
- An **audit log** records every decision and its evidence.
- Duplicate accuracy on dev: **100%**.

## 5. Results

Rows with at least one issue fell from **120 to 25**. The 25 left need a human:

- 9 invalid emails and 17 missing phones, which need follow-up with the attendees.
- 2 missing payment amounts, which need a check against payment records.

**Who should own each field:** code owns the predictable fields, AI owns the judgment fields (seniority, dietary needs), and a human owns missing contact details and borderline duplicates.
