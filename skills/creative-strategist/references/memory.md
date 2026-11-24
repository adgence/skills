# The brand's memory: what is saved where, and how it is read

The strategist's memory lives in Adgence, not in the chat, so any later run (another chat, a scheduled task, a teammate on the same workspace) picks up the thread. Boards are free to read and write.

## 1. Layout

One board per brand (agency: per client), named **"Strategy: {brand}"**, with these files (folders). Each run adds a new dated note; `get_board` returns notes newest first, so the first note in a file is the current one and older ones are the history.

| File | What one note holds | Written | Read |
|---|---|---|---|
| **Strategy file** | The brand's current strategy in full (section 2) | Kick-off; weekly only when a belief changed | Every run, first |
| **Test plan** | Every test with status (section 3) | Every run with something new to read | Every run |
| **Learnings** | What this run learned, with evidence and confidence (section 4) | Every run with something new to read | Weekly loop |
| **Concepts and briefs** | One `script` item per concept: the card, and the full brief for the ones briefed (section 5) | Every run | When briefing or judging a test |
| **Evidence** | The competitor ads and customer comments the findings stand on, saved as items with `data` so they show with their image | Kick-off; weekly when new | When a finding is questioned |
| **Run log** | Date, mode, depth, what was read, every id, credits (section 6) | Every run | Weekly loop: ids and last date |

Also read, never rewritten by this skill: the board **"Brand"** (the brand note other skills share), **"Customer language"** files from customer-review-mining, **"Competitor watch"** from the weekly digest, teardown boards ("{competitor} teardown"), and the ad library (`my_ad_library`, Creative DNA of every watched ad, searchable by brand, hook type, format, awareness).

`save_to_board` takes `board`, `file` and up to 50 `items`; one call per file. The same title and text twice is skipped, so every note's title carries the date. A note holds up to 20,000 characters: keep the strategy note under about 8,000 so a weekly read stays light.

## 2. Strategy file note (title: "Strategy {brand} {date}")

```
STRATEGY: {brand}, {market}, as of {date} ({kick-off | updated from weekly run})
Product and offer: {what, price, offer, guarantee}  [read: brand note | site | user; MISSING: ...]
Budget band: {under $10K | $10-50K | $50K+ | not given}   Ad account: {connected: platform, id | none}
Buyers (personas): 1. {persona}: {situation}, words they use: "{quote}" ({n} of {base}) ...
Pains / desires ranked: {theme}: {n} of {base} comments ({source}) ...
Objections: {objection}: {n} of {base}
Proof the brand holds: {list} | MISSING: {what we'd need}
Competitors and what they own: {brand}: {live ads, as of date}; owns {angle} ({evidence: ad id, days live}); crowded on {angle}
Angle map: OWNED {...} | CROWDED {...} | OPEN {...} | OFF-LIMITS {...}  (each with count and base)
What has been tested: {own ads: concept, result | none known}; market structures watched: {scan ids}
Beliefs we're betting on: 1. {belief} (confidence, evidence) ...
Open questions for the user: ...
```

## 3. Test plan note (title: "Test plan {date}")

```
TEST PLAN as of {date}. Budget band {...}. Baseline: {own: hook rate x, CPA y as of date | none: rules below}
| # | Concept | Varies | Week | Budget share | Success measure | Kill rule | Read on | Status |
| T1 | Dose-first | 3 hooks, 1 body | 1 | 50% | hook rate > baseline +20%, CPA <= target | 2x target CPA, 0 conv | {date} | planned / running / confirmed / killed / inconclusive (why) |
```

## 4. Learnings note (title: "Learnings {date}")

```
LEARNINGS {date} ({kick-off | weekly})
Results (own account): {test}: {Confirmed | Killed | Inconclusive}: {numbers, spend, dates} (confidence)
Market bets: {signal}: {what it suggests} (bet, confidence, evidence)
Beliefs changed: {old} -> {new}, because {learning}
```

## 5. Concept card and brief (one `script` item each; title: "{date} C{n}: {concept title}")

Card: persona, pain in their words, awareness, angle, mechanic, format, hooks H1 to H3 (first frame / on-screen text / spoken), proof, offer and CTA, grounded in, hypothesis, difficulty, why it can fail, test ID.

Full brief (Motion brief-template, adapted): CONCEPT OVERVIEW (what the ad is and why it should work, 1 to 2 sentences) · COPY (video: each beat with timestamps, spoken line in quotes, on-screen text; static: headline, subhead, supporting line, CTA; no placeholders except MISSING facts) · VISUAL APPROACH (opening shot, setting, pacing and cut timing, production style and audio, tone arc; specific enough that two editors make the same thing) · DELIVERABLES (number of assets, 9:16 master and other ratios, length, hook versions as separate files) · REFERENCE (an ad from the evidence or the library, with link, and what to take from it) · DO NOT SAY (claims and words to avoid, with why) · SUCCESS METRICS (what to watch, the threshold, when to read, what to fix if it misses: low hook rate = new opening; hook good, hold poor = rework the middle; clicks but no sales = page or offer).

## 6. Run log note (title: "Run {date} {mode}")

```
RUN {date} {kick-off Quick | kick-off Full | weekly}, mode {founder | strategist | agency | buyer}, market {country}
Read: {what, with bases}
Ids: compare search_ids {brand: id}, find_competitor_ads search_id {id}, collection_id {id}, scrape_ids {...}, tracking_ids {brand: trk_...}, scan/ad ids watched {...}
Not read: {what and why}
Credits: {sum of credits_charged + credits_pending}; reused free: {what}
Next run: {date}; reads {tracking ids}; refresh {source} if older than 7 days
```

## 7. Reading it back (weekly loop)

1. `get_board` board "Strategy: {brand}", file "Run log": the newest note gives the date and ids.
2. `get_board` same board, file "Strategy file", then "Test plan" and "Learnings": the newest note of each.
3. A file missing or empty: say so and rebuild that part from the others; a board missing: run the kick-off.
4. Figures in memory are quoted with their date ("Crave ran 120 live ads on 23 Nov"); the delta is today's read against that.
