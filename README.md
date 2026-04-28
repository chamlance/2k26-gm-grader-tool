# 2k26 GM Grader Tool
### A browser-based tool for NBA 2K26 My League / franchise mode players who want their GM decisions graded like the real thing. Drop your raw notes — a trade, a signing, a draft pick — and the tool grades it on realism and strategic quality, generates insider reactions from a rotating pool of real NBA journalists.

**Live demo (Claude account required to use):** https://claude.ai/public/artifacts/9648f29a-63a8-441c-be79-0544419446d1

---

## What it does

A browser-based tool for NBA 2K26 My League / franchise mode players who want their GM decisions graded like the real thing. Drop your raw notes — a trade, a signing, a draft pick — and the tool grades it on realism and strategic quality, generates insider reactions from a rotating pool of real NBA journalists.

- A letter grade (A–F) for realism and GM decision quality
- Insider reactions from randomized journalists (Woj, Shams, Zach Lowe, etc.)
- An AI-generated GM Summary characterizing your organizational mode
- Rumors tab that feed into your next move analysis
- A Consult tab — pick the brains of your team's legends and front office before making a mvoe
- Power Rankings vs all 29 real NBA GMs and Presidents

Choose your team from all 30 NBA franchises. Each team has 
its own org voice pool (front office, coaches, legends).

---

## How to use it

Open the live link above. No login, no install, no account.

**Sync Code** = your save file. Copy it before closing 
your session. Paste it back to restore everything — moves, 
rumors, grades, all of it. Think of each sync code as a 
separate saved game, one per team.

---

## How this was built

This entire tool was built through natural language conversation 
with Claude (claude.ai) — no IDE, no terminal, no prior codebase.

Every feature, bug fix, and architectural decision was done 
through iterative prompting across a single project session:

- React 18 (no build step — UMD via CDN)
- Anthropic Claude Sonnet 4 via /v1/messages API
- LZString for sync code compression
- SheetJS for Excel export
- Single HTML file — ~110KB, zero backend

**Features shipped through AI prompting:**
- Move analyzer with strict grading prompt
- Rotating insider pool (16 journalists, 8 per side, 3 per call)
- GM Summary + Situation Room (auto-generated on each move/rumor)
- Consult tab with delimiter-based parsing (not JSON — avoids apostrophe corruption)
- 30-team org voice pools with front office / coach / legend tiers
- Player valuation framework (contract value, player tier, media perception)
- Sync code system with team identity baked in
- Unsaved changes warning (red indicator on Sync button)
- Power Rankings vs real NBA GMs

**Bugs fixed through AI debugging:**
- Temporal dead zone on TEAM_DATA constant
- Nested template literal syntax errors
- window.confirm() blocked in sandboxed iframes → custom modal
- JSON parse failures from apostrophes in Claude output → switched to ###DELIMITER format
- Regex character class range errors from Unicode chars in source file
- QR code overflow → format redesign

---

## Stack

| Layer | Tech |
|---|---|
| Frontend | React 18 UMD (no build) |
| AI | Anthropic Claude Sonnet 4 |
| Compression | LZString |
| Export | SheetJS XLSX |
| Persistence | window.storage (Anthropic artifact cloud) |
| Hosting | GitHub Pages |

No backend. No database. No auth. One file.

---

*Built entirely through Claude.ai — April 2026*
