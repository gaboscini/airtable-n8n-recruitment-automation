# Production Roadmap

The current project is a working portfolio implementation. The items below describe how it could evolve into a production recruitment operations system.

## 1. Make writes atomic

The current n8n flow checks for an existing Remotive ID before creating a record. This is idempotent for normal sequential runs but is not atomic under concurrency.

Recommended next step:

- Move imported jobs to Postgres or another database with `unique (source, external_job_id)`.
- Replace search-then-create with an atomic upsert.
- Keep Airtable as an operational interface only if business users still need it.

## 2. Add source synchronization

Existing imported jobs are intentionally skipped. A production version should define which source changes are safe to synchronize.

- Track `first_seen_at`, `last_seen_at`, and `source_updated_at`.
- Preserve recruiter-owned fields during source updates.
- Mark listings unavailable only after a defined grace period.
- Record material source changes for review.

## 3. Improve observability

- Send failure notifications to the operations channel.
- Alert on unusually high rejection counts or zero-result runs.
- Store run-level metrics outside n8n execution history.
- Add correlation IDs for tracing an imported record back to its execution.

## 4. Strengthen governance

- Apply least-privilege credentials and documented rotation procedures.
- Define candidate consent, retention, and deletion policies before using real personal data.
- Add role-based access for recruiters, managers, and administrators.
- Maintain an auditable history for placement and status changes.

## 5. Improve recruiter experience

- Add controlled candidate and vacancy intake forms.
- Create recruiter-focused Airtable interfaces and exception queues.
- Add a review workflow for imported jobs before promotion.
- Introduce category, location, and seniority filters for narrower sourcing.

## 6. Expand automated testing

- Add fixtures for malformed payloads, missing required fields, duplicate IDs, and unexpected dates.
- Test retry and rate-limit behavior without calling the live API.
- Validate the exported workflow in CI for JSON syntax, embedded code syntax, unresolved node connections, and credential objects.
- Add a repeatable database consistency check for relationships and calculated controls.
