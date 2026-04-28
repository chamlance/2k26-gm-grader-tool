# Data Flow

How data moves through the 2k26 GM Grader Tool — from user input to AI response to persistent storage.

---

## 1. Move Analysis Flow

The core feature. Triggered when user clicks **Analyze Move**.

```
User types GM notes (free text)
        │
        ▼
buildPrompt()
  - Attaches move history (last 8 moves for context)
  - Attaches active rumors (last 5 as context)
  - Picks 3 random insiders from each pool (Bulls + League)
  - Injects strict grading instructions
        │
        ▼
POST /v1/messages → claude-sonnet-4 (1600 tokens max)
        │
        ▼
Response: raw JSON string
  {
    moveType, tradeRealism { grade, score, summary },
    gmGrade { grade, score, summary },
    bullsReactions [ { insider, publication, reaction } × 3 ],
    otherTeamReactions [ ... × 3 ],
    capAnalysis, verdict
  }
        │
        ▼
Parse + sanitize → move object created
        │
        ├──→ setMoves([move, ...moves])   ← updates React state
        ├──→ setCur(move)                 ← sets Last Analysis view
        ├──→ setTab("result")             ← navigates to result
        ├──→ persistMoves()               ← writes to window.storage
        └──→ generateGmSummary()          ← triggers summary API call
```

---

## 2. GM Summary Flow

Triggered automatically after every move analysis and after every move deletion.

```
moves[] (latest 5)
        │
        ▼
POST /v1/messages → claude-sonnet-4 (800 tokens max)
  Prompt: "Characterize this GM in 3-5 sentences"
        │
        ▼
Response: plain prose text
        │
        ├──→ setGmSummary(text)
        └──→ wSet(GMSUMM_KEY, text)       ← persists to storage
```

---

## 3. Rumor + Situation Room Flow

```
User types rumor → Save Rumor
        │
        ▼
Prepend to rumors[]
        │
        ├──→ persistRumors()
        └──→ generateRumorSummary()
                │
                ▼
        POST /v1/messages → claude-sonnet-4 (800 tokens max)
          Prompt: "Characterize org situation based on these rumors"
                │
                ▼
        Response: plain prose
                │
                ├──→ setRumorSummary(text)
                └──→ wSet(RUMSUMM_KEY, text)
```

**Rumors also feed move analysis context:**
When `buildPrompt()` runs for a new move, it reads the active `rumors[]` array and appends them to the prompt — so insiders react to the war room intel, not just the move itself.

---

## 4. Consult Flow

Triggered when user clicks **Consult Org** on a proposed (not yet executed) move.

```
User types proposed move (free text)
        │
        ▼
pickConsultVoices()
  - Reads TEAM_DATA[selectedTeam].voices
  - Random picks: 3 front office, 1 coach, 3 legends
        │
        ▼
Build prompt with:
  - Player valuation framework (tier, contract, media, exec consensus, recency)
  - Voice list (name + role, 7 total)
  - ###VOICE / ###QUOTE delimiter format instructions
        │
        ▼
POST /v1/messages → claude-sonnet-4 (800 tokens max)
        │
        ▼
Response: plain delimited text (NOT JSON)
  ###VOICE:Jerry Krause
  ###QUOTE:reaction text here...

  ###VERDICT:league view...
  ###PUSH:PUSH IT
  ###REASON:one sentence...
        │
        ▼
Parser: indexOf("###VOICE:") + indexOf("###QUOTE:")
  - No JSON.parse() — immune to apostrophes and special chars
  - Falls back to first-name matching if name not found exactly
        │
        ▼
setConsultResult({ reactions, leagueVerdict, pushVerdict, pushReason, voices })
```

**Why delimiters instead of JSON?**
Claude's natural language output (apostrophes in *"it's"*, em-dashes, smart quotes) repeatedly corrupted JSON.parse(). The delimiter format is structurally immune — a `'` or `—` inside a `###QUOTE:` block is just text.

---

## 5. Sync Code Flow

The manual data bridge between sessions and devices.

```
ENCODING (copy):
moves[] + rumors[] + selectedTeam
        │
        ▼
JSON.stringify({ m, r, t, v:3 })
        │
        ▼
LZString.compressToEncodedURIComponent()
        │
        ▼
"Q..." string (300-500 chars)    ← user copies this
        │
        ▼
User pastes in iMessage / Notes / anywhere

DECODING (paste):
"Q..." string
        │
        ▼
LZString.decompressFromEncodedURIComponent()
        │
        ▼
JSON.parse({ m, r, t, v })
        │
        ├──→ setMoves(d.m)
        ├──→ setRumors(d.r)
        ├──→ setSelectedTeam(d.t)         ← team identity restored
        └──→ all persisted to window.storage
```

**Team identity in sync code:**
Starting from v3 of the sync format (`v:3`), the team ID (`t`) is embedded. Restoring a code automatically sets your team. Old codes (v:2) have no team → the app forces team picker after restore.

---

## 6. Power Rankings Flow

Pure client-side computation, no API call.

```
LEAGUE_GMS[]                     ← 29 real GMs with curated baseline scores
        +
{ name:"YOU", score: avgGM }     ← computed from moves[]
        │
        ▼
Sort descending by score
        │
        ▼
Find index of user row → userRank
        │
        ▼
Compare to previous move's avg → rankDelta (▲ or ▼)
        │
        ▼
Render ranked table
```

---

## API Call Summary

| Trigger | Endpoint | Max Tokens | Format |
|---|---|---|---|
| Analyze Move | /v1/messages | 1600 | JSON |
| GM Summary | /v1/messages | 800 | Plain prose |
| Rumor Summary | /v1/messages | 800 | Plain prose |
| Consult Org | /v1/messages | 800 | ###Delimited text |

**Important:** All calls are user-triggered. None run in the background. None run concurrently. The app waits for one to complete before enabling the next.
