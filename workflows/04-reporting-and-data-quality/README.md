# 4. Pipeline reporting and CRM data quality

**Platform:** Zoho Analytics, Zoho CRM (COQL), Looker Studio on BigQuery, Excel
**Status:** Production.

## Problem

Sales reviews ran on gut feel and on numbers that did not reconcile. Counts of leads, appointments and closes changed depending on who pulled them and how.

## What I built

**Daily KPI board (Zoho Analytics).** One table synced from the CRM tracking Leads Created, Leads Converted, Deals Created, Consults Booked and Consults Held. Daily, weekly, month-to-date and year-to-date views are emailed at end of day.

**Acquisition and conversion reports (Looker Studio on BigQuery, and Excel).** Used in weekly pipeline reviews to decide where follow-up effort goes.

**Cohort conversion analysis (COQL and Excel).** Leads grouped by the month they were created, then followed through booking and close.

| Cohort | Lead to booking | Booking to close (mature cohorts) |
|---|---|---|
| April | 58.6% | 48.8% |
| July | 83.0% | 20.2% |

Intake improved while close rate at the appointment fell. That moved the coaching focus from intake to the appointment stage. Cohorts, not monthly totals, are what exposed it: monthly totals blended old and new leads.

## Data quality findings

| Finding | Effect |
|---|---|
| COQL excludes converted leads by default | A one-month lead count read 95. The real number was 228. Converted leads must be queried with an explicit filter and combined. |
| Test and mystery-shopper records in the lead table | 36 records removed, leaving 192 qualified leads for the month. The exclusion keys on the disqualification reason field. |
| Appointment counts built from deal creation | 558 counted, 450 actual in-person appointments. Rebuilt from the Events module. |
| Recurring internal meeting series dominate raw event counts | Filter on the event title to isolate client consultations, then hand-classify rebookings, repair visits and test records. |
| Won deals move to a second pipeline | Filtering a single pipeline undercounts closes. Query terminal stages across all pipelines. |
| Some stage-timestamp fields only populate after a certain date | Querying earlier months returns sparse results. Do not use them for those periods. |

## COQL notes

- Query the unconverted and converted sets separately, then combine.
- Pull the disqualification reason as an array field and filter on it in code if COQL filtering is unreliable.
- Use `IN (...)` for notes across many parent records. `OR` in the WHERE clause did not work for the Notes module.
- Filter owner by ID, not by name.
- Long date ranges can truncate. Split into narrower windows.
- Examples: [`coql/`](coql/)

## Human review

Every number in a pipeline review is reconcilable to a query. Records removed from a count are listed, not silently dropped.
