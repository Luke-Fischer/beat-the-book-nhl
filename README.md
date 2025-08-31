# NHL AI vs Fanduel Lines
A full season, GameCenter-style web app that pits my AI model against FanDuel’s lines across all 1,312 NHL games. Browse the season by month, drill into any day, and see each matchup as a live game card with score, SOG, status, and a head-to-head pick comparison.

## Planned Features
- Season calendar with daily game cards
- Live score, SOG, team record, game time on each game card
- Train an AI model to go up against fanduel's predicted winner
- Predictions freeze at puck drop for full audability
- Competition overiview dashboard, i.e comparing fanduels picks with mine

## Architecutre
- UI: Next.js + Tailwind. Renders calendar, daily cards, game details, competition pages.
- API: API: ASP.NET Core (.NET 8) Minimal API providing REST + SSE.

### Workers
- worker-odds: Polls pregame odds and writes odds_snapshots. Stops at lock.
- worker-lock-settle: Freezes picks at the first Live state, settles results at Final, updates standings.
- Scheduled batch predictions for games not yet locked, writes model_predictions.
- PostgreSQL: Source of truth for schedule, snapshots, predictions, frozen picks, results, standings.
- Redis: Hot cache and lightweight pubsub for “locked” events and live blobs.

## Monorepo Layout
.
+-- src
|   +-- Api
|   |   +-- Api.csproj
|   |   `-- Program.cs
|   +-- Worker.Odds
|   |   +-- Worker.Odds.csproj
|   |   `-- Program.cs
|   +-- Worker.LockSettle
|   |   +-- Worker.LockSettle.csproj
|   |   `-- Program.cs
|   `-- Shared
|       +-- Shared.csproj
|       `-- Db.cs
+-- ui-next
+-- models
|   `-- predictor-python
+-- sql
|   `-- 001_init.sql
+-- docker
|   +-- Api.Dockerfile
|   +-- Worker.Odds.Dockerfile
|   `-- Worker.LockSettle.Dockerfile
`-- docker-compose.yml



