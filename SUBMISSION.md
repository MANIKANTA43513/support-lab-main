Product Support Engineer - L1 Support Investigation Report

Submission Details

Name: Manikanta Kandula

Date submitted: [Fill in before submission]

Repository link: [Paste your GitHub repository URL]

Total time spent (real time): [Enter actual time spent]

Exercise-clock note: All customer communication drafts and incident timelines below use the exercise clock: Monday, 16 November 2026, Asia/Dubai (UTC+4). No customer emails were actually sent. Drafts are included for review. Supporting logs, database outputs, code checks, and investigation notes are stored in the evidence/ directory.

1. Executive Summary

I approached this exercise as an L1 Product Support Engineer responsible for protecting customer operations while maintaining clear ownership of each incident.

My first objective was to understand the actual business impact rather than treating every incoming issue as an isolated technical error. I reviewed the five incidents, assigned priorities using the provided SLA matrix, inspected the available application and infrastructure evidence, and investigated the incidents where the available evidence allowed a meaningful conclusion.

The investigations highlighted four different support scenarios:

Long-running invoice imports: A customer experienced a slow import, repeated retries, and duplicate-related errors.

Production degradation: Multiple customers experienced 502 responses while invoices remained in the Validated state.

Invoice validation failures: Specific invoices were rejected because calculated totals did not match the expected values.

ASP submission handling: A batch contained invoices stuck in Submitted, with evidence of HTTP 429 responses and missing handling in the application's response switch.

Input-format usability: A small business submitted dates in DD/MM/YYYY format, while the parser expected ISO dates or supported Excel date values.

The main lesson is that a successful HTTP response from an import endpoint does not necessarily mean that every invoice has reached the ASP. Each invoice must be traced through its actual processing state and submission outcome.

2. Incident Prioritisation

Incident

Priority

Business impact

Reason

Oasis Builders Supply LLC

P2

Month-end close at risk; repeated retries may create duplicate confusion

Import is slow and some invoice numbers are reported as already existing

Marina Bay Hospitality / production API

P1

Multiple customers affected; invoices are not progressing to ASP

Critical restart alert, 502 reports, and invoices remaining Validated

Al Noor Fuel & Lubricants LLC

P2

VAT audit review affected for a subset of invoices

3 of 6 test invoices rejected with BR-CO-15

BluePeak / Sahara Medical Supplies FZE

P2

32 of 40 test invoices remain Submitted; buyer receipt is incomplete

Evidence shows 32 HTTP 429 responses and missing 429 handling

Ghaf Tree Café LLC

P3

All invoices in the test batch rejected due to date format

Single-customer input-format issue with a practical workaround

Prioritisation principle: I prioritised incidents based on whether invoices could reach the ASP, the number of affected customers or invoices, the business impact, and the urgency of the customer's workflow.

3. SLA Assessment

The following deadlines are calculated using the supplied SLA matrix. P1 is monitored 24x7. P2–P4 use business hours of Monday–Friday, 08:00–18:00 Asia/Dubai, with the clock pausing outside business hours.

Incident

Priority

Received

First response deadline

Resolution target

Oasis Builders

P2

09:05, Mon 16 Nov

10:05, Mon 16 Nov

17:05, Mon 16 Nov

Marina Bay / API alert

P1

09:42, Mon 16 Nov

10:12, Mon 16 Nov

13:42, Mon 16 Nov

Al Noor Fuel

P2

10:15, Mon 16 Nov

11:15, Mon 16 Nov

08:15, Tue 17 Nov

BluePeak / Sahara

P2

11:30, Mon 16 Nov

12:30, Mon 16 Nov

09:30, Tue 17 Nov

Ghaf Tree Café

P3

12:10, Mon 16 Nov

16:10, Mon 16 Nov

10:10, Thu 19 Nov

SLA handling notes:

A meaningful acknowledgement must explain what is being checked and when the customer will hear back.

Escalation does not pause or reset the SLA clock.

L1 remains responsible for customer updates even when L2 or L3 takes over technical investigation.

P1 requires immediate on-call escalation and customer updates every 30–60 minutes.

P2 updates are required every 2–4 hours; P3 updates are daily.

4. Acknowledgements and Priority for All Five Incidents

