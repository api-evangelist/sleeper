---
name: Read a draft's picks and trends
description: >-
  Pull a completed Sleeper draft's picks (player, round, roster) and cross-check
  the global player pool and trending adds/drops.
api: openapi/sleeper-openapi.yml
operations: [getDraft, getDraftPicks, getDraftTradedPicks, getAllPlayers, getTrendingPlayers]
method: generated
source: openapi/sleeper-openapi.yml
generated: '2026-07-21'
---

# Read a draft's picks and trends

Read-only, no auth. Base URL `https://api.sleeper.app/v1`.

## Steps

1. **Load the draft** — call `getDraft` (`GET /draft/{draft_id}`) for `type`
   (snake/auction/linear), `status`, and `draft_order`.
2. **Get the picks** — call `getDraftPicks` (`GET /draft/{draft_id}/picks`); each
   pick carries `pick_no`, `round`, `roster_id`, `player_id`, and `picked_by`.
3. **Get traded picks** — call `getDraftTradedPicks`
   (`GET /draft/{draft_id}/traded_picks`) to see picks that changed hands.
4. **Resolve player names** — call `getAllPlayers` (`GET /players/nfl`) ONCE, cache
   the ~5MB map, and look up each `player_id`. Never call this per pick.
5. **Optional trends** — call `getTrendingPlayers`
   (`GET /players/nfl/trending/add`) to surface hot waiver targets.

## Rules

- Cache `getAllPlayers` for up to a day; it is large and rate-limit sensitive.
- Join picks to players by `player_id` and to users by `picked_by`.
- Back off on `429`; stay under ~1,000 req/min.
