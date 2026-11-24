# Fallbacks: each step's method when its skill isn't installed

The strategist hands each step to a specialist skill when it is installed (its method is deeper). When it isn't, run the step with the tools below and this short method. Costs checked in `packages/core/src/plans.ts` PRICES and the tool files, 23 Nov 2026.

## Competitors' ads (competitor-ad-teardown, winning-ads-finder)

1. `compare_competitor_ads` with the competitors and the market (1 each with ads). Read `live_ads_total` (Meta's count; `live_ads_more` means a floor: "60+"), longest run, launches in 7 days, scaled ads. Never report `ads_returned` as their total.
2. The main one in full: `find_competitor_ads` competitor + country (1), then `more_from` its `search_id` with `offset` to page (free). Check the sample first: if most ads read are under 30 days while the account is old, the read holds only the newest ads; say so.
3. Age cohorts (Motion competitor-watch): 90+ days or 60+ with 3+ copies = proven (High); copies still launching on an old creative = being scaled (High); 30 to 90 = likely (Medium); under 14 days in a same-day batch = in test (a bet); catalog or dynamic = ignore for creative.
4. Code every ad's copy: angle, persona, awareness, format, offer, proof type. What 2+ competitors kept 60+ days is table stakes; what one kept alone is owned.
5. Persona or "Dr." pages, creator pages and partner pages: find the brand behind them by landing domain; `find_advertiser` (free) for the brand's own page.
6. Never describe a video from its copy: brands put one proven text over very different videos.

## What buyers say (customer-review-mining)

1. Reuse first: a "Customer language" file or a collection on the same topic in the last 7 days is free.
2. `collect_customer_comments` with the topic as buyers say it, sources that fit: `reddit` + `youtube` default; `tiktok_shop` (verified buyers) for consumer products; `trustpilot` for brands and stores; `app_store`, `google_play` for apps (free). About 1 credit a page, at most about 12 pages.
3. `get_customer_comments` (free, `wait_seconds` up to 50) sorted by likes, read 100 to 150; tag pains, desires, objections, failed alternatives, identity words.
4. `count_mentions` (free) with 4 to 8 phrasings per theme; check `matched_terms` and drop a term that catches the wrong comments (in test 1, "dose" caught the brand "Everyday Dose" and a "Weekly Dose of Truth" newsletter). Check `by_page`: a theme where most matches sit on one page (30 of 39 "weight" comments in one weight-loss thread) is that thread's topic, not the market's. State "N of M comments".
   `get_customer_comments` with `limit` 120 can exceed the client's size limit (53,586 characters in test 1): ask for 50 at a time, or read the saved file.
5. Buyers (reviews, verified purchases) outrank browsers (Reddit, video comments) for what drives a purchase; browsers tell you objections before purchase. Keep them apart.
6. Quote verbatim with likes and link; never invent or polish a quote.

## Angle map (ad-angle-gap-finder)

Cross the ad codes with the buyer themes on one codebook. **Open:** buyers raise it (3+ people, 2+ sources) and no competitor ad says it, or only one is testing it. **Trust gap:** competitors claim it and buyers contradict it: win with proof, not volume. **Counter-position:** a competitor sells as a feature what buyers blame. **Crowded / table stakes:** 2+ competitors own it. **Over-bought:** claimed heavily, rarely raised. **Off-limits:** needs a regulated or unprovable claim. Before ranking a gap ask why nobody runs it: hard (a real opening), worthless or forbidden (not). Absence rests on the ads read: Medium at most on a thin read.

## The ads that matter, watched (analyze-ad)

`break_down_ads` up to 10 ids per call, 3 at a time when the videos are long (1 each, free within 30 days and for copies of a watched creative; `still_watching` ones come back free on a later call, `credits_pending` counts them). For each: hook in three layers with times, beat map, measured pacing from `pacing` (confirm 0 or 1 cut readings on the sheets), the angle named precisely, awareness, proof, offer, the load-bearing element. Save each with `save_ad_dna` (free). Copy only (nothing downloadable): say it wasn't watched.

## What spreads organically (viral-video-hooks)

`find_viral_videos` (up to 3; free within 72 h). Drop off-topic videos and say how many. `views_per_follower` is not lift: `get_creator` (1) gives `median_views`; lift = views ÷ median, with the base (`posts_read`). Lottery accounts (tiny, one hit) and paid or Shop-affiliate posts (`sponsored_breakdown`) are named. Take hooks and formats, never sales claims.

## Writing the work (ugc-creative-brief, ad-hooks-and-copy)

Brief template in `memory.md` §5. Hooks in three layers, each tested as its own ad; lines from verbatim customer words where possible, tagged with their source; a do-not-say list (health, weight, income, guarantee claims, competitor names, the reference's own words). Statics: headline 5 to 8 words that stands alone.

## Own results and fatigue (creative-fatigue-check, weekly-ad-account-report)

`get_my_ads` (free) last 7 or 30 days with `compare_previous`; `daily: "ad"` for a trend. `get_my_ad_breakdown` (free) at ad level, `time_increment: "7"`, for frequency, CTR, CPM and video fields. Fatigue (H): frequency rising past about 3 in 7 days on cold audiences with CTR falling 20%+ from the ad's own first week and CPM steady or rising; rule out a budget change, a placement shift and seasonality first. Verdicts: keep, refresh the hook, refresh the body, replace with a new angle. Real versus noise: a CPA change on fewer than about 30 conversions a side is noise (H).

## What changed this week (weekly-competitor-ads-digest)

`whats_new` with each `tracking_id` (free): `live_ads_change`, `new_ads`, `crossed_30_days`, `crossed_90_days`, and `stopped_ads` only when the whole account fits one read (otherwise `stopped_note`). "Only one check so far" = no delta yet. A launch batch of 3+ on one theme is a coordinated bet; an ad crossing 30 days is a new likely winner: watch it. Without tracking: `compare_competitor_ads` again and diff against the run log's counts (say it's two snapshots, not tracked history).
