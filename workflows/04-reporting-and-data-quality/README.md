# 4. Pipeline reporting and CRM data quality

**Platform:** Zoho Analytics, Zoho CRM (COQL), Looker Studio on BigQuery, Excel
**Status:** Production.

## Problem

Sales reviews ran on gut feel and on numbers that did not reconcile. Counts of leads, appointments and closes changed depending on who pulled them and how.

## What I built

**Daily KPI board (Zoho Analytics).** One table synced from the CRM tracking Leads Created, Leads Converted, Deals Created, Consults Booked and Consults Held. Daily, weekly, month-to-date and year-to-date views are emailed at end of day.

**Acquisition and conversion reports (Looker Studio on BigQuery, and Excel).** Used in weekly pipeline reviews to decide where follow-up effort goes.

**Monthly funnel (COQL and Excel).** Deal records created in each month, followed to their current stage. Closes are classified by current stage, not by a close date, because the sold-date field only exists from late May. Bookings are consult events created in the same month, so lead to booking is a ratio of two counts, not a strict follow of the same leads. Raw counts stay in the CRM and are not published here.

| Month | Lead to booking | Booking to close | Lead to close |
|---|---|---|---|
| April | 58.6% | 48.8% | 28.6% |
| May | 71.2% | 34.0% | 24.2% |
| June | 70.7% | 20.2% | 14.3% |
| July | 83.0% | 13.7% (not final) | 11.4% |

Closed means the deal currently sits in Sold - Pending Ops Approval, Ops Approved, Ready for Install, In Production, Completed - Pending Closeout, Invoice Sent or Paid & Closed. Lost includes Disqualified. Everything else is open.

Lead to booking rose 24 points from April to July. Booking to close fell from 48.8% to 20.2% across the April to June cohorts, which have almost fully resolved. More prospects reached an advisor and a smaller share bought, so the coaching focus moved from intake to the appointment stage.

Two limits on the read. July is not final: about two thirds of its deals were still open when this was pulled, and on June's aging pattern it lands nearer 20% than 13.7%. April is flattered: its closes include repair jobs, from a month with heavy repair quoting. August onward has not been calculated here yet. The queries in [`coql/monthly_funnel_by_stage.coql`](coql/monthly_funnel_by_stage.coql) produce it.

## Data quality findings

| Finding | Effect |
|---|---|
| COQL excludes converted leads by default | A one-month lead count read about 42% of the real number. Converted leads must be queried with an explicit filter and combined. |
| Test and mystery-shopper records in the lead table | About 16% of the month's leads were test, contractor or mystery-shopper records, removed before counting. The exclusion keys on the disqualification reason field. |
| Appointment counts built from deal creation | Deal-creation counts overstated appointments by about 24% against the Events module. Rebuilt from Events. |
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
