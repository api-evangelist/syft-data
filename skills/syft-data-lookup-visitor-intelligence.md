---
name: syft-data-lookup-visitor-intelligence
description: Look up a company or contact in Syft by email or domain and read their site + LinkedIn intent activity.
api: Syft Data Lookup & Export API
generated: '2026-07-21'
method: generated
source: https://docs.syftdata.com/implementation/lookup-api
operations:
  - lookupEntities
---

# Look up visitor intelligence in Syft

Use this skill to pull Syft's observed company/contact intelligence into your own
tools. Syft only returns entities it has **already seen** on your site or LinkedIn
ads — it is not an enrichment API and returns `404 Not found` for unknown entities.

## Auth
Use a Server Secret Key (`sk_live_...`) from Settings > Data Collection. Send it as
`Authorization: Bearer sk_live_...` (or `x-syft-secret-key: sk_live_...`).

## Steps

1. **Decide the key.** Look up by `email` for a specific person, or by `domain`
   for a company (returns up to 10 known contacts). If both are sent, `email` wins.
2. **Call `lookupEntities`** — `POST https://app.syftdata.com/api/syft/lookup` with
   JSON body `{ "email": "john@acme.com", "includeActivity": true }`. Set
   `includeActivity: true` to get `site_activity` (up to 10 recent sessions) and
   `linkedin_activity` per contact.
3. **Read the response.** `company` carries firmographics + `syft_purchase_intent`
   (low/medium/high/very_high); `contacts[]` carry identity, session counts, and
   activity.
4. **Handle errors** (flat `{ "error": ... }` envelope): `400` = neither email nor
   domain sent; `401` = bad/missing key; `404` = Syft has not observed the entity —
   do not treat as an error to retry.
