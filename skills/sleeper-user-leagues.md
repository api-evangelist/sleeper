---
name: Find a user's leagues and drafts
description: >-
  Resolve a Sleeper user by username, then list every league and draft they
  belong to for a given sport and season.
api: openapi/sleeper-openapi.yml
operations: [getUser, getUserLeagues, getUserDrafts, getSportState]
method: generated
source: openapi/sleeper-openapi.yml
generated: '2026-07-21'
---

# Find a user's leagues and drafts

Read-only, no auth. Base URL `https://api.sleeper.app/v1`.

## Steps

1. **Resolve the user** — call `getUser` (`GET /user/{id_or_username}`) with the
   username to obtain the numeric `user_id`.
2. **Pick the season** — if unspecified, call `getSportState`
   (`GET /state/nfl`) and use `season`.
3. **List leagues** — call `getUserLeagues`
   (`GET /user/{user_id}/leagues/{sport}/{season}`) for every league object.
4. **List drafts** — call `getUserDrafts`
   (`GET /user/{user_id}/drafts/{sport}/{season}`) for each draft's `draft_id`,
   `type`, and `status`.

## Rules

- Always resolve username → `user_id` first; downstream calls take `user_id`.
- `sport` is typically `nfl`; `season` is a 4-digit year string.
- Handle `404` (unknown user) and `429` (rate limit) explicitly.
