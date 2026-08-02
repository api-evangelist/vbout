---
name: vbout-schedule-social-post
description: Schedule a social media post to connected channels via the VBOUT API and check its calendar slot and stats.
api: openapi/vbout-openapi-original.json
operations:
  - get-SocialMedia-Channels
  - post-SocialMedia-AddPost
  - get-SocialMedia-Calendar
  - get-SocialMedia-post
  - get-SocialMedia-Stats
---

# Schedule a social post with VBOUT

Base URL `https://api.vbout.com/1`; authenticate with the `key` parameter.
Respect the 15 req/s limit.

## Steps

1. **List connected channels** — `GET SocialMedia/Channels`
   (`get-SocialMedia-Channels`) to see which networks the account has
   connected and their channel ids.
2. **Create the post** — `POST SocialMedia/AddPost`
   (`post-SocialMedia-AddPost`). Required: `message`, `channel`
   (e.g. `facebook` | `twitter` | ...), `channelid`. To schedule instead of
   posting now, set `isscheduled: true` with `scheduleddate`,
   `scheduledhours`, and `scheduledampm` (`AM`/`PM`). Optional: `photo`
   (link or upload), `trackableLinks: true` to shorten links. No idempotency
   key exists — on a timeout, check the calendar (step 3) before retrying to
   avoid duplicate posts.
3. **Verify the slot** — `GET SocialMedia/Calendar`
   (`get-SocialMedia-Calendar`, paginated `page`/`limit`) to confirm the
   scheduled post, or `GET SocialMedia/GetPost` (`get-SocialMedia-post`) for a
   single post.
4. **Measure** — `GET SocialMedia/Stats` (`get-SocialMedia-Stats`) for channel
   performance.

## Error handling

Check `response.header.status`; on `error`, read `response.data.errorCode` /
`errorMessage` (see `errors/vbout-problem-types.yml`).