4.1 Oasis Builders Supply LLC

Received: Monday, 16 November 2026, 09:05 +0400
Priority: P2

Acknowledgement draft

Subject: Re: November invoice import – Processing status and duplicate warnings

Hello Rashid,

Thank you for reporting this. I understand that the November invoice import is taking longer than expected and that repeated retries have resulted in some invoice numbers being reported as already existing.

We are checking the import processing status and the invoice records already accepted by the system. Please avoid uploading the same batch again until we confirm which invoices have been processed, so that we do not introduce additional duplicates.

I will share the verified invoice status and the safest next steps by 10:05 AM Gulf time.

Regards,
Product Support Team

Priority justification: P2 because the import is affecting a month-end business process and repeated retries may create duplicate confusion. The available test evidence shows that the eight Oasis test invoices were ultimately Accepted, but this does not independently prove the final status of every invoice in the customer's original portal upload.

SLA: First response by 10:05; resolution target 17:05 on 16 November.

4.2 Marina Bay Hospitality – Production API Incident

Alert received: Monday, 16 November 2026, 09:42 +0400
Customer impact reported: 09:48 +0400
Priority: P1

Acknowledgement / escalation communication draft

Subject: Production API degradation – P1 incident under investigation

Hello Priya,

We have acknowledged the production API alert and the reported customer impact, including 502 responses and invoices remaining in the Validated state.

This is being treated as a P1 incident. The on-call engineering team needs to investigate the API and invoice-submission path immediately. We are also checking the affected invoice states to avoid recommending a resend before confirming whether any invoices have already been submitted.

I will continue to own customer communication and provide the next update by 10:30 AM Gulf time, or sooner if we confirm a material change.

Regards,
Product Support Team

Priority justification: P1 because the alert reports repeated production API restarts, multiple customers are reportedly seeing 502 responses, and invoices are not progressing to ASP submission. The upcoming Marina Bay Hospitality go-live on 18 November increases the business risk.

SLA: First response deadline 10:12; resolution target 13:42; update cadence every 30–60 minutes. L3 on-call escalation is immediate.

4.3 Al Noor Fuel & Lubricants LLC

Received: Monday, 16 November 2026, 10:15 +0400
Priority: P2

Acknowledgement draft

Subject: Re: BR-CO-15 invoice rejections – November VAT review

Hello Fatima,

Thank you for sharing the rejection details. We have started reviewing the accepted and rejected invoice records to identify the exact validation differences.

The initial evidence shows that some invoices were accepted while three invoices in the test batch were rejected under BR-CO-15. We are comparing the submitted and expected totals for each rejected invoice so that we can provide specific correction guidance rather than suggesting changes to the entire file.

I will share the findings and recommended next steps by 11:15 AM Gulf time.

Regards,
Product Support Team

Priority justification: P2 because a subset of invoices is rejected during an active VAT audit review, while other invoices in the same test batch were accepted.

SLA: First response by 11:15; resolution target 08:15 on 17 November.

4.4 BluePeak ERP Integrations / Sahara Medical Supplies FZE

Received: Monday, 16 November 2026, 11:30 +0400
Priority: P2

Acknowledgement draft

Subject: Re: Invoice submission status – Sahara Medical Supplies

Hello Daniel,

Thank you for holding off on resending the invoices. That is the correct precaution while we verify their current submission state.

We are tracing the invoice records against the submission responses and checking why some invoices remain in Submitted without an ASP reference. We will not recommend a resend until we establish whether the invoices were accepted by the ASP or whether a safe retry is possible.

I will provide the verified status and next steps by 12:30 PM Gulf time.

Regards,
Product Support Team

Priority justification: P2 because 40 invoices were sent, but the buyer has received only some of them. The payment run this week makes the issue time-sensitive, and an unsafe resend could create duplicate submissions.

SLA: First response by 12:30; resolution target 09:30 on 17 November.

4.5 Ghaf Tree Café LLC

Received: Monday, 16 November 2026, 12:10 +0400
Priority: P3

Acknowledgement draft

Subject: Re: Invoice date format validation

Hello Hessa,

Thank you for contacting us. We checked the date-format issue and found that the import currently expects dates in YYYY-MM-DD format or a supported Excel date value.

