# 2k26 GM Grader Tool

### A browser-based tool for NBA 2K26 My League / franchise mode players who want their GM decisions graded like the real thing. Drop your raw notes — a trade, a signing, a draft pick — and the tool grades it on realism and strategic quality, generates insider reactions from a rotating pool of real NBA journalists.

**Live demo (Claude account required):** <https://claude.ai/public/artifacts/501a6ba2-2252-42e2-9454-4cf4daf41f31>

---

## What it does

A browser-based tool for NBA 2K26 My League / franchise mode players who want their GM decisions graded like the real thing. Drop your raw notes — a trade, a signing, a draft pick — and the tool grades it on realism and strategic quality, generates insider reactions from a rotating pool of real NBA journalists.

* A letter grade (A–F) for realism and GM decision quality
* Insider reactions from randomized journalists (Woj, Shams, Zach Lowe, etc.)
* An AI-generated GM Summary characterizing your organizational mode
* Rumors tab that feeds into your next move analysis — with resolve/archive to keep intel current
* A Consult tab — pick the brains of your team's legends and front office before making a move
* Power Rankings vs all 29 real NBA GMs and Presidents (recency-weighted, move-type adjusted)

Choose your team from all 30 NBA franchises. Each team has its own org voice pool (front office, coaches, legends). Switching teams clears your session — each franchise run is isolated.

---

## How to use it

Open the live link above. A Claude account is required — the tool runs inside Claude.ai's artifact viewer.

**Sync Code** = your save file. Copy it before closing your session. Paste it back to restore everything — moves, rumors, grades, all of it. Think of each sync code as a separate saved game, one per team. Pasting a code auto-selects your franchise — no re-selection needed.

---

## How this was built

This entire tool was built through natural language conversation with Claude (claude.ai) — no IDE, no terminal, no prior codebase.

Every feature, bug fix, and architectural decision was done through iterative prompting across a single project session:

* React 18 (no build step — UMD via CDN)
* Anthropic Claude Sonnet 4 via /v1/messages API
* LZString for sync code compression
* SheetJS for Excel export
* Single HTML file — ~106KB, zero backend

**Features shipped through AI prompting:**

* Move analyzer with strict grading prompt
* Rotating insider pool (16 journalists, 8 per side, 3 per call)
* GM Summary + Situation Room (auto-generated on each move/rumor; regenerates on restore if cache is empty)
* Consult tab with delimiter-based parsing (not JSON — avoids apostrophe corruption)
* 30-team org voice pools with front office / coach / legend tiers
* Player valuation framework (contract value, player tier, media perception)
* Sync code system with team identity baked in — auto-selects franchise on restore
* Recency-weighted Power Rankings with move-type multipliers (Trade 1.5×, FA Signing 1.3×, etc.)
* Rumors resolve/archive system — resolved rumors excluded from future analysis
* Sync status indicator (✓ dot on modal, data-tip tooltip, CSS-based — not native title attr)
* GM identity rule injected into all prompts — no real GM names referenced in reactions
* Unsaved changes warning (red indicator on Sync button)

---

## Stack

| Layer | Tech |
| --- | --- |
| Frontend | React 18 UMD (no build) |
| AI | Anthropic Claude Sonnet 4 |
| Compression | LZString |
| Export | SheetJS XLSX |
| Typography | Barlow + Barlow Condensed (Google Fonts) |
| Persistence | window.storage (Anthropic artifact cloud) |
| Hosting | GitHub Pages |

No backend. No database. No auth. One file.

---

*Built entirely through Claude.ai — April 2026*
