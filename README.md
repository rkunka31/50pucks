# 🏒 50 Pucks — NHL Playoff Pool

A live NHL playoff pool dashboard. Single HTML file — no build tools required.

## Setup

### 1. Create a GitHub repository
1. Go to github.com → **New Repository**
2. Name it `50pucks` (or whatever you like)
3. Make it **Public**
4. Upload `index.html` to the repository

### 2. Enable GitHub Pages
1. In your repo, go to **Settings → Pages**
2. Under "Source", select **Deploy from a branch**
3. Branch: `main`, folder: `/ (root)`
4. Click **Save**
5. Your site will be live at `https://yourusername.github.io/50pucks/` within a minute

### 3. Configure for your pool
Open `index.html` and edit the `CONFIG` object near the top (around line 25):

```js
const CONFIG = {
  year: 2026,
  entryFee: 40,
  
  // Publish your Google Sheet as CSV and paste URL here
  googleSheetCsvUrl: 'https://docs.google.com/spreadsheets/d/YOUR_ID/pub?output=csv',
  
  // Set multipliers before playoffs start
  teamMultipliers: {
    TOR: 1.0, WPG: 1.0, WSH: 1.0, VGK: 1.0,    // Division winners
    CAR: 1.5, FLA: 1.5, EDM: 1.5, DAL: 1.5,      // Middle seeds
    MTL: 2.0, OTT: 2.0, MIN: 2.0, STL: 2.0,      // Wild cards
  },
  
  // All 16 playoff teams — remove as eliminated
  teamsAlive: ['TOR','WPG','WSH','VGK','CAR','FLA','EDM','DAL','MTL','OTT','MIN','STL','COL','TBL','NJD','LAK'],
};
```

### 4. Set up the Google Sheet for entries
Create a Google Sheet with these exact columns in Row 1:

| team_name | owner_name | player_name | player_team | position | pucks |
|-----------|------------|-------------|-------------|----------|-------|
| COWABUNGAR DUDE | Matt Ungar | Leon Draisaitl | EDM | F | 6 |
| COWABUNGAR DUDE | Matt Ungar | Connor McDavid | EDM | F | 6 |

Each contestant gets **15 rows** (10 forwards + 5 defense).

To publish:
1. **File → Share → Publish to web**
2. Select the sheet tab with entries
3. Format: **CSV**
4. Click Publish and copy the URL
5. Paste into `googleSheetCsvUrl` in the config

### 5. Google Form for entry submission
Create a Google Form and link responses to your Sheet. After entries close, reformat into the flat table structure above.

## During Playoffs

1. **Scores update automatically** from the NHL API every time someone loads the page
2. **When a team is eliminated:** Edit `teamsAlive` in the config, remove that team, and push to GitHub
3. That's it — everything else is automatic

## Features

- **Leaderboard** — Ranked standings with score, players alive, best/worst picks
- **Team Detail** — Full player breakdown with puck allocation visualization
- **Charts** — Score comparison, position breakdown, multiplier tier analysis
- **Player Analytics** — Ownership rates, average pucks, performance across entries
- **Monte Carlo Projections** — Customizable simulation of remaining games
- **Pool Info** — Rules, payout structure, team multipliers
- **Mobile-friendly** — Works on phones and tablets

## How Scoring Works

**Score = Playoff Points × Team Multiplier × Pucks**

Example: McDavid gets 10 pts, Oilers are 1.5x, you gave him 6 pucks → 10 × 1.5 × 6 = **90 points**
