# Product Thinking

## The Problem

NBA 2K franchise mode has a genuine depth problem for serious players. The game gives you all the tools of a real GM — trades, signings, extensions, draft picks, cap management — but zero feedback on whether your decisions are actually good.

You can trade away a first-round pick for a player on a max contract with two years left and the game will let you do it without blinking. There's no mechanism that tells you: *this is an overpay, 28 out of 30 real GMs would reject this, and your cap situation in year three is going to collapse.*

The gap: **franchise players want to feel like real GMs, but the game doesn't simulate the accountability that real GMs face.**

---

## The Insight

Real GMs operate inside a system of accountability that the game ignores:

- **Media pressure** — every move gets publicly graded by journalists within hours
- **Org alignment** — ownership, coaches, and franchise legends all have opinions
- **League perception** — your reputation among other GMs affects future deals
- **Cap reality** — the financial logic of every move is scrutinized in public

The insight was that AI could simulate all four of those accountability mechanisms simultaneously — and do it in plain English, the same way a player would actually think about a move.

---

## The Solution

A GM grader that takes free-text notes (exactly how a real decision-maker thinks, not a dropdown menu) and returns structured accountability:

1. **Realism grade** — would this trade actually happen in the real NBA?
2. **GM decision grade** — separate from realism: is this smart for your franchise even if it's real?
3. **Insider reactions** — randomized pool of real journalists, both team-beat and league-wide
4. **Cap implications** — the financial logic, not just the headline
5. **Org consultation** — your franchise's own legends and executives weighing in before you pull the trigger

The key UX decision was **free text input**. No dropdowns, no forms, no structured fields. You write exactly how you'd describe the move to a friend. The AI figures out the rest.

---

## Target User

A franchise mode player who:
- Takes the GM simulation seriously, not just the on-court game
- Has opinions about real NBA transactions and wants to apply that lens to their game
- Is frustrated that the game treats a terrible trade the same as a great one
- Wants the *feeling* of running a real front office, not just clicking through menus

This is not a casual player. It's the person who spends 45 minutes in the trade negotiation screen, reads cap analysis, and has a genuine opinion about whether a deal makes sense.

---

## Key Product Decisions

**Free text instead of structured input**
The alternative was a form: pick move type, enter player names, select contract years. Rejected because it breaks the immersion. Real GMs don't think in dropdowns. The grader had to feel like talking to a scout, not filling out a form.

**Strict grading rubric**
Early versions graded almost everything A or B. That made the tool useless — if every move is good, there's no accountability. The final prompt explicitly calibrates: most moves are B or C, A-range is reserved for genuinely franchise-altering decisions. The grade has to mean something.

**Rumors as context, not decoration**
The Rumors tab could have been a note-taking feature. Instead, rumors actively feed into the next move analysis — insiders react to the war room intel, not just the move in isolation. This mirrors how real NBA transactions are processed: always in the context of what's already circulating.

**Consult before vs. Grade after**
Two separate modes for two separate moments in the decision process. Consult (before) is about getting org buy-in on a proposed move. Grade (after) is about accountability once it's done. Keeping them separate mirrors how real front offices actually work.

**Delimiter format over JSON for Consult**
A technical decision with a product rationale. Claude's natural language (apostrophes, em dashes) kept breaking JSON parsing, making the Consult feature unreliable. Switched to ###VOICE: / ###QUOTE: delimiters — structurally immune to punctuation. The product lesson: format your AI outputs around the failure modes of the medium, not the ideal case.

**Sync code as save file**
The storage limitation (window.storage resets per URL) could have been a dealbreaker. Instead it became a feature framing: each sync code is a separate save file, one per team. Multiple sync codes = multiple franchise runs you can jump between. The constraint became part of the product logic.

**30-team org voice pools**
The Bulls-specific version was functional but limited the audience to Bulls fans. Expanding to all 30 teams required curating 10-12 historically significant figures per franchise — front office builders, championship coaches, and franchise legends — with enough specificity that the voices feel authentic, not generic.

---

## What This Project Demonstrates

**Product thinking:**
- Identifying a real gap in an existing product (franchise mode feedback loop)
- Designing around user mental models (free text, not forms)
- Turning technical constraints into product features (sync code as save file)
- Calibrating AI output to be useful, not just impressive (strict grading rubric)

**AI-assisted development:**
- Complete application built through natural language prompting
- Debugging complex issues (JSON parse failures, iframe sandboxing, React hook ordering) without writing code manually
- Iterating on prompt design to get reliable, analytically grounded outputs
- Recognizing when AI output format needs to change vs. when parsing needs to improve

**Judgment calls:**
- When to use JSON vs. delimiters based on output reliability
- When to simplify UX (two options, not three) based on actual user behavior
- When a technical limitation is actually a product opportunity

---

## What I'd Build Next

- **Trade simulator** — propose a trade and see if the other team would actually accept it, modeled on real team needs and cap situations
- **Season recap** — after 82 games, grade the full GM season based on moves made vs. outcomes
- **Multi-user mode** — two players running rival franchises, with shared trade history and insider reactions covering both sides
- **Real-time player valuations** — pulling live NBA data to ground the tier assessments in current performance, not hardcoded assumptions
