---
name: vbout-send-email-campaign
description: Create, schedule, and measure a VBOUT email campaign against lists or audiences.
api: openapi/vbout-openapi-original.json
operations:
  - get-EmailMarketing-GetLists
  - get-EmailMarketing-GetAudiences
  - post-EmailMarketing-AddCampaign
  - get-EmailMarketing-Campaigns
  - get-EmailMarketing-GetCampaign
  - get-EmailMarketing-Stats
---

# Send an email campaign with VBOUT

Base URL `https://api.vbout.com/1`; authenticate every call with the `key`
parameter. Respect the 15 req/s rate limit (429 + `x-rate-limit-after` on
breach).

## Steps

1. **Pick recipients** — `GET EmailMarketing/GetLists`
   (`get-EmailMarketing-GetLists`) and/or `GET EmailMarketing/GetAudiences`
   (`get-EmailMarketing-GetAudiences`); collect the IDs for the `lists` or
   `audiences` parameters (comma-separated).
2. **Create the campaign** — `POST EmailMarketing/AddCampaign`
   (`post-EmailMarketing-AddCampaign`). Required: `name`, `subject`,
   `fromemail`, `from_name`, `reply_to`, `body`. Optional: `type`
   (`standard` | `automated`), `isdraft`, `isscheduled` +
   `scheduled_datetime` to schedule instead of sending immediately, and
   `lists` / `audiences` for recipients. Start with `isdraft: true` when a
   human review step is wanted — there is no idempotency key, so do not blind-
   retry a create; on timeout, list campaigns first (step 3) to check whether
   the create landed.
3. **Confirm** — `GET EmailMarketing/Campaigns` (`get-EmailMarketing-Campaigns`,
   paginated via `page`/`limit`) or `GET EmailMarketing/GetCampaign`
   (`get-EmailMarketing-GetCampaign`) with the campaign id.
4. **Measure** — `GET EmailMarketing/Stats` (`get-EmailMarketing-Stats`) with
   the campaign id for opens/clicks performance.

## Error handling

Check `response.header.status`; on `error`, `response.data.errorCode` /
`errorMessage` explain the failure (see `errors/vbout-problem-types.yml`).
