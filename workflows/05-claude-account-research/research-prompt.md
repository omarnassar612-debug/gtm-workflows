# Research prompt template

Used to run one batch. Replace the bracketed parts.

```
You are researching outbound targets for [offer, one sentence].

Find [25] [type of company] in [region] with at least one of these signals:
- ADVERTISING: running Google or Meta ads right now (check the Meta Ad Library).
- HIRING: an open job post for an appointment setter, intake coordinator or similar.

For every company return one row with:
company, website, region, signal_type, signal_detail, signal_source_url,
contact_name, contact_title, contact_source_url, score, opener_draft

Rules:
- Every claim needs a source URL. If you cannot link it, leave the field empty and do not claim it.
- Contacts must be published (company site, public listing). Do not guess or construct email addresses.
- Skip any company you cannot verify the signal for.
- The opener must cite the specific signal in the first sentence and ask one question.
- Do not write anything that implies we have worked with the company before.
- Do not send anything. Return the table only.
```

## Notes

- Run in batches of 25 so each batch can be reviewed in one sitting.
- Check a sample of source URLs in each batch before approving it.
- Keep the openers short. One signal, one question.