For example, please enter 2026-11-13 instead of 13/11/2026. You can correct the date column and upload the file again.

Please keep the original file so that we can compare the corrected upload if the issue continues. I will follow up with you by 4:10 PM Gulf time.

Regards,
Product Support Team

Priority justification: P3 because the issue affects one customer's import format and has a clear workaround. All four invoices in the test batch were rejected, but the issue does not currently indicate a platform-wide outage.

SLA: First response by 16:10; resolution target 10:10 on 19 November, based on 24 business hours.

5. Detailed Investigations

The following incidents were selected for deeper investigation because the available evidence allowed meaningful tracing across application behaviour, database states, logs, and validation results.

5.1 Oasis Builders Supply LLC – Slow Import and Duplicate Warnings

Priority: P2
Customer impact: November month-end close may be delayed. Repeated retries create uncertainty about which invoices have already been processed.

Investigation log

Step

Investigation performed

Observation

Result

1

Reviewed the Oasis investigation output

Eight test invoice records were present

Established the test-batch scope

2

Checked invoice statuses

All eight test invoices were Accepted

Confirmed the outcome of the test batch

3

Reviewed the load-test output

Oasis test import completed in approximately 121.5 seconds

Confirmed that the import path was slow in the test

4

Compared the direct API test with the reported portal behaviour

The direct API test completed, but the customer reported a portal timeout and retry behaviour

The direct test does not reproduce the portal proxy path

5

Considered the duplicate warnings

Existing invoice numbers were reported after repeated attempts

Re-uploading without checking existing records is unsafe

Root cause and evidence

What is established:

The eight Oasis test invoices were Accepted.

The test import took approximately 121.5 seconds.

The customer reported that the portal remained in Processing and that repeated retries resulted in “already exists” messages.

Likely technical explanation:

The available evidence is consistent with a long-running synchronous import path interacting poorly with the portal or proxy timeout. If the portal request times out while backend processing continues, a customer may retry even though the original import is still progressing. That can lead to duplicate warnings when the same invoice numbers are submitted again.

This is a probable explanation, not a fully reproduced production root cause. The direct API load test bypassed the web proxy, so it does not independently prove the exact timeout experienced by Rashid.

Evidence:

evidence/investigations/oasis-builders.txt

evidence/environment/load-test.txt

Relevant import-processing implementation should be reviewed in the repository before adding exact source line references.

Confidence: Medium for the long-running import behaviour; lower for the precise portal/proxy timeout mechanism.

Open questions:

What was the exact import ID for Rashid's original upload?

Which invoice numbers were Accepted, Rejected, or still Processing in the customer's actual upload?

Did the original request continue processing after the portal timed out?

What timeout value was applied at the portal/proxy layer?

Customer resolution reply draft

Hello Rashid,

We reviewed the test evidence for the November import path. The eight invoices in the test batch reached Accepted status, although the import took approximately 121.5 seconds.

Because your original upload may continue processing after the portal stops waiting for the request, please do not upload the full file again. First, we need to reconcile the original import ID and invoice numbers against their current statuses.

Once we confirm which invoices are already Accepted, we can isolate only the invoices that genuinely require another submission. This avoids unnecessary duplicate attempts.

We will share the invoice-level reconciliation as soon as the original import records are verified.

Regards,
Product Support Team

Workaround offered

Do not retry the complete file.

Identify the original import ID.

Reconcile invoice numbers against their current statuses.

Resubmit only invoices confirmed as not processed, after verifying that the retry is safe.

Prevention ideas

Make long-running imports asynchronous and expose a durable import ID with a status endpoint.

Add idempotency protection so repeated uploads cannot create duplicate invoice submissions.

Display invoice-level progress instead of leaving the customer with only a generic Processing state.

Provide clear messaging when a request times out but backend processing may still continue.

Monitor import duration and the number of imports remaining in Processing.

5.2 Al Noor Fuel & Lubricants LLC – BR-CO-15 Rejections

Priority: P2
Customer impact: Three invoices in the test batch were rejected while the customer was preparing for a VAT audit review.

Investigation log

Step

Investigation performed

Observation

Result

1

Reviewed the Al Noor investigation output

Six invoice records were available

Established the batch size

2

