# Marina Bay Holdings - Investigation

## Priority
P1 - Critical internal alert and customer go-live risk.

## Customer impact
11 invoices remain in Validated status. The customer reported invoices are not progressing to ASP submission. Marina Bay has an upcoming Wednesday go-live.

## Investigation performed
1. Queried invoice status counts for seller_id = 3.
2. Identified 11 invoices currently in Validated status.
3. Queried event history for all 11 invoices.
4. Confirmed each invoice has Parsed and Validated events, but no Sent to ASP event.
5. Inspected API container state.
6. Checked API logs for panic, fatal, error, timeout, 502, 503 and killed messages.

## Evidence
- marina-validated-invoices.txt
- marina-event-history.txt
- marina-container-inspect.txt
- marina-container-state.json
- marina-api-errors.txt

## Findings
- 11 invoices are stuck in Validated status.
- No ASP submission event exists for these invoices.
- API container RestartCount is 2.
- OOMKilled is false.
- Container is currently running and healthy.
- No matching error was found in the available API logs.

## Root cause
Unconfirmed. The evidence establishes that invoices did not progress from validation to ASP submission, but it does not establish why. The two API restarts are observed facts, not a proven cause of the invoice issue.

## Immediate action / workaround
Escalate to the engineering team to inspect the submission worker, queue health, and ASP dispatch logs. Do not ask the customer to re-upload or resend the invoices until duplicate-submission risk is checked.

## Prevention
Add monitoring for invoices remaining in Validated status beyond an agreed threshold, alert on API restarts, and correlate invoice IDs across submission-worker and ASP logs.

## Next checks
Inspect worker/container logs and queue state, then correlate the 11 invoice IDs with submission attempts and ASP responses.

## Container evidence
- API container restart count: 2.
- OOMKilled: false.
- ExitCode: 0.
- Current status: running.
- This confirms two restarts but does not establish an out-of-memory cause.
- The exact restart trigger remains unconfirmed and needs correlation with worker, queue and ASP logs.

Evidence: `marina-container-inspect.txt`, `marina-container-state.json`, `marina-api-errors.txt`.
