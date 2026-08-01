---
name: Get a league's teams and weekly matchups
description: >-
  Look up a Sleeper league, list its teams (rosters + owners), and pull the
  head-to-head matchups for a given week to compute standings or scores.
api: openapi/sleeper-openapi.yml
operations: [getLeague, getLeagueRosters, getLeagueUsers, getSportState, getLeagueMatchups]
method: generated
source: openapi/sleeper-openapi.yml
generated: '2026-07-21'
---

# Get a league's teams and weekly matchups

The Sleeper API is read-only and needs **no authentication**. Base URL:
`https://api.sleeper.app/v1`. Keep total traffic under ~1,000 requests/minute.

## Steps

1. **Confirm the current week** — call `getSportState` (`GET /state/nfl`) and read
   `week` / `season` if the caller did not specify one.
2. **Load the league** — call `getLeague` (`GET /league/{league_id}`) for its
   name, `season`, `status`, `total_rosters`, and `roster_positions`.
3. **List teams** — call `getLeagueRosters` (`GET /league/{league_id}/rosters`)
   for each team's `roster_id`, `owner_id`, and `players`, and `getLeagueUsers`
   (`GET /league/{league_id}/users`) to map `owner_id` → `display_name`.
4. **Pull the week's matchups** — call `getLeagueMatchups`
   (`GET /league/{league_id}/matchups/{week}`). Rosters that share the same
   `matchup_id` are playing each other; `points` is each roster's score.
5. **Assemble** — join matchups to rosters by `roster_id`, then to users by
   `owner_id`, to produce readable head-to-head results.

## Rules

- Handle `404` as "league or week not found"; back off on `429`.
- Resolve avatars at `https://sleepercdn.com/avatars/{avatar}`.
- There is no pagination — each call returns the full array.