Compared invoice statuses

Three Accepted and three Rejected

Confirmed a partial failure

3

Inspected BR-CO-15 rejection details

Rejected invoices had differences between calculated and expected totals

Identified the validation area

4

Compared the three rejected invoice amounts

Differences were between 0.01 and 0.02

Indicates a small rounding or calculation discrepancy

5

Compared accepted invoices

Other invoices in the same test batch were Accepted

The issue is not a blanket rejection of the entire batch

Invoice-level evidence

Invoice

Status

Calculated / received amount

Expected amount

Difference

0041

Accepted

—

—

—

0042

Rejected

15,022.42

15,022.43

0.01

0043

Accepted

—

—

—

0044

Rejected

11,817.57

11,817.59

0.02

0045

Accepted

—

—

—

0046

Rejected

5,610.99

5,611.00

0.01

Root cause and evidence

The evidence confirms that the three rejected invoices failed BR-CO-15 because the calculated totals did not match the expected totals.

The small differences suggest a rounding or calculation inconsistency between the submitted invoice values and the validation calculation. However, the available evidence does not establish exactly which upstream calculation produced the discrepancy. I would not classify a specific tax-calculation function as the confirmed cause without tracing the original invoice payload and calculation logic.

Evidence:

evidence/investigations/al-noor-fuel.txt

The invoice-level BR-CO-15 rejection amounts recorded in the investigation output.

Confidence: High that the three invoices failed because of total mismatches; medium that rounding inconsistency is the underlying cause.

Open questions:

What were the original line-level taxable amounts and tax rates?

Were amounts rounded per line or only at invoice level?

Did the ERP and portal use the same decimal precision and rounding method?

Can the original payload be compared with the ASP validation response?

Customer resolution reply draft

Hello Fatima,

We reviewed the six invoice records in the test batch. Three were Accepted, while invoices 0042, 0044, and 0046 were rejected under BR-CO-15.

The rejected records show the following differences:

Invoice 0042: 15,022.42 received against 15,022.43 expected.

Invoice 0044: 11,817.57 received against 11,817.59 expected.

Invoice 0046: 5,610.99 received against 5,611.00 expected.

These differences point to a total-calculation or rounding mismatch. Please have your ERP team compare the line-level taxable amounts, tax calculations, and final invoice totals for these three invoices.

Please do not change the three invoices that were already Accepted. Once the rejected invoice values are corrected and verified, we can review the resubmission outcome.

Regards,
Product Support Team

Workaround offered

Correct only the three rejected invoices.

Reconcile line-level taxable values, tax amounts, and final totals.

Keep the three Accepted invoices unchanged.

Revalidate the corrected invoices before resubmission.

Prevention ideas

Use a consistent decimal precision and rounding strategy across ERP, portal, and ASP calculations.

Add pre-submission checks for invoice-level totals.

Provide clearer validation messages showing the calculated and expected amounts.

Maintain a test suite for boundary cases involving fractional tax amounts and rounding.

Compare the ERP-generated totals against the portal's calculation before bulk submission.

5.3 BluePeak ERP Integrations / Sahara Medical Supplies – HTTP 429 and Stuck Submitted Invoices

Priority: P2
Customer impact: 32 of the 40 invoices in the test batch remained Submitted, and the buyer had received only some invoices.

Investigation log

Step

Investigation performed

Observation

Result

1

Reviewed the Sahara load-test result

40 invoices were tested; 8 Accepted and 32 Submitted

Confirmed the status distribution

2

Reviewed invoice-level API response evidence

32 invoice-specific HTTP 429 responses were recorded

Identified rate-limit responses

3

Inspected the application's submission response switch

The inspected cases covered 200, 422, 500, 502, 503, and 504; no 429 case was present

Identified a handling gap

4

Inspected retry-state rules

Retry was allowed for Validated or Failed states, not Submitted

Explained why these invoices cannot simply use the existing retry path

5

Compared the evidence

32 invoice-level 429 responses and 32 Submitted records were observed in the test batch

Strong evidence of a relationship between the rate-limit responses and the stuck state

Root cause and evidence

Finding: The test evidence shows 32 invoice-specific HTTP 429 responses for the Sahara batch. The inspected submission response switch does not contain an explicit 429 handling case. The retry path also does not permit invoices in Submitted state.

