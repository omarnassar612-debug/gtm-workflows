# 3. Post-consultation follow-up cadence

**Platform:** Zoho CRM (Deals module), Deluge, Workflow Rules
**Status:** Production. Extended from an 8-touch, 14-day cadence to 12 touches over 90 days.

## Problem

Homeowners rarely decide at the consultation. Follow-up depended on each advisor remembering to call, and what they said varied by person.

## Trigger

A Workflow Rule fires when a deal's **Stage** changes to the post-consultation stage ("Consult Completed"). It calls a Deluge custom function and passes the deal ID.

## Actions

The function creates the whole cadence as CRM tasks on the deal, assigned to the deal owner, each with a due date and the touch's talk track in the task description. See [`deluge/start_followup_cadence.deluge`](deluge/start_followup_cadence.deluge).

The advisor opens their task list each morning and works it. Nothing sends automatically. These touches are calls, voicemails and personal texts and emails from the advisor.

## Cadence structure

Front-loaded in the first week, tapering after that.

| Touch | Day | Purpose |
|---|---|---|
| 1 | 0 | Recap and thank-you, outcome-focused |
| 2 | 1 | Curious check-in call, voicemail if no answer, text 30 minutes later |
| 3 | 3 | Outcome email with objection answers, same-day call |
| 4 | 5 | Short text that surfaces the real hold-up |
| 5 | 7 | Call: what does it mean if the project is not done by season end |
| 6 | 10 | Pattern-interrupt message to a silent prospect |
| 7 | 12 | Direct call with honest scheduling urgency |
| 8 | 14 | Close-the-file message that leaves the door open |
| 9 to 12 | to day 90 | Lower-pressure, value-led nurture touches |

Days for touches 1 to 8 are the original 14-day cadence. Later touches are set in the production function and are not reproduced here.

Deals stay in a future-timeline stage after day 14. "Lost" is reserved for day-90 non-responders or an explicit no.

## Messaging principles

Scripts follow value-first selling (Hormozi) and question-led conversations (NEPQ):

- Lead with the prospect's outcome, not the product.
- Ask questions instead of making statements. Questions lower resistance.
- Urgency only when it is real, such as an actual install slot.
- Give people a clean way out. It keeps the door open.

## Human review

The advisor does every touch. Closing out tasks when a deal is sold is done by the advisor.

## Deluge lessons (from five iterations)

- Build the owner map as its own variable before putting it in a task record. Nested `map().put()` inside `put()` is rejected.
- Deluge has no ternary operator. Use `if` / `else`.
- Keep string literals and comments to plain ASCII. Em dashes, smart quotes and symbols caused line-level syntax errors.
- The declared function name must match the API name shown in the CRM UI.
- A task linked to a deal needs `$se_module` set to `"Deals"` and `$related_to` set to the deal ID.
