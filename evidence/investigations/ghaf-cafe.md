# Ghaf Cafe - Investigation

## Priority
P3 - Normal

## Customer impact
All 4 invoices in the load test were rejected due to date validation.

## Investigation performed
1. Ran the load test and recorded the four rejected invoices.
2. Inspected the date parser in services/api/internal/sheet/sheet.go.
3. Confirmed ParseDate accepts YYYY-MM-DD dates and valid Excel date serial numbers.
4. Inspected services/api/internal/validate/validate.go.
5. Confirmed invalid issue dates and due dates trigger validation rule AE-BR-04.

## Root cause
The supplied DD/MM/YYYY text date format is not supported by the current date parser. The parser accepts ISO dates (YYYY-MM-DD) and valid Excel date serial numbers.

## Workaround
Convert dates to YYYY-MM-DD format or provide valid Excel date cells before uploading.

## Prevention
- Validate date formats before upload.
- Display a clear example of the accepted date format.
- Consider supporting additional unambiguous date formats.

## Customer reply
Hi Ghaf Cafe Team,

We reviewed the rejected invoices and identified a date-format validation issue. Please use YYYY-MM-DD format for dates, or provide dates as valid Excel date cells, and retry the affected invoices. Please contact us if the issue continues.

## Evidence
- ../environment/load-test.txt
- ghaf-cafe-code-evidence.txt
- Source: services/api/internal/sheet/sheet.go
- Source: services/api/internal/validate/validate.go

## Limitation
The code confirms the accepted date formats and validation rule. The load-test output confirms four rejections. Retain the original invoice input or validation output if available to independently verify the exact date values supplied.
