# Airtable Field Dictionary

Base: `Staffing Agency Recruitment Pipeline`

This document is the authoritative specification for the Airtable implementation. All sample people and organizations are fictional. The `Imported Jobs` table is populated by the Remotive-to-Airtable n8n workflow.

## Clients

Purpose: Stores staffing-agency customers and their primary commercial contacts.

| Field | Airtable type | Source | Purpose / rule |
| --- | --- | --- | --- |
| Client Name | Single line text, primary | Manual | Human-readable organization name |
| Client Code | Single line text | Manual | Stable business identifier using `CLI-nnn` |
| Industry | Single select | Manual | Structured client segmentation |
| Client Status | Single select | Manual | Prospect, Active, or Inactive |
| Primary Contact | Single line text | Manual | Main client contact |
| Contact Email | Email | Manual | Valid contact email |
| Contact Phone | Phone number | Manual | Main telephone number |
| Website | URL | Manual | Corporate website |
| Billing Country | Single line text | Manual | Commercial operating country |
| Account Notes | Long text | Manual | Relevant account context |
| Job Openings | Linked records | Reciprocal | Openings owned by the client |
| Total Openings | Rollup | Derived | Counts all linked opening IDs |
| Active Openings | Rollup | Derived | Sums the linked openings' `Open Flag` values |

## Job Openings

Purpose: Stores approved, agency-managed vacancies. One record represents one position.

| Field | Airtable type | Source | Purpose / rule |
| --- | --- | --- | --- |
| Opening ID | Single line text, primary | Manual | Stable identifier using `JO-nnnn` |
| Job Title | Single line text | Manual | Position title |
| Department | Single select | Manual | Engineering, Operations, Finance, HR, or Customer Success |
| Employment Type | Single select | Manual | Full-time, Part-time, Contract, or Temporary |
| Work Arrangement | Single select | Manual | Remote, Hybrid, or On-site |
| Location | Single line text | Manual | Position location or region |
| Opening Status | Single select | Manual | Draft, Open, On Hold, Filled, Closed, or Cancelled |
| Open Date | Date | Manual | Date recruitment began |
| Target Start Date | Date | Manual | Intended onboarding date |
| Salary Minimum | Currency | Manual | Lower end of salary range |
| Salary Maximum | Currency | Manual | Upper end of salary range |
| Salary Currency | Single select | Manual | USD, PHP, or AUD |
| Hiring Manager | Single line text | Manual | Client-side hiring owner |
| Job Description | Long text | Manual | Concise position summary |
| Client | Single linked record | Manual | Owning client; limited to one |
| Applications | Linked records | Reciprocal | Applications submitted for the opening |
| Placements | Linked records | Reciprocal | Placement associated with the opening |
| Imported Jobs | Linked records | Reciprocal | External records linked after review |
| Open Flag | Formula | Derived | Returns `1` when status is Open, otherwise `0` |
| Client Industry | Lookup | Derived | Industry from the linked client |
| Application Count | Rollup | Derived | Counts linked application IDs |
| Placement Count | Rollup | Derived | Counts linked placement IDs |
| Placement Control | Formula | Derived | Flags an exception if more than one placement is linked |
| Salary Validation | Formula | Derived | Flags an exception when minimum exceeds maximum |

## Candidates

Purpose: Stores reusable candidate profiles that can participate in multiple applications.

| Field | Airtable type | Source | Purpose / rule |
| --- | --- | --- | --- |
| Candidate ID | Single line text, primary | Manual | Stable identifier using `CAN-nnn` |
| Full Name | Single line text | Manual | Candidate display name |
| Email | Email | Manual | Candidate email |
| Phone | Phone number | Manual | Candidate phone number |
| Location | Single line text | Manual | Candidate location |
| Current Title | Single line text | Manual | Current or most recent role |
| Years of Experience | Number | Manual | Whole-number experience estimate |
| Candidate Source | Single select | Manual | Referral, LinkedIn, Job Board, Agency Database, or Direct Application |
| Availability | Single select | Manual | Immediately, 2 weeks, 1 month, or Not actively looking |
| Expected Salary | Currency | Manual | Candidate salary expectation |
| Candidate Status | Single select | Manual | New, Active, Placed, or Inactive |
| LinkedIn Profile | URL | Manual | Optional professional profile URL |
| Recruiter Notes | Long text | Manual | Recruiter context and observations |
| Applications | Linked records | Reciprocal | Candidate's application history |
| Placements | Linked records | Reciprocal | Candidate's placement history |

## Applications

Purpose: Junction table resolving the many-to-many relationship between Candidates and Job Openings.

