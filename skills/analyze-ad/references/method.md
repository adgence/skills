# The one-ad method

How a senior creative strategist reads one ad: what it's doing, why it works on the buyer (or doesn't), how sure you can be, and what to do with it. Sources: Motion's analyze-ad skill (find the ad, context first, the "why", the call, the multi-ad flow) and its creative-strategist references (the 4-question evaluation framework, metric-to-question correlations, Ready / Iterate / Rethink, performance context, the insight quality bar); Adgence's creative-strategy, copy-and-scripts, competitor-analysis and my-ads notes; claude-ads (observed vs inferred); the competitor-ad-teardown and ugc-creative-brief methods; and five live Adgence test runs on 23 Nov 2026 (a Javvy protein coffee Meta ad, a viral skincare TikTok, a Grammarly Google search ad, an SPNutrition comparison static and its catalog ads, and three Javvy ads in a row). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What each source can and can't say

| Source | Read | Never visible | Watch out |
|---|---|---|---|
| Meta Ad Library | Creative, copy, headline, CTA, landing URL, start date, days live, platforms, copies (`same_creative_count`), `creative_id` | Spend, impressions, results, targeting, comments | Days live counts from the ad's start: the creative can be re-edited since (the Javvy 95-day ad carries a Black Friday sticker). A brand's ads can run under creator, persona or partner pages |
| TikTok or Instagram post | The video, caption, views, likes, comments, the creator | Whether it was boosted or paid (unless tagged), sales | A pasted post link may come back without view counts: the post's row in `get_creator` has them |
| Google Ads Transparency | The ad as rendered (one image), format, first and last shown, advertiser legal name | Keywords, spend, position, the full asset set, landing path | A search ad is **one combination** of the advertiser's headlines and descriptions. First shown = the asset set's start, not this wording's |
| Catalog or dynamic ads (`catalog` flag) | That a feed campaign runs, and for how long | Any written creative: products, pictures and prices come from the store feed | Never pay to watch them; answer from the list |
| The user's own ad | Everything above plus spend, impressions, clicks, CTR, CPA, ROAS, video plays, ThruPlays, comments, learning status | Nothing material | Small samples swing; learning phase; funnel stage |

Run time means the advertiser keeps paying for it. It does not mean it's profitable (Motion: "you're reading bets, not results").

## 2. Context before opinions

Motion's Phase 1 puts the ad in its hierarchy before judging it. For a public ad, the hierarchy is the advertiser's account.

### 2.1 The evidence ladder for a competitor ad (H)

| Signal | Reads as | Confidence it's a kept winner |
|---|---|---|
| 90+ days live, or 60+ with 3+ copies | They keep paying for it | High |
| New copies of the same `creative_id` launched in the last 14 days on an old creative | They are scaling it now | High (stacked with run time) |
| One of the account's few long runners (e.g. 3 of 46 past 90 days) | A protected anchor | High |
| 30 to 90 days | Survived the first test | Medium |
| Under 30 days, or in a same-day batch | A bet | Low: "a test, not a winner" |
| Copies counted only, no run time | Pushed, not proven | Low to medium |

State where it sits: "one of Javvy's 3 ads past 90 days out of 46 live (Meta's counts)". `brand_read` gives `live_total`, `running_30_days_plus`, `running_90_days_plus`.

### 2.2 Pages behind the ad

Brands run ads under creator, persona and partner pages (Javvy under "Marta Mazur" and "Best Coffee Recipes"; SPNutrition under "Dr. May Garcia"). When `resolved_page` was matched by landing domain, or the page is a person's name, find the brand's own page (`find_advertiser`, free) and read that: it's the account that shows what they keep paying for. A "Dr." page with no real doctor behind it is an implied endorsement: flag it, never copy it.

### 2.3 Copy is not the video

The `hook` field in ad lists is the **primary text**, not the video's opening. Brands put one proven text over many different videos. Javvy's three long runners share the cafe-swap copy ("keeps you full", "$6/day… wired and hungry") over three unrelated videos: a one-take recipe, a packing-room dare series, a creator's dessert recipe. **Never say a sister ad "opens on" anything until it's watched**; say "their copy says". When the copy is shared and the videos differ, the proven thing is the **message**, and the execution is open: that's a finding in itself.

### 2.4 Organic posts