This creates a clear handling gap: rate-limited submissions are not being processed through an explicit retry or recovery path, while the invoices remain in a state that the current retry endpoint does not accept.

The evidence strongly supports this explanation for the test batch. The exact ASP-side recovery behaviour and whether every 429 response included a Retry-After header still need confirmation.

Evidence:

evidence/bluepeak-sahara/api-429.log

evidence/bluepeak-sahara/sahara-429-only.txt

evidence/api-429-switch.txt

evidence/api-429-handler-check.txt

evidence/api-429-runtime-check.txt

evidence/retry-state-check.txt

evidence/investigations/sahara.md

evidence/environment/load-test.txt

Confidence: High that 429 responses were not explicitly handled in the inspected response switch and that Submitted invoices were not eligible for the existing retry path.

Open questions:

Did the ASP return a Retry-After header?

Were any of the 32 invoices accepted asynchronously after the initial 429 response?

Does the ASP provide a status lookup endpoint for reconciliation?

Is there a safe recovery operation for Submitted invoices after checking ASP status?

Customer resolution reply draft

Hello Daniel,

We reviewed the Sahara test batch and confirmed that 40 invoices were processed through the test path. Eight reached Accepted status, while 32 remained Submitted.

We also found 32 invoice-specific HTTP 429 responses in the submission evidence. This indicates that the ASP or its submission endpoint applied rate limiting. The application currently does not have an explicit 429 handling branch in the inspected submission response logic, and the existing retry path does not accept invoices in Submitted state.

Please continue to hold off on resending the 32 invoices. We first need to reconcile their status with the ASP to confirm that none has already been accepted. Once that check is complete, the affected invoices can be recovered through a controlled retry process.

Regards,
Product Support Team

Workaround offered

Do not resend the 32 Submitted invoices blindly.

Reconcile each invoice against the ASP using its invoice reference or available status lookup.

Separate invoices confirmed as accepted from those confirmed as not accepted.

Retry only records that are safe to retry, using an approved recovery procedure.

Prevention ideas

Add explicit HTTP 429 handling to the submission response logic.

Respect the Retry-After response header when provided.

Use bounded exponential backoff with jitter to avoid repeated rate-limit bursts.

Introduce a recoverable state for rate-limited submissions rather than leaving them indefinitely Submitted.

Add an ASP reconciliation process before retrying ambiguous submissions.

Monitor the 429 response rate and the age of invoices remaining in Submitted.

Add automated tests for 429 handling, retry eligibility, and duplicate prevention.

5.4 Ghaf Tree Café LLC – Unsupported Date Format

Priority: P3
Customer impact: All four invoices in the test batch were rejected because the submitted date format was not accepted.

Investigation log

Step

Investigation performed

Observation

Result

1

Reviewed the Ghaf test result

Four invoices were rejected

Confirmed the test-batch outcome

2

Inspected the date parsing implementation

Parser accepts ISO date format and supported Excel serial values

Identified supported input formats

3

Inspected the validation path

Invalid date parsing leads to date validation rejection AE-BR-04

Connected parser behaviour to validation

4

Compared the reported input format

Customer used 13/11/2026

Identified the format mismatch

5

Evaluated a correction

Convert to 2026-11-13 or use a supported Excel date cell

Identified a practical workaround

Root cause and evidence

The parser expects dates in YYYY-MM-DD format or supported Excel date values. The customer supplied 13/11/2026, which is a DD/MM/YYYY string and is not accepted by the current parser path.

The validation layer then rejects the affected invoices under AE-BR-04.

Evidence:

evidence/investigations/ghaf-cafe.md

evidence/investigations/ghaf-cafe-code-evidence.txt

evidence/environment/load-test.txt

Relevant implementation files:

services/api/internal/sheet/sheet.go

services/api/internal/validate/validate.go

Confidence: High for the parser's supported-format behaviour and the available workaround. The original customer file should still be checked to confirm the exact cell values before closing the live customer incident.

Customer resolution reply draft

Hello Hessa,

We identified the date-format issue. The import currently accepts dates in YYYY-MM-DD format or supported Excel date values.

Please change the date from:

13/11/2026

to:

