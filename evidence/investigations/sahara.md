# Sahara Medical Supplies FZE - Investigation

## Test findings
- Seller ID: 5
- Total invoices: 52
- Accepted: 20
- Submitted: 32

## Database evidence
- 52 Parsed events: Created from uploaded file
- 52 Validated events: Passed portal validation
- 52 Submitted events: Sent to ASP
- 20 Accepted invoices confirmed by ASP
- 32 invoices currently remain in Submitted status
- Latest 30 events show Submitted / Sent to ASP.

## API log evidence
- ASP HTTP 429 responses were observed during the load test.
- The invoice_events table contains no event detail explicitly mentioning 429.
- Exact mapping between individual 429 responses and the 32 Submitted invoices is not established.

## Preliminary root cause
The ASP rate limit is a strong suspected contributor. The API submission flow does not appear to handle HTTP 429 correctly, based on code inspection. Further correlation is needed before attributing every Submitted invoice to a 429 response.

## Recommended actions
- Implement HTTP 429 handling with Retry-After support and exponential backoff.
- Persist retry state instead of leaving invoices indefinitely in Submitted.
- Add monitoring for 429 responses and invoices pending ASP acknowledgement.
- Reconcile pending invoices with ASP before retrying to avoid duplicate submissions.

## Evidence
- ../environment/load-test.txt
- API logs captured during investigation
- Database status and invoice_events query results

## Additional code evidence
- `api-429-switch.txt` — submission response handling does not include an explicit HTTP 429 case.
- `retry-state-check.txt` — normal retry eligibility is limited to Validated and Failed statuses; Submitted invoices are not eligible.
- These code findings support the suspected retry-handling gap, but do not prove that every one of the 32 Submitted invoices received HTTP 429.
