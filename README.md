# GTM Workflows

Revenue workflows I designed and built for a home-services sales team: lead intake, qualification, appointment reminders, post-consultation follow-up, reporting, and a Claude-assisted account research workflow.

Each folder documents one workflow: the **trigger**, the **actions**, the **human review points**, and the **result**. Code is included where it is mine to share.

Employer, client names, CRM record IDs, webhook URLs and customer data are removed. Message copy is replaced with the structure of each touch.

## Status of each workflow

I separate what ran in production from what is designed or in progress.

| # | Workflow | Platform | Status |
|---|---|---|---|
| 1 | [Lead intake, qualification, lifecycle](workflows/01-lead-intake-lifecycle/) | Zoho CRM, Zoho Flow, RingCentral | Production. Measured. |
| 2 | [Consultation confirmations and reminders](workflows/02-consultation-reminders/) | Zoho CRM workflow rules, Zoho Flow, Deluge | Confirmation path in production and verified end to end. Timed reminders and cancellation notice designed and templated. |
| 3 | [Post-consultation follow-up cadence](workflows/03-post-consultation-cadence/) | Zoho CRM, Deluge | Production. |
| 4 | [Reporting and CRM data quality](workflows/04-reporting-and-data-quality/) | Zoho Analytics, COQL, Looker Studio, BigQuery, Excel | Production. |
| 5 | [Claude-assisted account research and outreach](workflows/05-claude-account-research/) | Claude, Claude in Chrome, LinkedIn, Meta Ad Library | Designed. Research phase run, first outreach batch drafted, nothing sent or measured yet. |

## Measured results (workflow 1 and 2, production system)

- Average first response in business hours: about 1.5 minutes. Including after-hours leads: 42 minutes.
- Appointment cancellations: about 50% down to about 10%. The new intake, appointment confirmations and scheduling coordination all contributed. The first-response text alone did not produce this.
- Lead-to-booking ratio by month: 58.6% (April) to 83.0% (July). Booking-to-close fell from 48.8% (April) to 20.2% (June) on the months that have mostly resolved. July is not final. That moved the coaching focus from intake to the appointment stage. Full table in workflow 4.

## Conventions used across workflows

- **Lifecycle stages are explicit.** A lead is always in exactly one stage, and every automation reads or writes the stage.
- **One field per message, not one picklist for everything.** Each reminder has its own timestamp field. Empty means send and stamp. Populated means skip. This replaced a multi-select picklist that failed on array formatting.
- **Flow owns sending and branching. Deluge does only what Flow's visual nodes handle badly** (phone normalization, date math, bulk task creation). Visual logic is easier for the next person to edit.
- **Humans approve anything outbound that is not a templated transactional message.**

## Tools

Zoho CRM, Zoho Flow, Deluge, Zoho Analytics, COQL, RingCentral, Looker Studio, BigQuery, Excel, Claude, Claude in Chrome.

## Notes on the code

Deluge files here are sanitized from the production versions. Secrets are read from organization variables instead of being written into the script. Treat them as reference implementations: review field API names for your own CRM before deploying.
