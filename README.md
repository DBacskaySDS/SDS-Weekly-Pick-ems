# SDS Weekly Pick'em

A one-page NFL pick'em site for the Striking Distance Studios office pool. Everything lives in [index.html](index.html): there's no build step, no backend, and nothing to install. It's served as a static page via GitHub Pages.

## What the page does

The page has two tabs.

**Leaderboard**
- **Season Standings**: computed in the browser from the weekly results table. Players are ranked by total correct picks. The page also shows an "If Lowest Dropped" total (the two lowest weeks removed, once 3+ weeks are complete), missed weeks, and average tiebreaker diff.
- **Week N Results**: the most recent completed week, with the weekly winner highlighted.
- **Week N Picks (Live)**: everyone's picks for the current week, read from the Google Form's published response sheet. Picks stay hidden until picks lock on Sunday. If someone submits more than once, only their latest submission counts.
- **Week N Live Standings**: grades the current week's picks against ESPN's public scoreboard API as games go final. In-progress games don't count yet.

**Make Your Picks**
- A form for the current week's games plus a tiebreaker (total points in the Monday night game). It posts straight to a Google Form through a hidden iframe.
- The tab locks on Sunday morning, before the first Sunday kickoff.

## Scoring rules

- **Season ranking:** most total correct picks. Ties go to the higher drop-2 total, then to name order.
- **Weekly winner / live standings:** most correct picks, then the smallest tiebreaker diff.
- **Tied games:** a game that ends in a tie doesn't count as a correct pick for anyone.
- **Late picks:** the form stays open until Sunday. If you submit after the Thursday game kicks off, your Thursday pick counts as incorrect. The code doesn't apply this rule (`FIRST_GAME_KICKOFF_AT` is only for reference), so adjust for it by hand when entering weekly results.

## Weekly rollover checklist

SDS uses a **new Google Form and Sheet every week**, so each of these values in the `<script>` block of [index.html](index.html) has to be replaced, not just edited:

1. **Add last week's results** to `TRACKER_TEXT` under `| Weekly Results |`, one row per player:
   ```
   | Week | Name | Correct | Total Games | Tiebreaker Guess | Actual Total | Diff | Rank | Won |
   ```
   Row order doesn't matter. Season standings, drops, and missed weeks recalculate on their own.
2. **`CURRENT_WEEK`**: bump it to the new week.
3. **`PICKS_LOCK_AT`**: the earliest Sunday kickoff, in UTC (for example, 9:30 AM EDT is `13:30:00Z`).
4. **`FIRST_GAME_KICKOFF_AT`**: the Thursday night kickoff, in UTC.
5. **`FORM_ACTION_URL`**: the new form's `.../formResponse` URL.
6. **`NAME_ENTRY`, `TIEBREAKER_ENTRY`, and each game's `entry`** in `GAMES`: the new form's `entry.NNNN` IDs. Double-check that each ID matches its question. A mapping that's off by one makes the form reject every submission without any error showing.
7. **`GAMES`**: the matchups, kickoff text, and away/home team names. Set `tiebreaker: true` on the Monday night game. Team names have to match the form's options exactly, since they're compared against the response sheet and ESPN.
8. **`PICKS_CSV_URL`**: the new response sheet's published CSV link. In the form editor, go to Responses → View in Sheets, then in the sheet choose File → Share → Publish to web → the "Form Responses" sheet → CSV → Publish.
9. `SEASON_YEAR` only changes when a new season starts.

Then commit and push. GitHub Pages picks up the change in a minute or two.

## Running locally

Open `index.html` in a browser. The live picks and live standings sections need network access to Google Sheets and ESPN. They won't load inside sandboxed previews, such as Claude artifacts, so check them on the GitHub Pages site.
