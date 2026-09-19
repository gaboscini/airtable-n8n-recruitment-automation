# Test Scenarios and Results

The implementation was validated against normal execution, repeat-run idempotency, and controlled failure behavior.

## Verified results

| Scenario | Expected behavior | Observed result |
| --- | --- | --- |
| Airtable relationships | Linked records and calculated fields resolve consistently | Six-table schema, reciprocal links, lookups, rollups, and formulas resolved correctly |
| Initial Remotive import | Valid records are mapped into separate Airtable fields | 10 distinct jobs were created with IDs, URLs, timestamps, source, status, and normalized descriptions |
| Repeated execution | Existing external IDs are not created again | All 10 jobs followed Mark Skipped; Create Imported Job did not execute |
| Duplicate count check | Airtable record count remains unchanged | Imported Jobs remained at 10 records with 10 distinct Remotive IDs |
| Invalid API response | No malformed data reaches Airtable | Validation followed the false branch and Stop - Invalid API Response ended the run |
| Failed-run write check | A failed request creates no records | Imported Jobs remained unchanged |
| Sanitized export | Public workflow contains no credentials | JSON parsed successfully with no credential object or detected token pattern |

## Reproduce the checks

### Successful import

1. Point both Airtable nodes to an empty Imported Jobs table.
2. Confirm `simulateFailure` is `false`.
3. Execute the Manual Trigger.
4. Inspect Execution Summary and the created Airtable records.
5. Confirm the mapped URL and date fields use the expected Airtable types.

### Duplicate-safe repeat run

1. Run the workflow again without changing the search term or destination table.
2. Confirm every existing ID follows Mark Skipped.
3. Confirm Create Imported Job does not execute.
4. Confirm the Airtable record count does not increase.

### Controlled failure

1. Set `simulateFailure` to `true`.
2. Execute the Manual Trigger.
3. Confirm Valid API Response follows the false branch.
4. Confirm Stop - Invalid API Response ends the execution.
5. Confirm no Airtable write node executes.
6. Restore `simulateFailure` to `false` immediately after the test.

## Workflow export checks

Before publishing a new export:

- Parse the file as JSON.
- Compile the JavaScript from every Code node.
- Confirm every connection points to an existing node.
- Confirm `active` is `false`.
- Confirm no node contains a credential object.
- Scan for tokens, authorization headers, private links, and personal data.