- **Lift = views ÷ the creator's `median_views`** (`get_creator`, recent posts), never views per follower (that flatters small accounts: @laurenlietzow read 32x by followers, 11x by median).
- Over 2x the median (H) is a real outlier for the hook and format; it proves attention, not sales.
- The median is read from posts after the hit and may include reply videos riding it: say so.
- Comments: what viewers ask ("what did you use?") tells you what the video makes them want; what they doubt ("go close up again") tells you what a sceptic will say about your version.

## 3. Read the creative

### 3.1 The hook in three layers, with times
First frame (what's moving at 0.0 s), on-screen text (words and when they change), spoken line (from the transcript or captions), plus the sound. Quote exactly. Read the 5 hook frames (0, 0.5, 1, 1.5, 2.5 s) and the first contact sheet.

### 3.2 The beat map
Hook, Problem, Solution or Demo, Proof, Offer, CTA, each with timestamps from the sheets and `cut_times`. Mark the product's first appearance, the pack readable, the proof moment, the offer and the CTA.

### 3.3 Pacing, measured, never estimated
`pacing.seconds`, `cuts`, `cut_times`, `average_shot_seconds`, `shots_in_first_3s`; words per second = transcript words ÷ seconds. Cut detection counts a picture change over 30%: it misses jump cuts in the same framing and can count a whip. Confirm 0 or 1 cut readings on the sheets ("1 detected cut, one continuous take, checked on the frames"). Test spread: 1 cut in 34.2 s, 12 in 40.5 s, 18 in 39.8 s, all long runners for one brand: cut speed is rarely the load-bearing element.

### 3.4 Name the angle precisely
Not "UGC" or "testimonial" (those are formats). The angle is the core truth for one pain or desire and one persona: "habit swap: the cafe drink is the enemy (price, calories), protein is the reason to believe"; "label check: your current bottle is half the dose"; "persona + one job: students who need citations". Use the layer stack: angle, mechanic, hook, format (creative-strategy §1).

### 3.5 Awareness, proof, offer
- Awareness (Schwartz): unaware, problem-aware, solution-aware, product-aware, most aware. Say what the ad assumes the viewer already knows.
- Proof: reviews, demo, numbers, expert, comparison, none. A demo is proof of ease, not of results.
- Offer and CTA: in the video, in the copy, on the button. A sale layer on old footage is a finding ("they bolt the sale onto a proven video").

### 3.6 The load-bearing element
The one thing that, removed, breaks the ad. Tests found: benefits disguised as guesses (the Starbucks question ad); the product's absence (the skin timeline makes viewers ask); the checkable number (200 vs 400 mg); persona in the headline (Grammarly). It decides what the user must protect when they borrow.

### 3.7 The buyer's mind
Motion's insight bar: explain what happens in the viewer's head, not what the ad looks like. "It reads like gossip, so nobody's sales guard goes up" is an insight; "it uses a question hook" is a label.

## 4. The four questions (Motion's evaluation framework)

Ranked: failing an earlier one overrides strengths later.
1. **Does it make sense fast?** In 1 to 3 seconds the viewer knows what this is about.
2. **Will the right person feel it's for them?** It names their moment or situation, not a demographic.
3. **Will they believe it?** Claims feel credible; proof is present; nothing sounds like a salesperson.
4. **Will they take the action?** A clear next step, a reason to act now, and a page that delivers what the ad promised.

### 4.1 Competitor and organic ads: judged, labelled as inferred
Answer each from the frames and copy, and say it's a judgement: no performance data exists. Run time, copies and comments support it; they don't prove it.

### 4.2 The user's own ad: diagnosed from the numbers
Fields: Meta `get_my_ads` (spend, impressions, CTR, CPC, CPM, conversions, ROAS) and `get_my_ad_breakdown` at ad level with `video_play_actions`, `video_thruplay_watched_actions`, `video_p25_watched_actions`, `video_p100_watched_actions`, `inline_link_click_ctr`; TikTok `video_watched_2s`, `video_watched_6s`, `average_video_play`.
- **Hook rate** = 3-second views ÷ impressions on Meta (2-second views on TikTok). Roughly 25 to 30%+ is strong on Meta (H, my-ads notes); compare within the account first. Meta's default fields give plays and ThruPlays: if the 3-second figure isn't in the result, say which field you used.
- **Hold rate** = ThruPlays ÷ 3-second views (or p50 to p75 views).

| Pattern | Which question fails | Fix first |
|---|---|---|
| Low hook rate | Q1: the opening doesn't stop or doesn't explain | New first 3 s (visual, text, spoken), same body |
| High hook, low hold | Q1 passes, the middle loses them | Cut or reorder the body; pay off the hook sooner |
| High hook and hold, low CTR | Q2 or Q3: they watch but don't act | A clearer reason to click, proof, a stronger CTA |
| High CTR, low conversion | Q4: the page doesn't deliver what the ad promised | Landing page or offer, not the ad |
| Strong engagement, low conversion | Q3: entertains, doesn't persuade | Add proof and the product's mechanism |
| Spend falling, metrics stable | Not Q1 to Q4: fatigue or saturation | New hook or new format on the same angle |

**Context before blaming the creative** (Motion): funnel stage (retargeting converts by default; compare prospecting with prospecting), budget starvation (a new ad beside proven winners may never get spend), learning phase (about 50 optimisation events in 7 days on Meta), sample size (under ~1,000 impressions for CTR or under 1 to 2x target CPA for conversions is too early, H), test fairness, and a testing versus scaling campaign (from names: "Testing", "Scaling", "CBO", "ABO", "Retargeting").

## 5. The call

**Competitor or organic ad:**
| Call | When |
|---|---|
| **Copy the structure** | The format and mechanic fit the user's product and don't need the advertiser's brand, claims or footage to work |
| **Borrow one element** | One layer is strong (a hook, a format, a reveal) and the rest doesn't fit, or carries risk |
| **Skip** | It needs their brand equity, a claim the user can't make, or it's a sale-only or catalog ad |

**The user's own ad (Motion):**
| Call | Means |
|---|---|
| **Ready** | Keep running; notes are polish |
| **Iterate** | Workable; fix in priority order: conversion blockers (Q4), then attention (Q1), then trust (Q3). Specific enough to act without a call |
| **Rethink** | The angle, audience signal, proof or format is wrong; surface fixes won't save it. Don't soften it |

Then: what to copy, what to fix, **three tests** (each a hypothesis, the variable, the measure: hook rate for a new hook, hold rate for a new body, CPA or purchases for a new concept), and a draft of the user's version with MISSING where their facts are needed.

## 6. Formats

- **Video:** the full read above.
- **Static image:** one-glance test (is it understood in 1 second?), headline bar, visual hierarchy, proof element, product readable. Pacing doesn't apply. Note AI-made images, especially when they recreate another brand's pack.
- **Carousel:** first card is what's returned; read the copy for the rest; say only the first card was seen.
- **Catalog or dynamic** (`catalog` flag): feed content, no written creative. Say what it tells you (a feed campaign has run N days) and don't watch it.
- **Google search ad:** persona and job in headline and description; the run time is the asset set's; keywords are invisible. Read 2 or 3 of the advertiser's still-showing ads before naming what they *don't* say: Grammarly's student ad had no offer, but its next ad led with "Free Proofreading Tool" and sitelinked "Free Essay Checker". One combination is one combination. The user's own search terms (`get_my_search_terms`) show which queries they lose.
- **Organic post:** lift over median, comments, and what changes when it becomes a paid ad (it loses "not selling"; a personal claim becomes the brand's claim).

## 7. The multi-ad flow (Motion: users paste 20+ ads in a session)

- From the second ad: a short block: the call, why in one line, one flag, the cost. No repeated method talk.
- Reuse: ads watched in 30 days and copies of a watched creative are free; say "already seen, free".
- After 3 ads, or when asked, **the pattern**: what's shared across them (message, product timing, offer layer), what isn't (format, cut speed), with the base ("4 ads, 3 distinct videos: Medium at most"). Lead with the one finding that changes how to read all of them.
- **Correct earlier answers plainly** when a later watch shows they were wrong ("I said their long runners all open on the same enemy; that was their copy, not their videos").
- Offer: compare them, brief the pattern, or the whole account.

## 8. Honesty and the quality bar

- Label every figure **read** (from a tool), **worked out** (say how: "95 words ÷ 34.2 s = 2.8 a second") or **MISSING**. Never invent transcript text, numbers, spend or results (Motion, claude-ads).
- Confidence on every finding, with what it rests on.
- Two insight tests (Motion): remove the numbers, and the reasoning still holds; and it must not fit any brand in the category. A third (the action test): it changes what the user does next.
- Lead with the one finding that changes the most; give the rest room in proportion. If everything feels equally important, judgment is missing.
- Cut: descriptions of what the ad looks like with no "so what", "they use UGC", run time called profit, advice that fits any brand.
- Flag what not to copy: competitor names and packs, effect or health claims, unbacked numbers (the "40 G protein" title over 20 g of product), profanity, doctor-sounding pages.
