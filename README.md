# 🏒 50 Pucks — NHL Playoff Pool

A live, interactive NHL playoff pool dashboard. Contestants pick 15 players, allocate 50 pucks as multipliers, and compete for the highest score throughout the playoffs.

## How It Works

**Score = Playoff Points × Team Multiplier × Pucks Allocated**

- Pick **10 forwards** and **5 defensemen**
- Allocate **50 pucks** across your roster (min 1, max 6 per player)
- Teams have multipliers based on seeding: **Wild Card = 2.0x**, **Middle = 1.5x**, **Division Winners = 1.0x**
- Scores update live from the NHL API

## Features

- **Live Leaderboard** — Ranked standings with score, players alive, best/worst picks
- **Team Detail** — Player-by-player breakdown with puck allocation visualization
- **Score Charts** — Score comparison, position breakdown, multiplier tier analysis
- **Player Analytics** — Ownership rates, average pucks, performance across all entries
- **Monte Carlo Projections** — Simulates remaining games with customizable parameters
- **Pool Info** — Rules, payout structure, team multipliers, entry list
- **Mobile-friendly** — Responsive design works on all devices

## Quick Start

### 1. Install dependencies

```bash
npm install
```

### 2. Configure the season

Edit `src/config/season.js`:

```js
export const SEASON_CONFIG = {
  year: 2026,
  entryFee: 40,
  
  // Set team multipliers before playoffs start
  teamMultipliers: {
    TOR: 1.0, WPG: 1.0, WSH: 1.0, VGK: 1.0,  // Division winners
    CAR: 1.5, FLA: 1.5, EDM: 1.5, DAL: 1.5,    // Middle seeds
    MTL: 2.0, OTT: 2.0, MIN: 2.0, STL: 2.0,    // Wild cards
  },
  
  // Update as teams are eliminated
  teamsAlive: ['TOR', 'WPG', 'WSH', 'VGK', 'CAR', 'FLA', 'EDM', 'DAL', 'MTL', 'OTT', 'MIN', 'STL', ...],
  
  // Your published Google Sheet URL (see below)
  googleSheetCsvUrl: 'https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID/pub?output=csv',
};
```

### 3. Set up the Google Sheet for entries

1. Create a Google Sheet with these columns (exact headers in Row 1):

| team_name | owner_name | player_name | player_team | position | pucks |
|-----------|------------|-------------|-------------|----------|-------|
| COWABUNGAR DUDE | Matt Ungar | Leon Draisaitl | EDM | F | 6 |
| COWABUNGAR DUDE | Matt Ungar | Connor McDavid | EDM | F | 6 |
| ... | ... | ... | ... | ... | ... |

2. Each contestant gets 15 rows (10 forwards + 5 defense)
3. Publish the sheet: **File → Share → Publish to web → CSV format**
4. Copy the URL into `SEASON_CONFIG.googleSheetCsvUrl`

### 4. Set up the Google Form for entry submission

Create a Google Form with:
- Team Name (text)
- Owner Name (text) 
- Forward 1 through Forward 10 (dropdowns with player names)
- Forward 1 Pucks through Forward 10 Pucks (dropdowns: 1-6)
- Defense 1 through Defense 5 (dropdowns with player names)
- Defense 1 Pucks through Defense 5 Pucks (dropdowns: 1-6)

Link the form responses to your Google Sheet. You may need to reformat the responses into the flat structure above.

### 5. Run locally

```bash
npm run dev
```

Open `http://localhost:5173`

### 6. Deploy to GitHub Pages

```bash
# Build
npm run build

# Deploy (requires gh-pages package)
npm run deploy
```

Or set up GitHub Actions for auto-deploy on push (see `.github/workflows/deploy.yml`).

## Yearly Maintenance

Each year before playoffs:

1. **Update `src/config/season.js`:**
   - `year`
   - `nhlSeasonId` 
   - `teamMultipliers` (based on final standings)
   - `teamsAlive` (start with all 16 playoff teams)
   - `googleSheetCsvUrl` (new sheet for the year)
   - `entryDeadline`
   - `payoutSplit` if changed

2. **Create new Google Sheet** with contestant entries

3. **During playoffs:** Update `teamsAlive` as teams are eliminated, then rebuild and deploy

## Tech Stack

- **React 18** + Vite
- **Tailwind CSS** for styling
- **Recharts** for charts
- **Lucide React** for icons
- **NHL Public API** (api-web.nhle.com) for live stats
- **Google Sheets** as entry database
- **GitHub Pages** for hosting

## Project Structure

```
src/
├── api/
│   ├── nhlApi.js        # NHL API service
│   └── sheets.js        # Google Sheet loader
├── components/
│   ├── Header.jsx       # Branding, live indicator, refresh
│   ├── Leaderboard.jsx  # Main standings table
│   ├── TeamDetail.jsx   # Player-by-player breakdown
│   ├── PointsChart.jsx  # Score comparison charts
│   ├── PopularPlayers.jsx # Ownership & performance analytics
│   ├── Simulation.jsx   # Monte Carlo projections
│   ├── PoolInfo.jsx     # Rules, payouts, multipliers
│   └── LoadingScreen.jsx
├── config/
│   └── season.js        # ⭐ EDIT THIS EACH YEAR
├── utils/
│   ├── scoring.js       # Score calculation engine
│   └── simulation.js    # Monte Carlo engine
├── App.jsx
├── main.jsx
└── index.css
```

## License

MIT — use it for your own playoff pool.
