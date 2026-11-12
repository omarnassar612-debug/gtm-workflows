# 2. Consultation confirmations and reminders

**Platform:** Zoho CRM (Events module, workflow rules with scheduled actions), Zoho Flow, Deluge, RingCentral SMS, Zoho Mail
**Status:** Confirmation path: production, verified end to end. Timed reminders and cancellation notice: designed and templated; check the live state of each rule before relying on them.

## Problem

Booked in-home consultations were cancelled or missed at a high rate, and rescheduling broke any reminder that was scheduled against the original time.

## Design

| Touch | When | Channel |
|---|---|---|
| Confirmation | On booking | SMS + email |
| Day-before | 9am the day before | SMS |
| Morning-of | 10am the day of | SMS |
| One-hour | One hour before start | SMS |
| On my way | Triggered manually by the advisor | SMS |
| Cancellation notice | On cancel | SMS + email |

### Trigger

Three CRM workflow rules on the Events module: **created**, **edited** and **cancelled**. Timed reminders are **scheduled actions** on the same rules, set relative to `Start_DateTime`.

Scheduled actions replaced an earlier design that polled the CRM every 15 minutes with COQL. Polling spent API calls every cycle and handled rescheduling badly. With scheduled actions, an edit to the meeting re-queues the reminders against the new time, so rescheduling needs no extra logic.

### Actions

1. On create or edit, a Deluge function copies phone and email from the linked Contact or Lead onto the event, normalizes the phone number to E.164, and posts a webhook payload to Flow. See [`deluge/populate_meeting_contact_info.deluge`](deluge/populate_meeting_contact_info.deluge).
2. Flow runs the decisions in order. Design: three stacked Decision nodes (is this a consultation, is it cancelled, has this message already been sent), not one multi-branch node.
3. Flow sends the SMS and email, then writes a timestamp to that touch's field.
4. On edit, the timestamp fields are cleared so the confirmation re-sends with the new date and time.

### De-duplication

Each touch has its own DateTime field:

`Confirmation_Sent_At`, `Day_Before_Sent_At`, `Morning_Of_Sent_At`, `One_Hour_Sent_At`, `On_My_Way_Sent_At`

If the field is empty, send and stamp. If populated, skip. An earlier version used one multi-select picklist (`Reminder_Status`). Writing to a multi-select from Flow needs a JSON array, and the formatting errors made the stamp-back unreliable. Five timestamp fields removed the problem.

### Webhook payload

Production payload fields: `first_name`, `phone`, `email`, `host_id`, `event_id`, `event_title`, `date`, `start_time`, `end_time`. The published function sends the first three. Add the rest where your Flow builds the message text.

### Gate

The function returns early for events with no linked contact or lead. Flow additionally checks the event is a client consultation, so internal meetings and calls do not trigger customer messages.

## Human review

The advisor sends the "on my way" text. Message templates are written and approved by a person. Nothing in this workflow generates message text on the fly.

## Lessons

- Reference the owner field from the fetched record node consistently. Swapping node references changes what the message shows.
- Keep Deluge for phone normalization and date math. Keep sending and branching in Flow so someone without Deluge skills can edit it.
