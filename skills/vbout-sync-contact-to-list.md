---
name: vbout-sync-contact-to-list
description: Add or update a contact in a VBOUT email marketing list, set custom fields, and tag them.
api: openapi/vbout-openapi-original.json
operations:
  - get-EmailMarketing-GetLists
  - get-EmailMarketing-GetList
  - post-EmailMarketing-AddContact
  - post-EmailMarketing-SyncContact
  - get-EmailMarketing-GetContactByEmail
  - post-EmailMarketing-AddTag
---

# Sync a contact into a VBOUT list

Base URL `https://api.vbout.com/1`. Every call needs the account API key as the
`key` parameter (see `authentication/vbout-authentication.yml`). Stay under 15
requests/second and back off on HTTP 429 using the `x-rate-limit-after` header
(see `rate-limits/vbout-rate-limits.yml`).

## Steps

1. **Find the target list** — `GET EmailMarketing/GetLists`
   (`get-EmailMarketing-GetLists`) and pick the list `id`. Use
   `GET EmailMarketing/GetList` (`get-EmailMarketing-GetList`) to read that
   list's custom field IDs — the `fields` parameter below is keyed by field ID,
   not name.
2. **Prefer upsert** — `POST EmailMarketing/SyncContact`
   (`post-EmailMarketing-SyncContact`) with required `email`, plus `listid`,
   `status` (`Active` | `Disactive`), and `fields` (Array keyed by fieldID).
   There is **no idempotency-key mechanism** on this API
   (`conventions/vbout-conventions.yml`); SyncContact is the safe retryable
   write because it adds-or-updates by email. Use
   `POST EmailMarketing/AddContact` (`post-EmailMarketing-AddContact`,
   required: `status`, `listid`) only when you explicitly want a new record.
3. **Verify** — `GET EmailMarketing/GetContactByEmail`
   (`get-EmailMarketing-GetContactByEmail`) with `email` (and `listid` to scope
   to the list).
4. **Tag** — `POST EmailMarketing/AddTag` (`post-EmailMarketing-AddTag`) with
   required `email` and `tagname`.

## Error handling

Success/failure is signaled in the envelope, not only the HTTP status: check
`response.header.status` == `ok`; on `error`, read `response.data.errorCode` /
`errorMessage` (e.g. code 1000 = missing/invalid key). See
`errors/vbout-problem-types.yml`.