2026-11-13

You can then upload the corrected file again. There is no need to change the other invoice details unless the portal reports another validation issue.

If the corrected upload is still rejected, please share the new import result so that we can check the specific validation message.

Regards,
Product Support Team

Workaround offered

Convert dates to ISO format (YYYY-MM-DD) or enter them as valid Excel date cells before uploading the file.

Prevention ideas

Display the accepted date format beside the upload field.

Validate date formats before starting the import.

Show the exact row and column containing an invalid date.

Consider supporting unambiguous additional date formats with explicit locale rules.

Add sample files and import guidance for small businesses that do not have dedicated accounts or IT teams.

5.5 Marina Bay Hospitality – Production API Degradation

Priority: P1
Customer impact: Multiple customers reportedly experienced 502 responses, and 11 invoices remained Validated without a corresponding Sent to ASP event.

Investigation log

Step

Investigation performed

Observation

Result

1

Reviewed the production alert

Three API restarts were reported within 15 minutes

Confirmed a critical infrastructure signal

2

Reviewed the customer impact report

Multiple customers reported 502 responses

Established multi-customer impact

3

Checked the affected invoice records

11 invoices remained Validated

Identified the stuck processing state

4

Reviewed invoice event history

11 invoices had Parsed and Validated events but no Sent to ASP event

Narrowed the investigation to the submission stage

5

Inspected current API container state

RestartCount was 2; OOMKilled was false; ExitCode was 0; container was currently running

No evidence that the inspected container had been OOM-killed

6

Reviewed available API logs

No matching error was found in the available log sample

Root cause remains unconfirmed

Findings and root cause status

The evidence confirms that 11 invoices reached Validated but had no corresponding Sent to ASP event in the inspected event history.

The current container inspection shows RestartCount 2, OOMKilled false, ExitCode 0, and a currently running container. This does not establish the cause of the earlier restart alert. The alert reported three restarts in a 15-minute window; the inspected RestartCount is a separate container-level value and should not be treated as a direct confirmation of that alert's exact time window.

Root cause: Not confirmed.

The next investigation should focus on the submission worker, queue health, ASP dispatch logs, and the relationship between API restarts and invoice processing. This incident must remain open until L3 identifies why validated invoices did not progress to submission.

Evidence:

evidence/investigations/marina-bay-findings.md

evidence/investigations/marina-bay-findings.txt

evidence/investigations/marina-validated-invoices.txt

evidence/investigations/marina-container-inspect.txt

evidence/investigations/marina-container-state.json

evidence/investigations/marina-docker-events.txt

evidence/investigations/marina-api-errors.txt

evidence/investigations/marina-api-last-100-lines.txt

evidence/api-startup-timeline.txt

evidence/api-container-state.json

Confidence: High that the 11 inspected invoices had not recorded a Sent to ASP event. Low regarding the underlying cause, because the available evidence does not identify a confirmed failure in the worker, queue, or ASP dispatch path.

L3 escalation note

Summary: P1 production API degradation; multiple customers reportedly receiving 502 responses, with 11 invoices remaining Validated and no Sent to ASP event.

Priority / business impact: P1. Production impact across multiple customers. Marina Bay Hospitality has a planned go-live on 18 November.

Reported by: Grafana alert and CSM Priya Nair. Alert at 09:42; customer impact reported at approximately 09:48, 16 November 2026 +0400.

Affected scope: 11 inspected invoices in Validated state without a Sent to ASP event. Multiple customers reported 502 responses. Exact total production impact requires further confirmation.

Evidence reviewed: Invoice event history, API container inspection, Docker events, available API logs, and restart-window evidence. See the evidence paths listed above.

What has been ruled out / not established:

The inspected container was not OOM-killed.

The inspected container was currently running.

The available log sample did not show a matching error.

These observations do not rule out a worker, queue, transient infrastructure, or ASP dispatch issue.

Suspected cause: Submission worker or queue/dispatch processing issue. Confidence is low; this is a direction for investigation, not a confirmed RCA.

Workaround: Do not resend the 11 invoices until their ASP status has been reconciled. Maintain the affected invoice list and provide customer updates while L3 investigates.

L3 request:

Inspect submission worker logs around 09:30–10:00 +0400.

