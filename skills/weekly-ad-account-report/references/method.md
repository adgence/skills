# The Monday read: method

How a senior media buyer reads their own ad accounts every Monday, and decides what is a real change and what is noise. Sources: Adgence's my-ads notes (Meta's and TikTok's documentation on learning, attribution and the breakdown effect), the practitioner Monday reports in the research notes (Wupscale's four questions and weekly contents, ritiknamdev's client report format, Vicky Lalwani's cadence and conversion-lag rule, the ahmeego anomaly thresholds, Dara Denney's Monday creative report, surferstack's agency guide, Usman Akram's per-audience reports), the best existing skills read in full (Motion's weekly-performance, performance-analysis and morning-briefing; Corey Haines's ads skill with its operating check and Google Ads data notes; irina's weekly-account-summary, cpa-diagnostics and anomaly-detection; claude-ads' report and Meta audit contracts), Pipeboard's week-over-week prompts, and the code of Adgence's own-account tools (checked 23 Nov 2026). Thresholds marked (H) are heuristics: use them and say they are. The account's own history always beats an industry number.

## 1. What the data is, and what it can't say

- **Every number is platform-reported.** Meta, Google and TikTok each count a sale under their own attribution window (Meta's default is 7-day click, 1-day view; Google counts by click date over the conversion action's window, often 30 days; TikTok uses its own). Adgence doesn't pass an attribution setting, so each platform's account default applies.
- **Never add platforms together as "sales".** The same order is often claimed by Meta and Google. A cross-platform total is "platform-reported conversions", never revenue. The true blended number (MER: store revenue / total ad spend) needs the store's own revenue: ask for it or write MISSING.
- **Never sum different currencies.** Each account has its own currency (`my_ad_accounts`).
- **Recent days are incomplete (conversion lag).** Conversions keep arriving for days after the click and are booked back to the click or impression date. Last week's CPA will usually improve a little over the next days; the last 2 or 3 days are the least complete. The week before is close to settled. So a small drop in last week's results is expected, not news. See §4.
- **Freshness.** The campaign tree comes from the ad platform through Zernio's sync; `get_my_ads` says when in `numbers_as_of`. Put it in the header; if it's older than the end of the week being reported, the last days may be missing: say so. `get_my_ads` may carry a note that older history is still importing: then don't compare beyond the last 90 days.
- **Own-account alerts in `whats_new` are click-rate signals, and now say so.** "Click rate falling" means the ad's click rate over the last 3 days fell under 70% of the 7 days before, each window with 1,000+ impressions and the earlier one with 10+ clicks, with spend in the last 3 days; its text ends "A signal from clicks only, not results". "Click rate well above your account's" means a new ad (under 14 days, or new since the last sync) at 2x the account's click rate or more, on 2,000+ impressions, 30+ clicks and at least a quarter of the average ad's spend; its text ends "check its cost per result before giving it more budget". Neither looks at purchases or cost: re-judge every alert on CPA or ROAS before calling anything a winner. A "rejected" alert comes from the platform's review status.
- **Click rate on Meta.** `ctr` and `clicks` in Meta rows count every click (likes, expands, profile taps). Use link CTR (`inline_link_click_ctr`, `inline_link_clicks`, in `get_my_ad_breakdown`'s default fields) for every CTR line and the funnel split.
- **`whats_new` marks alerts as read when it shows them.** Put every alert it returned in the report; they won't show as unread anywhere else afterwards.

## 2. The week

- Default: **the last complete Monday to Sunday**, with `from` and `to`. On Monday 23 Nov 2026 that's 16 to 22 Nov, compared with 9 to 15 Nov (the compare_previous flag: the previous period has the same length and ends the day before `from`).
- `days: N` now means the last N full days ending yesterday (today is left out by default), so on a Monday `days: 7` is last Monday to Sunday. Any other day it isn't: pass `from` and `to`. `exclude_last_days` leaves out the newest days too (a lag-safe read: `days: 14, exclude_last_days: 3`).
- Also read 28 days (the 4 weeks to Sunday) once, as the account's own baseline when the user gives no target.
- Seasonal check: name the week's context. In 2026, Black Friday is 27 Nov and Cyber Monday 30 Nov; the weeks around them carry higher CPMs and sale-driven conversion rates, so compare a BFCM week with the same week last year (a 52-week read) rather than the week before, and expect January to fall back.

## 3. Where the money went, and the funnel that explains it

1. **Spend map:** campaigns sorted by spend; the few that take ~80% of spend are judged first (H). Small spenders rarely change the answer: one line for the rest.
2. **Split what behaves differently before averaging:** brand search vs non-brand on Google (brand ROAS flatters the account: report non-brand on its own line), prospecting vs retargeting on Meta, each objective apart (a traffic campaign has no CPA).
3. **Walk the funnel for every line that moved.** CPA = CPM / (10 × CTR% × CVR%) with CTR and CVR as percentages, so a change in CPA splits into its causes:
   - %change CPA ≈ %change CPM − %change CTR − %change CVR (for small moves; compute the exact figures from the two weeks).
   - **CPM up** (cost to reach people): auction pressure (season, competitors launching, a narrow audience) or weak creative quality. Not fixed in the ad.
   - **CTR down** (the creative's pull): fatigue or a weak hook. Fixed in the creative.
   - **CVR down with CTR stable** (after the click): landing page, offer, price, stock, or **tracking**. Not fixed in the ad.
   - **AOV** (purchase value / conversions) explains ROAS moves CPA doesn't.
   Name the step that moved, with both weeks' figures. That's the "why" readers pay for.
4. **Tracking check before any alarm:** conversions down 50%+ with clicks steady, or a day with spend and clicks but zero conversions on an account that always converts, points to tracking first. `check_my_ad_account` shows the Meta pixel's newest event and Google's enabled conversion actions. On Meta, pick one purchase action from the actions array (omni_purchase, or the pixel purchase, never both): adding several purchase action types double-counts sales, and "double-counted purchases lead Claude to recommend scaling" (Wupscale).

## 4. Is the change real? (the decision rules)

Weekly results are counts, and counts are noisy. The noise in a count of n is about √n, so the relative noise of a cost per conversion over two weeks is about √(1/n₁ + 1/n₂), with n the conversions in each week (H: a standard Poisson approximation; it ignores order-value spread, so ROAS is a bit noisier than this).

| Conversions in each week | A CPA or conversion change is **real** above about | **Possible** between |
|---|---|---|
| 10 | ±90% | 45% to 90% |
| 25 | ±57% | 28% to 57% |
| 50 | ±40% | 20% to 40% |
| 100 | ±28% | 14% to 28% |
| 200 | ±20% | 10% to 20% |
| 500 | ±13% | 6% to 13% |

(Real = beyond twice the noise; Possible = one to two times; below that, Noise.) Compute it for the actual counts: noise = √(1/n₁ + 1/n₂); real when |change| > 2 × noise.

- **One week against one week is not a trend** (Corey Haines's operating check). Also read the last 8 weeks at account level (`get_my_ad_breakdown`, level account, time_increment 7, free): a week inside that range is the account's normal variance even when it differs from the week before. Call a performance change Real only when it passes the noise band **and** sits outside the 8-week range, or is the third week in a row moving the same way. Facts are exempt: a budget cap, a broken page, tracking, a rejected ad, and a stop rule against target (an ad at 2 to 3× target CPA with no sale is bad even if it's "normal for itself").
- **Labels on every change:** Real, Possible (re-check next week), Noise (don't mention it in the verdict). The same rule applies to CTR with clicks as n (clicks are many, so CTR moves are often real when CPA moves aren't) and to CVR with conversions as n.
- **CPM, spend and impressions** have little sampling noise: their moves are real, but spend moves usually have a cause (a budget change, a campaign switched on or off, learning). Look for it in the change log first.
- **Conversion lag:** when the latest week is worse by an amount inside the lag the account usually shows, and the drop sits in the last 2 or 3 days of the daily series, call it "probably lag; re-check Thursday". **Learn the account's own lag:** the report saves each week's headline numbers to the board. Next Monday, re-read the previous week (same `from`/`to`) and compare with what was saved: the growth is this account's lag (e.g. conversions for the week rose from 120 to 131 a week later = +9%). Use that factor from the second report on, and say it's measured. Until it's measured, say "the latest week isn't settled yet".
- **Zero conversions is evidence only with enough clicks.** With the account's conversion rate p, a segment, ad or search term with c clicks would expect c × p conversions. Zero is a real signal when c × p ≥ 3 (under a 5% chance of zero by luck) (H). Below that, only plainly wrong intent ("free", "recipe", "jobs", "login") justifies calling it waste (Vicky Lalwani).
- **Judging one ad:** not before ~1,000 impressions for CTR, and not on CPA before it spent 1 to 2 times the target CPA (H). Two ads within ~20% of each other on few conversions are a tie.
- **Learning:** a Meta ad set needs about 50 optimisation events in 7 days to leave learning; budget jumps, targeting, creative or bid edits restart it (Meta's documentation). Meta's own verdict is now on each ad set in `get_my_ads` (`learning.status`: LEARNING, SUCCESS, FAIL = learning limited, WAIVING; unknown on paused and older ad sets). Google's is in GAQL (`campaign.primary_status`); TikTok's isn't available, so infer it from age, recent edits and conversions a week, and say it's inferred. Mark lines still learning and don't judge them; a FAIL ad set is a structure finding (too few events a week), not a creative one.
- **Breakdown effect:** Meta moves budget to where the next result is cheapest, so a placement or age group with a worse average CPA can still be efficient at the margin. A breakdown "surprise" is a lead, not a cut: test an exclusion in a separate ad set, don't cut on averages.

## 5. The ads: top, bottom, tiring

- **Top 3 and bottom 3** among ads with enough spend (§4), ranked on the account's goal (CPA or ROAS; CTR only for traffic goals). Show each with spend, conversions, CPA or ROAS, CTR and its change against last week (`previousMetrics` on the ad node).
- **Watch the best and the worst** (`watch_my_ad`, 1 credit each; image and video ads only, Google Search ads are text): hook in three layers (first frame, on-screen text, spoken line), measured pacing (cuts, shots in the first 3 s, when the product first appears), the offer. Name the difference that likely matters, as a hypothesis: "the winner shows the product at 1.2 s and states the price; the loser opens on a face for 4 s" is a testable idea, not a proven cause.
- **Fatigue** (stack signals for confidence): frequency rising (H: above about 2.5 to 3 over 7 days for prospecting), CTR down 30%+ against the ad's own earlier days while still spending, CPM flat or up, CPA up. "There is no universal fatigue frequency: compare each ad with its own history" (Whathead). Frequency comes from the Meta breakdown at ad level.
- **Hook and hold rates** for video when the platform gives them: TikTok `video_watched_2s` / impressions (hook) and `video_watched_6s` / `video_watched_2s` (hold); on Meta the default fields now carry `video_play_actions`, `video_thruplay_watched_actions`, `video_p25_watched_actions` and `video_p100_watched_actions` (3-second views sit in the actions array as video_view), so hook = video_view / impressions, hold = ThruPlays / video_view, completion = p100 / plays. Roughly 25 to 30%+ hook rate is strong on Meta (H).

## 6. Breakdowns: one surprise, not a table dump

`get_my_ad_breakdown` by placement and by country (Meta; on Google network and device, or country). A surprise worth a line (H): a segment with 10%+ of spend and a CPA 2× the account's or more with enough conversions to be real (§4), or spend with zero conversions where clicks × CVR ≥ 3. Practitioner examples of what this finds: Instagram demos at about $300 per result against Facebook at $117; Audience Network with a high CTR and a poor landing-page-view rate (Wupscale). Report one or two, with the rows behind them.

## 7. Google Ads extras

- **Before calling a term wasted, its break-even conversion rate** = CPC / target CPA (Corey Haines). If the clicks it got can't yet show whether it beats that, it's unmeasured, not failing. The search terms report also leaves out queries Google withholds: compare the report's clicks with the campaign's clicks and say what share was disclosed.
- **Conversions vs all conversions:** metrics.conversions counts primary actions only; a campaign's "conversions" can mix a purchase with a lower-value action. Check the conversion actions (`check_my_ad_account`) before calling a CPA.
- **Search terms that cost money and never convert** (`get_my_search_terms`, free): the week's top waste in one line, with the §4 zero-conversion rule; the full negatives pass belongs to the search-terms skill (every 2 weeks, Vicky Lalwani's cadence).
- **Impression share lost** (a GAQL query in `get_my_ad_breakdown`, campaign level): metrics.search_budget_lost_impression_share says budget is capping a profitable campaign; metrics.search_rank_lost_impression_share says ad rank (bid or quality) is. Only worth a line for campaigns at or under target.
- **Lag-free conversions:** metrics.conversions_by_conversion_date counts conversions on the day they happened, so last week's number doesn't change later. Use it for the week-over-week line when the account has long conversion lag (untested in a real account; check the query returns rows).
- Google's own recommendations (`check_my_ad_account`) are "often to spend more": mention only ones the numbers support.

## 8. Targets

- The user's target CPA or ROAS, or **break-even ROAS = 1 / gross margin** (40% margin: break-even ROAS 2.5) when they give a margin. Without either, use the account's own 4-week average and say "against your 4-week average; give me a target to judge against your economics".
- Budget pacing when a monthly budget is given: month-to-date spend against the share of the month gone.

## 9. Decisions

**Check in order and stop at the first real cause** (Corey Haines's operating check): did spend move (budget, cap, limited, billing), did traffic change (CPM, CTR, CPC, new placements or search terms), did conversions change (CVR, value, lag), did creative tire, did the landing page break, did tracking break. A cause higher up explains the symptoms below it.

**Rank by money, not by percent** (irina's weekly summary and CPA diagnostics: "changes that matter financially"; fires first, wins second, housekeeping third). For each campaign, the cost of its change = this week's spend − this week's conversions × last week's CPA (positive: it cost that much more than last week's efficiency would have; negative: it saved that much). The campaign with the biggest absolute figure leads the "three changes", even if a small campaign moved more in percent. High spend with a falling result is a budget leak and outranks any low-spend loser (Motion).

**One call for each account**, then one per campaign that moved (Corey Haines): **HOLD** (inside normal variance, learning, lag or too little data; say what would change the call), **FIX** (a named problem with a named fix), **TEST** (a hypothesis with a metric and a stopping rule), **INVESTIGATE** (the data can't be trusted yet: tracking in doubt, counts jumped with no business reason). INVESTIGATE beats everything: no performance call is valid while tracking is in doubt. Each call ends with three short lists: facts (in the data), hypotheses (inferred), missing data (what would change the call).

**Name the week** (Motion's weekly-performance): breakout (a new ad entered the top spenders and beats the account on the goal), fatigue (3+ of last week's top 10 ads fell 20%+ on the goal with nothing as good replacing them), shift (spend moved to a different format, angle or campaign type), launch (5+ new ads with real spend), or steady. The week type sets the verdict's story; in a steady week, look for the quiet riser or the fatigue that hasn't hit yet, and say "steady" plainly rather than inventing urgency.

Every finding ends in one of these, with the numbers that triggered it:

| Decision | When (H) |
|---|---|
| **Keep** | At or better than target, change is Noise or Real-good |
| **Watch** (re-check on a named day) | Possible change, learning, lag, or too little spend to judge |
| **Fix** | Tracking, a rejected ad, a broken placement (spend, ~0 results), a landing problem (CVR down, CTR stable) |
| **Propose a change** (needs the user's yes) | Pause: spent 2 to 3× target CPA with no conversion, or CPA 50%+ worse than the account after learning. Scale: CPA beats target 3+ days out of learning; raise 20 to 30% every 2 to 3 days, or duplicate into a new ad set. Refresh: fatigue on a former winner. The report lists these; the daily-watch skill or `change_my_ads` (preview, then confirm on a yes) carries them out |
| **Test** next week | The gap the read found, with a decision rule: hypothesis, what's tested, budget, and "call it when each version spent 1 to 2× target CPA, or after 7 days; winner = lower CPA by more than the noise band" |

## 10. The competitor line (the join only Adgence can make)

From `whats_new` on tracked competitors: Meta's live count then and now, ads launched and stopped, ads that crossed 30 or 90 days (their new likely winners), store price changes and best sellers selling out. Use it in two ways:
1. **One line of context**: "Crave launched 14 ads and stopped 9; 3 crossed 30 days."
2. **As a cause hypothesis, Low confidence**: CPM up on prospecting in the same week competitors launched big batches or started a sale points to auction pressure, not the user's creative. Never claim it as the cause; it's an inference from two public facts.

Honesty rules for this line: a tracked item with only one check so far comes back as "only one check so far: changes show from the next one": say that, never "no change". `live_ads_now` is Meta's live total; on checks made before the 23 Nov fix it reads "not recorded" (`live_ads_now_note`), and then only `ads_read` is known: say "in the ads checked". A competitor's new 30-day survivor in an angle the user doesn't run is a test candidate for next week.

**Launched and stopped, as the tool now reports them** (fixed 23 Nov 2026 after the tests showed sampling noise: two reads of Crave 7 minutes apart once gave 14 "new" ads, some live 201 days, and 16 "stopped", incl. its 502-day ads). `whats_new` now compares a tracked item only with its own previous check, in its own country, never with another tool's read; "launched" are ads whose start date is after the previous check; "stopped" is reported only when the whole account fits in one read, and otherwise it says stops can't be told from this read. Report it as stated. Two checks of a big brand still hold different samples, so "crossed 30 or 90 days" is reliable only for ads present in both: say so when it matters.

An untracked competitor read on the spot (`find_competitor_ads`, 1 credit, `detail: "compact"`) carries `brand_read` (Meta's live total, 30+ and 90+ day counts) and a `newest` list with start dates (verified live 23 Nov: Crave, US, 719 live, 541 past 30 days, 245 past 90; 10 newest started 18 to 21 Nov). Count last week's launches from `started` in that list, and say the list is the newest 50 read, not every launch.

## 11. The quality bar

**The client test** (Corey Haines): could the reader disprove this in a minute in their own Ads Manager? Volunteer the limits first (sample size, lag, disclosed search terms, platform-reported). Mark every claim verified (pulled in this run), inferred, or stale (from last week's note, with its date).

**How a metric reads as behaviour** (Motion's weekly-performance): high hook rate and low hold = the opening grabs and the middle doesn't pay off; high CTR and low conversion = the ad sells the click and the page doesn't close; spend rising on an ad = the platform found it works; strong result on low spend = a pocket the platform can't scale yet; high spend with a falling result = a budget leak. Lead with the reaction, back it with the number.

Every finding is evidenced (a row, with both weeks), labelled Real, Possible or Noise, and tied to a decision. Cut: tables restated as prose, metrics that didn't move, "CTR is 1.6%" without a so-what, generic advice, any number not retrieved. "What we did" comes only from the change log the user gave ("not invented by the model"). Data gaps get their own short section. A quiet week gets a short report: "nothing moved beyond noise" is a finding.
