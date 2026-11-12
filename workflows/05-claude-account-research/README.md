# 5. Claude-assisted account research and outreach

**Platform:** Claude, Claude in Chrome, LinkedIn (Jobs and Premium), Meta Ad Library, Zoho Mail
**Status:** Designed. The research phase has been run and the first outreach batch drafted. Nothing has been sent, so there are no reply or booking results yet.

I mark what is designed, what ran, and what is measured, so nobody reads a design as a result.

| | |
|---|---|
| Designed | The workflow below |
| Ran | Research phase across LinkedIn Jobs and the Meta Ad Library. Output: a target list (CSV with a source link per company). First outreach batch drafted. |
| Measured | Nothing yet |
| Next | First batch sends. Track replies and booked calls by signal type. |

## Problem

Outbound to home-service and trades companies is slow when research is manual, and generic openers get ignored. The goal is to find companies with a visible reason to need the offer (managed lead intake and appointment setting), and open with that reason.

## Signals

An account qualifies only with a signal that can be verified and linked:

- **Advertising signal:** the company is running Google or Meta ads. They are paying for leads, so a slow or missed response costs them money.
- **Hiring signal:** the company has an open appointment-setter or intake job post. They are trying to solve the same problem with headcount.

## Workflow

1. **Batch.** Claude researches 25 companies at a time.
2. **Signal and source.** For each company, record the signal type and a source link to the ad or job post.
3. **Contacts.** Find a published business contact (company website, public listing). No guessing or inferring addresses.
4. **Score.** Score each account against a rubric. See [`scoring-rubric.md`](scoring-rubric.md).
5. **Draft.** Write an opener that cites the specific signal.
6. **Review.** I approve the batch, then approve every email (recipient, subject, body).
7. **Send.** I send. Claude does not send on its own.
8. **Log.** Record the send and any reply against the account row.

Claims that cannot be verified are excluded from the list and from the email.

## Human review gates

| Gate | Who decides |
|---|---|
| Which accounts stay in the batch | Me |
| Each email before it sends | Me |
| Sending | Me. Claude has no send access. |
| Any claim without a source link | Excluded |

## Output

A CSV per batch with one row per company and a source link for every claim. Schema: [`data/targets_schema.csv`](data/targets_schema.csv). Drafts sit beside it, one per account.

## Prompt template

See [`research-prompt.md`](research-prompt.md).
