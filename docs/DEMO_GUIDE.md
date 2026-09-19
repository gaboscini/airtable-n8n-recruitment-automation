# Demonstration Guide

This guide provides a short, repeatable way to demonstrate the project without exposing credentials or private configuration.

## 1. Airtable model

Show the six tables and highlight:

- `Applications.Candidate` and `Applications.Job Opening` as the many-to-many junction.
- `Applications.Candidate Email` and `Applications.Opening Title` as lookups.
- `Clients.Total Openings` and `Clients.Active Openings` as rollups.
- Imported Jobs as a staging area separate from approved Job Openings.

## 2. Workflow structure

Walk through the four n8n stages from left to right:

1. Trigger and configuration.
2. Fetch, validate, and normalize.
3. Deduplicate and write.
4. Execution summary.

Explain that the export supports manual testing and an optional 12-hour schedule.

## 3. Execution scenarios

Demonstrate the three stored execution patterns:

- **Initial import:** new Remotive IDs follow Create Imported Job and Mark Created.
- **Repeated run:** existing IDs follow Mark Skipped and the create node does not execute.
- **Controlled failure:** an invalid response follows the false validation branch and stops before Airtable.

## 4. Design trade-offs

Close with the decisions that matter most:

- Airtable favors accessibility for operational users over strict relational enforcement.
- Skipping existing imports protects recruiter-managed fields.
- Search-then-create is appropriate for a controlled demonstration but should become an atomic upsert at scale.
- Production operation should add external alerting, governance, and source-update policies.

## Demo safety

- Use only fictional recruitment data.
- Never open credential settings during a recording or live demo.
- Keep `simulateFailure` set to `false` outside the controlled test.
- Confirm the Airtable link is read-only before sharing it.