Check queue depth, consumer health, and failed or delayed jobs.

Correlate API restart events with invoice submission events.

Check ASP dispatch requests and responses for the affected invoice IDs.

Confirm whether the 11 invoices can be safely recovered without duplicate submission.

Identify the cause of the reported 502 responses and provide a recovery plan.

Next customer update: 10:30 AM Gulf time, then every 30–60 minutes until the P1 is stabilised.

Customer update draft

Hello Priya,

We have confirmed that 11 inspected invoices reached the Validated state but do not have a corresponding Sent to ASP event in the available event history.

The API container is currently running, and the inspected container state does not show an OOM kill. However, the available evidence has not yet established the cause of the earlier restart alert or the missing submission events.

We have escalated this as a P1 for immediate investigation of the submission worker, queue, and ASP dispatch path. We are also holding off on any resend recommendation until the affected invoice statuses are reconciled.

I will provide the next update by 10:30 AM Gulf time.

Regards,
Product Support Team

Prevention ideas

Alert on invoices remaining Validated beyond an agreed processing threshold.

Monitor queue depth, worker availability, and submission latency.

Correlate API restart events with invoice processing events.

Add dashboards for Parsed → Validated → Submitted → Accepted transitions.

Ensure failed or abandoned jobs are recoverable and visible to support.

Maintain invoice-level ASP reconciliation before any manual resend.

6. Incidents Not Fully Resolved

The investigation evidence does not justify claiming that all five customer issues are fully resolved.

Incident

Current conclusion

Remaining work

Oasis Builders

Eight test invoices Accepted; slow import behaviour observed

Reconcile the customer's original import ID and confirm the portal/proxy timeout path

Marina Bay

11 invoices lack Sent to ASP events; root cause unconfirmed

L3 investigation of worker, queue, API restarts, and ASP dispatch

Al Noor Fuel

Three BR-CO-15 rejections confirmed with amount differences

Inspect original line-level values and identify the exact rounding calculation

BluePeak / Sahara

32 HTTP 429 responses and missing explicit handling identified

Confirm ASP status, Retry-After behaviour, and safe recovery

Ghaf Tree Café

Unsupported date format identified; workaround available

Confirm corrected customer upload succeeds

A test result is not the same as a confirmed production fix. I would close each live customer incident only after verifying the affected invoice outcomes or agreeing on a safe workaround.

7. Overall Reflections

7.1 How I prioritised the incidents

I did not prioritise based only on the order in which messages arrived.

I used three main questions:

Can invoices reach the ASP? If production invoices are not progressing, the impact is more serious than a single input-format rejection.

How broad is the impact? A multi-customer production issue takes priority over a single customer's file-format issue.

What is the business consequence? Month-end close, VAT audit work, payment runs, and a planned go-live all affect urgency.

This is why the Marina Bay production issue was P1, while Oasis, Al Noor, and Sahara were P2, and Ghaf was P3.

7.2 What I would do differently

Establish a consistent incident timeline before running multiple unrelated checks.

Capture import IDs and invoice IDs at the start of every investigation.

Preserve the original API response, payload, and ASP reference for every affected invoice.

Separate confirmed facts from suspected explanations in the investigation notes.

Avoid retrying invoices in ambiguous states until their ASP status is reconciled.

Record the exact source-code line references alongside the evidence when preparing the final technical RCA.

Provide the customer with a specific next-update time instead of saying that the issue is being investigated.

7.3 What I learned

Technical learning

An HTTP 201 from an import endpoint does not prove that every invoice was accepted by the ASP.

Invoice lifecycle states must be checked individually.

HTTP 429 requires explicit rate-limit handling and a safe retry strategy.

A retry mechanism must account for ambiguous submission states to prevent duplicates.

Date parsing and tax-total validation can cause invoice-level rejections even when other invoices in the same batch succeed.

Container restart counts, Docker events, application logs, and invoice event histories provide different pieces of evidence; they should not be treated as interchangeable.

Support-process learning

L1 owns customer communication even when a technical issue is escalated.

A good acknowledgement should communicate the next action and a clear update deadline.

A workaround must be safe, not merely quick.

A useful escalation includes customer impact, affected IDs, evidence, actions already taken, suspected cause, confidence, and a specific request to L2/L3.