| Field | Airtable type | Source | Purpose / rule |
| --- | --- | --- | --- |
| Application ID | Single line text, primary | Manual | Stable identifier using `APP-nnn` |
| Application Date | Date | Manual | Date the application entered the pipeline |
| Application Stage | Single select | Manual | Applied, Screening, Interview, Offer, Hired, Rejected, or Withdrawn |
| Recruiter Owner | Single line text | Manual | Recruiter responsible for the application |
| Last Stage Change | Date | Manual | Most recent pipeline movement date |
| Candidate Rating | Number | Manual | Whole-number candidate evaluation rating |
| Resume URL | URL | Manual | Reference to the candidate resume |
| Application Notes | Long text | Manual | Application-specific notes |
| Candidate | Single linked record | Manual | Candidate; limited to one |
| Job Opening | Single linked record | Manual | Opening; limited to one |
| Placements | Linked records | Reciprocal | Placement created from the successful application |
| Application Key | Formula | Derived | Candidate-opening combination used to detect duplicates |
| Candidate Email | Lookup | Derived | Email from the linked candidate |
| Opening Title | Lookup | Derived | Job title from the linked opening |

## Placements

Purpose: Stores successful hires. Each opening may have no more than one placement.

| Field | Airtable type | Source | Purpose / rule |
| --- | --- | --- | --- |
| Placement ID | Single line text, primary | Manual | Stable identifier using `PLC-nnn` |
| Start Date | Date | Manual | Agreed candidate start date |
| Placement Status | Single select | Manual | Planned, Started, Completed, or Cancelled |
| Placement Fee | Currency | Manual | Agency fee for the successful placement |
| Guarantee End Date | Date | Manual | End of the replacement or guarantee period |
| Placement Notes | Long text | Manual | Onboarding and commercial context |
| Application | Single linked record | Manual | Successful application; limited to one |
| Candidate | Single linked record | Manual | Placed candidate; retained for straightforward Airtable reporting |
| Job Opening | Single linked record | Manual | Filled opening; limited to one |
| Candidate Name | Lookup | Derived | Full name from the linked candidate |
| Opening Title | Lookup | Derived | Job title from the linked opening |

## Imported Jobs

Purpose: Stages external Remotive listings separately from agency-approved openings.

| Field | Airtable type | Source | Purpose / rule |
| --- | --- | --- | --- |
| Remotive Job ID | Single line text, primary | API | Stable external identifier used for upserts |
| Job Title | Single line text | API | External job title |
| Company | Single line text | API | External employer name |
| Location | Single line text | API | Location supplied by Remotive |
| Job Type | Single line text | API | Employment type supplied by Remotive |
| Publication Date | Date and time | API | Source publication timestamp in UTC |
| Job URL | URL | API | Original listing URL |
| Category | Single line text | API | Remotive category |
| Salary Text | Single line text | API | Unstructured salary text from the source |
| Source | Single select | Integration | Fixed to Remotive |
| Imported At | Date and time | Integration | Workflow processing timestamp in UTC |
| Import Status | Single select | Manual/Integration | New, Reviewed, Promoted, or Skipped |
| Description | Long text | API | External listing description |
| Linked Job Opening | Single linked record | Manual | Approved opening linked after review |
| Deduplication Key | Formula | Derived | Produces `remotive:<job-id>` for reliable matching |
| Linked Opening Title | Lookup | Derived | Title of the linked agency opening |

## Relationship model

| Parent | Related table | Cardinality | Implementation | Business rule |
| --- | --- | --- | --- | --- |
| Clients | Job Openings | One-to-many | `Job Openings.Client` | Every approved opening belongs to one client |
| Candidates | Applications | One-to-many | `Applications.Candidate` | One candidate can apply to multiple openings |
| Job Openings | Applications | One-to-many | `Applications.Job Opening` | One opening can receive multiple applications |
| Applications | Placements | Zero-to-one | `Placements.Application` | A placement is created only from a successful application |
| Candidates | Placements | One-to-many | `Placements.Candidate` | Direct link supports Airtable reporting and must agree with the application |
| Job Openings | Placements | Zero-to-one | `Placements.Job Opening` | `Placement Count` and `Placement Control` expose violations |
| Job Openings | Imported Jobs | Zero-to-many | `Imported Jobs.Linked Job Opening` | External listings remain staged until reviewed |

## Verified sample data

| Table | Records | Verification result |
| --- | ---: | --- |
| Clients | 4 | Northstar has two total openings and one active opening |
| Job Openings | 5 | Application and placement counts resolve correctly; all validation fields show OK |
| Candidates | 5 | One candidate participates in multiple applications |
| Applications | 5 | Candidate and opening lookups resolve; application keys are distinct |
| Placements | 3 | Candidate/opening lookups resolve; each opening has one placement |
| Imported Jobs | 10 | Populated with distinct public Remotive records during workflow validation |

MCP schema verification confirmed `prefersSingleRecordLink: true` for all seven many-to-one input fields.

## Airtable control limitation

Airtable exposes the one-placement-per-opening rule through `Placement Count` and `Placement Control`, but it does not provide a native relational unique constraint. A production Postgres implementation would enforce this with a unique constraint on `placements.opening_id` and additional foreign-key checks.
