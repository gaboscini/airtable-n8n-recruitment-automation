# Airtable Recruitment Data Model

The `Staffing Agency Recruitment Pipeline` base models the full recruitment lifecycle with linked records and structured field types. All client, contact, candidate, and recruitment sample data is fictional.

[Open the read-only Airtable demo](https://airtable.com/appjWilmBmSIATTmC/shrYU01l9fDnAm1Ft)

## Tables

| Table | Responsibility |
| --- | --- |
| Clients | Customer organizations and commercial contacts |
| Job Openings | Approved vacancies owned by clients |
| Candidates | Reusable candidate profiles |
| Applications | Candidate-opening junction and recruitment stage |
| Placements | Successful hires and placement-specific commercial data |
| Imported Jobs | Public Remotive listings awaiting review |

## Relationship model

- One Client can own many Job Openings.
- One Candidate can submit many Applications.
- One Job Opening can receive many Applications.
- Applications resolves the many-to-many Candidate and Job Opening relationship.
- One successful Application can produce at most one Placement.
- One Job Opening can have at most one Placement.
- An Imported Job can optionally link to an approved Job Opening after review.

## Business controls

- `Clients.Total Openings` counts every linked opening.
- `Clients.Active Openings` sums the linked openings that are currently open.
- Application lookups expose candidate email and opening title without copying source data.
- Placement lookups expose candidate and opening context.
- `Application Key` flags repeated candidate-opening combinations.
- `Placement Control` flags more than one placement against an opening.
- `Salary Validation` flags an invalid minimum and maximum range.
- `Deduplication Key` produces a stable `remotive:<job-id>` value.

## Sample dataset

The base includes four fictional clients, five candidates, five approved openings, five applications, and three placements. The n8n workflow adds real public job listings to Imported Jobs.

## Implementation reference

[`FIELD_DICTIONARY.md`](FIELD_DICTIONARY.md) documents every field, type, relationship, formula, lookup, rollup, and business rule required to recreate the base.

## Airtable limitations

Airtable is approachable for operational users, but formula fields detect rather than enforce uniqueness rules. A relational production implementation should enforce one application per candidate-opening pair and one placement per opening with database constraints.
