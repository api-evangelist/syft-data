---
name: syft-data-export-motion-results
description: Poll a Syft motion's execution results (processed accounts and contacts) with cursor pagination.
api: Syft Data Lookup & Export API
generated: '2026-07-21'
method: generated
source: https://docs.syftdata.com/implementation/export-api
operations:
  - exportMotionRuns
---

# Export Syft motion results

Use this skill to poll the outcomes of a motion — the accounts and contacts it
processed, with evaluation status and action summaries — into a custom integration.

## Auth
Send a Server Secret Key as `Authorization: Bearer sk_live_...` (or
`x-syft-secret-key`).

## Steps

1. **Call `exportMotionRuns`** — `GET https://app.syftdata.com/api/motion/{motionId}/export`.
   Default `status` is `completed`; pass `status=failed|skipped|in_progress` to widen.
2. **Filter incrementally.** Use `since`/`until` (ISO 8601) to poll only new runs;
   `order=asc` keeps a stable forward cursor for repeated polling.
3. **Paginate.** Read `items[]` and, if `nextCursor` is present, call again with
   `cursor=<nextCursor>` and `limit` up to 100 (default 50). Treat the cursor as opaque.
4. **Shrink the payload.** Pass `fields=status,account,contacts` to return only the
   top-level fields you need (`runId` is always included).
5. **Handle `401`** (`{ "error": "Unauthorized" }`) by refreshing the secret key.
