# 🏒 50 Pucks — NHL Playoff Pool

A live NHL playoff pool dashboard. Single HTML file — no build tools required.

## Setup

### 1. Create a GitHub repository
1. Go to github.com → **New Repository**
2. Name it `50pucks`
3. Upload `index.html` and this `README.md`

### 2. Enable GitHub Pages
1. **Settings → Pages** → Source: **Deploy from a branch** → `main` / `root` → **Save**
2. Site goes live at `https://yourusername.github.io/50pucks/`

### 3. Google Sheet + Apps Script (one-time setup)
1. Create a Google Sheet with headers in row 1: `team_name | owner_name | player_name | player_team | position | pucks | timestamp`
2. In the sheet: **Extensions → Apps Script** → paste:
```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);
  var rows = data.rows;
  for (var i = 0; i < rows.length; i++) {
    sheet.appendRow([
      rows[i].team_name, rows[i].owner_name, rows[i].player_name,
      rows[i].player_team, rows[i].position, rows[i].pucks,
      new Date().toISOString()
    ]);
  }
  return ContentService.createTextOutput(JSON.stringify({status:'ok'}))
    .setMimeType(ContentService.MimeType.JSON);
}
```
3. **Deploy → New deployment** → Web app → Execute as: Me → Access: Anyone → Deploy
4. Copy the Web App URL into `APPS_SCRIPT_URL` in `index.html`
5. **Publish sheet as CSV:** File → Share → Publish to web → CSV → copy URL into `sheetUrl` in the season config

## How It Works

**Score = Playoff Points × Team Multiplier × Pucks Allocated**

- Pick **10 forwards** and **5 defensemen**
- Allocate **50 pucks** (min 1, max 6 per player)
- Multipliers: **Wild Card = 2.0x**, **Middle = 1.5x**, **Division Winners = 1.0x**

## Features

- **Standings** — Leaderboard with score, players alive, pucks remaining
- **Team Detail** — Player-by-player breakdown with puck visualization
- **Analytics** — Single chart view with dropdown (7 chart types)
- **Players** — Ownership rates, performance across entries
- **Projections** — Monte Carlo simulation with customizable parameters
- **Pool Info** — Rules, payouts, multipliers
- **Enter Team** — Built-in entry form with dropdowns, validation, and direct-to-Sheet submit
- **Year toggle** — Switch between seasons (2025 historical, 2026 live)

## Admin Workflow

### Before Playoffs
| Task | How |
|---|---|
| Collect entries | Participants use "Enter Team" tab → data lands in your Google Sheet automatically |
| Review entries | Open Google Sheet, verify 15 rows per entry, pucks total 50 |
| Delete test entries | Delete rows directly in the Sheet |
| Set multipliers | Update `mult` in the 2026 season config in `index.html` |
| Set teams alive | Update `alive` array with all 16 playoff teams |
| Wire up the Sheet | Paste published CSV URL into `sheetUrl` in the 2026 config |
| Deploy | Commit and push to GitHub |

### During Playoffs
| Task | How |
|---|---|
| Team eliminated | Remove from `alive` array in `index.html`, commit and push |
| Scores | Automatic — NHL API updates on page load |
| Entry deadline passed | Optionally remove `'entry'` from the tabs array in the nav |

### After Playoffs
| Task | How |
|---|---|
| Final standings | Dashboard shows final results automatically |
| Save for history | The 2026 data stays in the Sheet and works via the year toggle |
| Prep for next year | Add a `2027` entry in the `SEASONS` config, repeat the process |

---

## 🚀 2026 PLAYOFF LAUNCH PROMPT

**When the 2026 playoff bracket is finalized (around April 16–18), copy everything below into a new Claude conversation to go live.**

Fill in the `[FILL IN]` sections first.

---

```
I have an NHL playoff pool application called "50 Pucks" hosted at:
https://rkunka31.github.io/50pucks/

The source code is a single index.html file in this GitHub repo:
https://github.com/rkunka31/50pucks

The Google Sheet for entries (connected via Apps Script) is here:
[FILL IN YOUR GOOGLE SHEET URL — e.g. https://docs.google.com/spreadsheets/d/ABC123/edit]

The published CSV URL for that sheet is:
[FILL IN YOUR PUBLISHED CSV URL — e.g. https://docs.google.com/spreadsheets/d/ABC123/pub?output=csv]

The Apps Script URL (already in the code) is:
https://script.google.com/macros/s/AKfycbxbstEyuXo2ydoxzF7WXgYIlw9-ot-vC2L5jw4rOor0_CxlMsf3gADprJv1n22vLvAY/exec

---

THE 2026 NHL PLAYOFF TEAMS AND MULTIPLIERS ARE:

Division Winners (1.0x multiplier):
[FILL IN — e.g. COL, CAR, BUF, ANA]

Middle Seeds (1.5x multiplier):
[FILL IN — e.g. DAL, MIN, TBL, MTL, PIT, NYI, VGK, EDM]

Wild Card Teams (2.0x multiplier):
[FILL IN — e.g. DET, BOS, UTA, SEA]

All 16 playoff teams:
[FILL IN — e.g. COL, DAL, MIN, ANA, VGK, EDM, UTA, SEA, CAR, BUF, TBL, MTL, DET, BOS, PIT, NYI]

---

WHAT I NEED YOU TO DO:

1. UPDATE THE 2026 SEASON CONFIG in index.html:
   - Set the team multipliers (1.0x, 1.5x, 2.0x as listed above)
   - Set the "alive" array to all 16 playoff teams
   - Set the sheetUrl to my published CSV URL
   - Set the entry fee (still $40 CAD unless I say otherwise)

2. UPDATE THE PLAYER ROSTERS in the entry form:
   - The ROSTER_F (forwards) and ROSTER_D (defense) arrays need to
     reflect the ACTUAL 2026 playoff rosters. Pull current rosters
     from the NHL API or NHL.com for all 16 playoff teams.
   - Include the top 6-8 forwards and top 3-4 defensemen per team.
   - Remove any teams that didn't make the playoffs from the lists.

3. VERIFY THE NHL API is working for the 2026 season:
   - The season ID should be '20252026'
   - Test that the stats endpoint returns data (it may not have
     playoff data yet if playoffs haven't started — that's fine,
     it will populate once games begin)

4. SET THE DEFAULT YEAR TO 2026:
   - Change the initial state S.yr from 2025 to 2026

5. KEEP 2025 AS A HISTORICAL OPTION:
   - The year toggle should still work for 2025

6. GIVE ME THE UPDATED index.html ready to commit to GitHub.

---

CONTEXT FOR CLAUDE:

This app was built across multiple iterations. Key architecture:
- Single index.html file, hosted on GitHub Pages
- Light theme (navy #0f1729 + gold #d4a843 + slate grays)
- NHL stats from api.nhle.com/stats/rest/en/skater/summary (gameTypeId=3 for playoffs)
- Fallback hardcoded stats for 2025
- Entry form with localStorage + direct-to-Google-Sheet submit via Apps Script
- Player dropdowns embedded in ROSTER_F and ROSTER_D arrays
- Scoring: Playoff Points × Team Multiplier × Pucks
- Features: Leaderboard, Team Detail, Analytics (single chart with dropdown),
  Players, Projections (Monte Carlo), Info, Entry Form
```

---

## Tech Stack

- **Tailwind CSS** (CDN) for styling
- **Chart.js** (CDN) for charts
- **NHL API** (`api.nhle.com`) for live playoff stats
- **Google Sheets** as entry database
- **Google Apps Script** as submission endpoint
- **GitHub Pages** for hosting
- No build tools, no Node.js, no dependencies to install
