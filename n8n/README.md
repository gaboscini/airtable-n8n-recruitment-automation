# Remotive-to-Airtable n8n Workflow

This workflow imports a controlled batch of public Remotive listings into the Airtable `Imported Jobs` staging table. It is designed to be repeatable, observable, and safe to share without credentials.

## Workflow file

Import [`remotive-airtable-import.json`](remotive-airtable-import.json) into n8n.

The export:

- Contains no Airtable token or n8n credential object.
- Is inactive by default.
- Uses a manual trigger for testing and a 12-hour schedule for optional operation.
- Limits each demonstration batch to 10 selected jobs.

## Processing flow

```text
Manual Trigger or Every 12 Hours
  -> Configuration
  -> Fetch Remotive Jobs
  -> Validate API response
     -> invalid: stop with a controlled error
     -> valid: normalize and validate jobs
  -> process jobs one at a time
  -> search Airtable by Remotive Job ID
     -> existing: skip
     -> new: create Imported Jobs record
  -> execution summary
```

## Default configuration

| Setting | Value |
| --- | --- |
| API endpoint | `https://remotive.com/api/remote-jobs` |
| Search term | `developer` |
| Maximum jobs | `10` |
| Schedule | Every 12 hours when activated |
| Controlled failure | `false` during normal operation |

## Field mapping

| Remotive value | Airtable field | Type |
| --- | --- | --- |
| `id` | Remotive Job ID | Single-line text |
| `title` | Job Title | Single-line text |
| `company_name` | Company | Single-line text |
| `candidate_required_location` | Location | Single-line text |
| `job_type` | Job Type | Single-line text |
| `publication_date` | Publication Date | Date and time |
| `url` | Job URL | URL |
| `category` | Category | Single-line text |
| `salary` | Salary Text | Single-line text |
| Constant `Remotive` | Source | Single select |
| Workflow timestamp | Imported At | Date and time |
| Constant `New` | Import Status | Single select |
| Sanitized `description` | Description | Long text |

Normalize Jobs also removes scripts, styles, and HTML tags; validates required fields, URLs, and dates; limits text lengths; and removes duplicate IDs inside the same API response.

## Deduplication

Each job is searched in Airtable using an exact match on `Remotive Job ID`:

```text
{Remotive Job ID}='<current Remotive ID>'
```

Existing records are skipped instead of overwritten. This protects recruiter-managed fields such as Import Status and Linked Job Opening.

## Error handling

- The HTTP request retries transient failures.
- Valid API Response blocks empty or malformed responses.
- Normalize Jobs rejects invalid individual records.
- Stop - Invalid API Response ends a failed run before Airtable is reached.
- Execution Summary exposes received, selected, valid, rejected, created, skipped, and processed counts.

## Setup

1. Create or copy the Airtable schema described in [`../airtable/FIELD_DICTIONARY.md`](../airtable/FIELD_DICTIONARY.md).
2. Import `remotive-airtable-import.json` into n8n.
3. Create an Airtable Personal Access Token restricted to your base with record-read, record-write, and base-schema-read access.
4. Select that credential in Find Existing Remotive Job and Create Imported Job.
5. Select your base and Imported Jobs table in both Airtable nodes.
6. Confirm `simulateFailure` is `false`.
7. Run the three validation scenarios in [`../docs/TESTING.md`](../docs/TESTING.md).
8. Activate the schedule only if recurring imports are wanted.

## Security notes

Never commit Airtable tokens, n8n credential exports, webhook secrets, or execution screenshots that expose private configuration. If a token has appeared in chat, a recording, or source control, revoke and replace it before using this workflow.