A case should be closed based on verified customer outcome, not simply because an engineering team has been notified.

7.4 What I need to learn further

Deeper debugging of distributed queues and asynchronous workers.

ASP integration behaviour, rate limits, and retry contracts.

Invoice lifecycle and idempotency design.

Tax calculation and decimal rounding rules.

Production observability using structured logs, metrics, traces, and alert correlation.

Building effective customer-facing incident updates during P1 and P2 incidents.

8. Evidence Index

All evidence referenced in this report is organised under evidence/.

Evidence area

Files / directory

Purpose

Environment baseline

evidence/environment/

Container state, initial logs, database snapshot, load-test output

Oasis

evidence/investigations/oasis-builders.txt

Invoice status and test investigation

Al Noor

evidence/investigations/al-noor-fuel.txt

Accepted/rejected invoice status and BR-CO-15 amount differences

Sahara

evidence/bluepeak-sahara/ and evidence/investigations/sahara.md

Invoice-specific 429 responses and batch investigation

429 handling

evidence/api-429-switch.txt, evidence/api-429-handler-check.txt, evidence/api-429-runtime-check.txt

Application response handling checks

Retry state

evidence/retry-state-check.txt

Retry eligibility evidence

Ghaf

evidence/investigations/ghaf-cafe.md, evidence/investigations/ghaf-cafe-code-evidence.txt

Date parser and validation evidence

Marina Bay

evidence/investigations/marina-* and evidence/api-*

Invoice event history, container state, restart and log investigation

Load testing

evidence/environment/load-test.txt

Test batch outcomes and performance observations

The evidence directory is intended to allow a reviewer to trace each important conclusion back to an actual command output, database result, log, or source-code inspection.

9. Real-Time Work Log

Fill this section with the actual time spent. Do not estimate or invent durations.

Activity

Actual start time

Actual end time

Time spent

Repository setup and initial environment review

[Fill]

[Fill]

[Fill]

SLA review and incident prioritisation

[Fill]

[Fill]

[Fill]

Oasis investigation

[Fill]

[Fill]

[Fill]

Al Noor investigation

[Fill]

[Fill]

[Fill]

Sahara / HTTP 429 investigation

[Fill]

[Fill]

[Fill]

Ghaf date-validation investigation

[Fill]

[Fill]

[Fill]

Marina Bay P1 investigation and escalation

[Fill]

[Fill]

[Fill]

Customer communication drafts and final report

[Fill]

[Fill]

[Fill]

Total





[Fill actual total]

10. AI Usage Disclosure

I used ChatGPT as an assistance tool during this exercise for:

Structuring the investigation approach and prioritisation.

Reviewing the SLA calculations and incident response expectations.

Interpreting technical evidence from application logs, database outputs, and source-code checks.

Organising investigation findings into a clear incident report.

Drafting customer acknowledgements, customer updates, and escalation notes.

Reviewing the report for clarity and consistency.

The technical conclusions in this report are based on the available repository evidence, command outputs, database results, logs, and source-code inspections. AI assistance was used to help organise and explain the findings; it was not treated as a replacement for technical verification.

Where evidence did not establish a definitive cause, I have explicitly marked the conclusion as suspected or unconfirmed rather than presenting an assumption as fact.

11. Final Submission Checklist

Replace the repository link with my own GitHub repository URL.

Enter the actual submission date.

Enter the actual total time spent.

Complete the real-time work log using actual timings.

Confirm that all evidence files referenced above are present in the repository.

Confirm that SUBMISSION.md is present at the repository root.

Review the README and ensure the repository setup instructions are clear.

Push the final changes to my GitHub repository.

Submit the GitHub URL through the provided Microsoft submission form using the same email address used for the job application.

Submission form: https://forms.cloud.microsoft/r/ARFdqdpsdP

Closing note

This exercise reinforced that Product Support is not just about identifying an error or forwarding a ticket to engineering. It is about understanding the customer's business impact, tracing the issue through the system, communicating clearly, protecting customer data from unsafe retries, and maintaining ownership until there is a verified outcome.

My approach throughout this exercise was to distinguish what the evidence proves from what still needs investigation, and to provide the customer with a safe and actionable next step.
