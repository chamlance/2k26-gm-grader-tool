# Architecture Overview

## What kind of app is this?

A **single-file browser application** — no server, no backend, no database, no build process. The entire tool is one HTML file (~110KB) that runs entirely in the browser. React, styles, logic, and all data are self-contained.

This was built through iterative AI prompting in Claude.ai and deployed as a Claude artifact before being exported to GitHub.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Browser (Client)                  │
│                                                     │
│   ┌─────────────────────────────────────────────┐   │
│   │           React 18 Application              │   │
│   │                                             │   │
│   │   UI Tabs → State → Services → API Calls   │   │
│   └─────────────────────────────────────────────┘   │
│                        │                            │
│            ┌───────────┼───────────┐                │
│            ▼           ▼           ▼                │
│      window.storage  LZString   SheetJS             │
│      (cloud save)   (compress) (export)             │
└─────────────────────────────────────────────────────┘
                         │
                         ▼
          ┌──────────────────────────┐
          │      Anthropic API       │
          │   claude-sonnet-4        │
          │   /v1/messages endpoint  │
          └──────────────────────────┘
```

---

## Layer Breakdown

### UI Layer — 6 Tabs
Each tab is a self-contained view rendered conditionally by React state:

| Tab | Purpose |
|---|---|
| New Move | Free-text input → triggers move analysis API call |
| Last Analysis | Displays full result of most recent move |
| GM Log | Persistent move history + AI-generated GM Summary |
| Rumors | War room intel board + Situation Room summary |
| Consult | Org voice consultation on a proposed move |
| Power Rankings | User's avg grade vs all 29 real NBA GMs |

### State Layer — React useState
All application data lives in React state. Nothing is computed server-side.

| State variable | What it holds |
|---|---|
| `moves[]` | Full array of analyzed moves with grades and reactions |
| `rumors[]` | Active war room rumors |
| `cur` | Currently selected move for Last Analysis view |
| `gmSummary` | AI-generated text characterizing the GM's mode |
| `rumorSummary` | AI-generated Situation Room text |
| `consultResult` | Last org consultation result |
| `selectedTeam` | Active team ID (e.g. `"bulls"`, `"lakers"`) |

### Persistence Layer — window.storage
`window.storage` is Anthropic's artifact cloud storage, scoped per artifact URL. When the artifact URL changes (on every new publish), storage resets. This is why the **Sync Code** system exists — it bridges sessions.

Five storage keys:
```
gmgrader-v5-moves     → moves array
gmgrader-v5-rumors    → rumors array
gmgrader-v5-gmsumm    → GM summary text
gmgrader-v5-rumsumm   → rumor summary text
gmgrader-v5-team      → selected team ID
```

---

## No-Server Design Decision

This was a deliberate constraint. Running entirely in the browser means:
- Zero hosting cost
- Zero backend maintenance
- Anyone can open it with just a URL
- The sync code system replaces what a database would normally handle

The tradeoff: `window.storage` resets on URL change, so users must manually copy sync codes to persist data across sessions.

---

## File Structure

```
index.html          ← entire application (HTML + CSS + JS)
README.md           ← project overview
docs/
  ARCHITECTURE.md   ← this file
  DATA_FLOW.md      ← how data moves through the system
  SERVICES.md       ← external services and APIs used
```
