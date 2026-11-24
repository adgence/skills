# The daily watch method

How a careful media buyer reads an ad account every morning and decides what to pause, what to scale and what to leave alone. Sources: Adgence's my-ads expert notes (Meta's and TikTok's own documentation on learning, attribution and the breakdown effect), Tobi Adonadon's twice-daily "nothing runs without your ok" skill (numbered moves, +20% max per change, account spending limit, daily cap, every ok logged), Ira Bodnar's hourly agent (pause over 3x target CPA after 50 conversions, +20% on 3-day winners, every action posted with its reason) and the replies to it ("set the spend cap on the ad account itself, not only in the prompt"), fekdaoui's 8am routine, the "approval fatigue" reports on Meta's own ads MCP, Vicky Lalwani's "watch conversion lag" rule, the HN runs where an autonomous agent "tries to correct things too quickly", and Wupscale's double-counted purchases that made Claude recommend scaling. It also takes from the best published skills: Corey Haines's `ads` skill and its references (the operating check's HOLD, FIX, TEST, INVESTIGATE calls, the Meta decision system's 3x data gate and frequency bands, reading Google Ads data without false conclusions), Motion's morning-briefing (the delta, not the state; a quiet day said plainly), and AgriciDaniel's claude-ads (draft by default, act only when the account still matches the draft, never delete). Thresholds marked (H) are practitioner heuristics: use them, say they are, and prefer the account's own history over any industry number.

## 1. What the data can and can't say

`get_my_ads` returns the platform's own numbers per campaign, ad set (Google: ad group) and ad: spend, impressions, reach, clicks, CTR, CPC, CPM, conversions, purchase value, ROAS, status, review state, budget and whether it sits on the campaign or the ad sets (`budgetLevel`), objective and bid strategy. It does not return:

- **Learning state on TikTok.** Meta's is read live on each ad set (`learning.status`, section 4) and Google's through GAQL; TikTok has no field. Infer it there (section 4) and say it's inferred.
- **Which event `conversions` counts.** It's the platform's roll-up. `check_my_ad_account` lists what the campaigns optimise for (`used_by_existing_campaigns`): judge each campaign on its own optimisation event, and say which one. On Meta's raw rows (`get_my_ad_breakdown`), `actions` hold several names for one purchase (`purchase`, `offsite_conversion.fb_pixel_purchase`, `omni_purchase`): **pick one, never add them up**, or purchases are counted two or three times and losers look like winners.
- **Margins, the store's own orders, stock.** Break-even ROAS and true CPA are MISSING unless the user gives them. Platform conversions usually differ from the store's numbers.
- **Change history on Meta and TikTok.** The tools can't see who edited what. On Google, `get_my_ad_breakdown` with a `google_query` on change_event (last 30 days, with a date filter and a LIMIT) shows recent edits. Otherwise the daily watch's own change log (section 8) is the history.

**Today is never judged.** Today's numbers are partial and conversions trail clicks. Read complete days only: `days: N` now means the last N full days ending yesterday (the result's `range` shows the dates), and `to` defaults to yesterday. `exclude_last_days` leaves out the newest days as well, for conversion lag. `numbers_as_of` says when the platform was last read: a read more than a day old is said in the report.

**Click rate on Meta.** Meta's `ctr` and `clicks` count every click (likes, expands, profile taps). For any CTR call use link CTR: `inline_link_click_ctr` and `inline_link_clicks` in `get_my_ad_breakdown`'s default fields.

## 2. Conversion lag

Platforms credit a conversion to the day of the click or view, not the day of the purchase. Meta's default window is 7-day click, 1-day view; Google credits the click date and its conversions can be fractions (data-driven attribution); TikTok uses its own windows. So the newest days always look worse than they will.

Rules:
- **Zero-result pauses** are judged on the last 7 complete days, never on yesterday alone. An ad that started under 3 days ago (H) is too young to pause for zero results unless it's broken.
- **Yesterday is a trend line, not a verdict.** A bad yesterday alone never triggers a pause or a scale.
- **Long buying cycles** (high price, B2B, lead forms qualified later): stretch the zero-result window to 14 days and leave out the last 3 days (H): `days: 14, exclude_last_days: 3`. Say so.
- When the user's store or CRM shows sales the platform doesn't, ask once which number they trust, and note it in the brand note.

## 3. Is the difference real? (small numbers)

- **Why 3x for a clear pause** (Corey Haines's Meta decision system): if an ad truly converts at the target, the chance it shows zero results after spending k times the target is about e^-k. At 2x that's about 14% (one fair ad in seven paused by mistake); at 3x about 5%. So zero results at 3x T is a clear pause, at 2x a likely one, and below 2x it's noise.
- **Google, by clicks** (Corey's reading-Google-data reference): zero conversions in N clicks only rules out conversion rates above roughly 3/N (95% confidence): 30 clicks rule out 10%+, 100 clicks rule out 3%+. Break-even conversion rate = CPC / T. If the clicks so far can't rule out the break-even rate, it's unmeasured, not failing.
- **Minimum to judge** (H): spend of at least `MIN_SPEND_TO_JUDGE` (default 2x target CPA) for a conversion call; about 1,000 impressions for a CTR call.
- **CPA on few conversions is noise.** With fewer than 5 conversions (H), a CPA can double or halve with one more sale: call it "too few to judge" unless spend with zero results passes the pause line.
- **Ties:** two ads or ad sets within about 20% of each other on few conversions are a tie (H).
- **One day against one day is not a trend** (Corey's operating check). Compare yesterday with the same metric over the trailing 7 days, and with the same weekday the week before. Inside that range is normal variance: hold. Three days moving the same way, or a target line crossed on 7 complete days, is a movement. Never alert on a percentage change with a tiny base (claude-ads): "CPA up 200%" on 2 purchases is nothing.
- **Account-wide drops are not ad problems.** If conversions across the whole account fell by more than half day over day while clicks held (H), suspect tracking, the site, checkout, stock or a price change before any ad. Propose no pauses that day; propose a check instead (`check_my_ad_account` shows the pixel's newest event on Meta and the conversion actions on Google).
- **Account-wide jumps** in purchase value with no matching rise in orders can be double counting: never scale on them before checking.

## 4. Learning phase

- **Meta:** an ad set needs about 50 optimisation events in 7 days to leave learning (Meta's documentation). Results swing while it learns. Significant edits restart it: budget jumps, targeting, creative, bid strategy, pausing for 7+ days.
- **TikTok:** the same idea, about 50 conversions per ad group in 7 days (TikTok's documentation).
- **Google Smart Bidding:** a new bid strategy or a big target or budget change sets off a learning period of about one to two weeks (Google's documentation describes it as up to 50 conversions or about 3 conversion cycles).

**Meta shows it:** `get_my_ads` reads each ad set's learning phase live from Meta (`learning.status`: LEARNING, SUCCESS = exited, FAIL = learning limited, WAIVING; plus `conversions` and `last_significant_edit` when Meta gives them), for up to 20 ad sets a call, active first. `unknown` means Meta didn't say (paused and many older ad sets): treat it as not read, and fall back to the inference below. FAIL (learning limited) is not a reason to pause: it says the ad set gets too few events a week, which a bigger budget, fewer ad sets or a higher-volume event fixes.

**Google shows it:** `get_my_ad_breakdown` with a `google_query` such as `SELECT campaign.id, campaign.name, campaign.primary_status, campaign.primary_status_reasons, campaign.bidding_strategy_system_status FROM campaign WHERE campaign.status = 'ENABLED'` returns LEARNING states (Corey: "bid strategies in learning are unreadable"). `get_my_ads` says so in its `learning_note`. Not tested on a live account yet.

**TikTok (and Meta's unknowns): inferred (H):** an ad group with fewer than 50 results in the last 7 complete days whose ads started under 7 days ago, or that the change log shows was edited in the last 7 days. Say "probably still learning (inferred)". A learning ad set is **kept and watched**, not paused, unless it passes the clear pause line with money at stake. Many small accounts never leave learning at all (on Meta: FAIL): then judge on 7-day totals and say so.

## 5. The decision rules

`T` = target CPA (or, for sales goals, target ROAS: invert the rules). All (H). Judge at the level where the money sits: ads inside a campaign budget (Meta CBO / Advantage campaign budget), ad sets with their own budgets (ABO), Google campaigns (ad groups have no budget).

| Call | Rule | Confidence |
|---|---|---|
| **Fix now** | Rejected or disapproved ad (`review_my_ad` for the reason); spend with zero clicks after about 1,000 impressions (broken link or placement); a campaign spending with no conversion tracked at all | High |
| **Pause, clear** | 7 complete days: spend at least 3x T with zero results; or at least 5 results and CPA at least 2x T. Not probably-learning, or learning with spend already over 3x T | High |
| **Pause, likely** | 7 complete days: spend at least 2x T (`MIN_SPEND_TO_JUDGE`) with zero results and the ad has run 3+ days; or CPA 1.5x to 2x T on 5+ results | Medium |
| **Keep and watch** | In learning; within about 20% of T; fewer than 5 results; under the spend line; one bad day | n/a: one line, no proposal |
| **Scale** | Out of learning (or 7-day results of 10+), CPA at or under T over both the last 3 and the last 7 complete days, no budget change in the last 3 days (change log), frequency not rising fast. Prospecting first: retargeting and brand search are capped by their audience and rarely grow with budget | High when 3-day and 7-day agree and results are 20+; Medium otherwise |
| **Scale, small and cheap** | Probably learning with fewer than 10 results a week, but CPA at or under 0.7x T on 5+ results over 7 days: a cheap ad set with few results is usually held back by its budget, not by its audience. One step only, then judge again after 3 days | Medium |
| **Refresh, not pause** | A "click rate falling" alert (click rate in the last 3 days under 70% of the 7 days before, each window with 1,000+ impressions, 10+ clicks in the earlier one, and spend in the last 3 days), confirmed on link CTR, on an ad that is still under T: hand to the creative-fatigue check | Medium |

Ira Bodnar's stricter line (3x T after 50 conversions) suits big accounts; the lines above are for accounts where 50 conversions a week is rare. For a big account, use his.

**Investigate beats everything** (Corey's operating check): if tracking is in doubt (section 3), no pause or scale is valid that day; the only proposal is the check.

**Cost caps and bid caps flatter CPA** (Motion's performance-analysis): an ad set on a cost cap or bid cap (`bid strategy` in `get_my_ads`) holds its CPA by holding back delivery. A bigger budget often doesn't spend there; say so instead of proposing a raise, or propose loosening the cap as a separate decision.

**Frequency bands** (H, Corey's Meta decision system): prospecting safe up to about 2.5, warning 2.5 to 4, critical above 4; retargeting safe up to about 4, warning 4 to 6. A scale candidate in warning gets "scale, watch frequency"; in critical it's a refresh, not a scale.

**Pausing a loser leaves a gap** (Corey: "never pause without a replacement" for winners). Pausing the last active ad in an ad set stops the ad set: say so. When a pause leaves an ad set or campaign with no live ad, the next moves offer the replacement.

**Adgence's own alerts are hints, not verdicts, and now say so.** "Click rate well above your account's" means a new ad (under 14 days old, or new since the last sync) with at least 2x the account's click rate on 2,000+ impressions and 30+ clicks, and at least a quarter of the average ad's spend; its text ends "Based on clicks only: check its cost per result before giving it more budget." It says nothing about cost per result. "Click rate falling" is the same kind of signal. Check CPA (and on Meta, link CTR) before proposing anything on either.

## 6. Budget steps and spend caps

- **Step size:** at most `MAX_BUDGET_STEP` (default +20%) per change, at most once every 2 to 3 days per ad set or campaign (H; Meta and Google both warn that big jumps reset learning or Smart Bidding). `change_my_ads` now warns in its preview on any raise over 20% in one step (and when it can't read the current budget), which matches this rule; the skill still sizes every raise itself (new budget = current x 1.2, rounded down to a whole currency unit) and treats a preview warning on its own proposal as a sign the budget moved since the read.
- **Marginal versus average CPA:** an ad set's average CPA includes its cheapest results. The next dollars buy the next-cheapest people, so a raise usually costs more per result than the average suggests (this is also Meta's breakdown effect: budget flows to where the next result is cheapest, not the lowest average). That's why a raise is one step, then judged again on the days after it, not on the old average. A scaled ad set whose CPA since the raise passes T goes back down a step.
- **Bigger jumps:** duplicate the winner into a new ad set (`duplicate_my_ad`, created paused) instead of one large raise. Propose it, don't do it.
- **Daily cap on new spend** (H, from Tobi Adonadon's "daily cap"): the sum of today's proposed budget increases and resumes stays within `MAX_BUDGET_STEP` of yesterday's whole-account spend. Pauses don't earn room for more raises unless the user says to move the money.
- **Moving money:** in ABO, pausing an ad set does not move its budget anywhere; account spend just drops. In CBO, pausing an ad sends its share to the other ads in that campaign. Say which, and show the **net change to daily spend** for the whole batch.
- **The account's own cap:** the prompt is not a safety limit. On Meta, `check_my_ad_account` shows whether an account spending limit is set: if none, say so once and suggest setting one in Ads Manager (Billing, Account spending limit). On Google and TikTok, point to the campaign or account budget settings. The skill never sets it.
- **Resumes spend money.** A resume is a spending change: say the amount and currency.
- **Google:** budgets are the campaign's daily budget; a shared budget is refused by the tool (change it in Google Ads).

## 7. Approval fatigue: batching the proposals

Meta's own ads MCP users report "approval fatigue" when an agent stages dozens of changes. A daily watch that asks for ten yeses a morning gets ignored, then switched off.

- **Rank by money at stake:** for pauses, the spend that would have bought results at T but didn't; for scales, the extra daily budget. Show the top ones only: **Founder 3, Buyer 5, Agency 5 per account** (H).
- **Group alike moves into one decision:** "Pause the 4 ads in Broad with $0 results ($212 spent in 7 days)" is one proposal with one yes, listed ad by ad underneath.
- **Everything else is one line** under "Watching, no action".
- **"No change today" is a full answer.** It's the most common good morning.
- **Don't nag:** a proposal the user declined isn't proposed again for 3 days (H) unless its spend at stake doubled. The change log holds what was declined.
- **One yes, then one confirm:** on "yes to 1 and 3", preview every change (`change_my_ads` without `confirm`), show all previews together, and act on one "confirm". If a preview differs from the proposal (budget changed, ad already paused), drop that one and say why.

## 7b. Every proposal is a draft with an undo

From Corey's operating check and claude-ads: each proposal carries current state, the change, the expected effect, and the undo trigger. For a raise: "step back down if the CPA on the days since passes T over 3 complete days". For a pause: "resume if [reason] proves wrong" only when there's a reason to expect that (a tracking fix, a stock return). A change runs only if the preview still matches the proposal (claude-ads: "remote state still matches the draft precondition"). Pause, never delete: the tools can't delete campaigns, and the skill never suggests it.

## 8. The change log

Kept on a board named "Daily watch" with one file per account (`save_to_board`, a note, once per run): date, proposals with their numbers, what the user approved, declined or left, and what each `change_my_ads` call answered. The next morning reads it first (`get_board`): it's how the watch knows a budget was raised 2 days ago, that a pause was declined yesterday, and what each change did. A scheduled run that proposed and stopped logs "proposed, not acted on".

## 9. Reading the result of yesterday's changes

For every change logged in the last 7 days, one line: what was done, and the numbers since (spend, results, CPA). A paused ad set's budget doesn't come back by itself; a raised one needs 2 to 3 days before it's judged again.

## 10. The insight quality bar

Every proposal carries the data row behind it (spend, results, CPA or ROAS, window, level, budget), its rule, its confidence and its risk. Cut: metrics without a decision, "CTR is down" with no action, advice that fits any account, more than the batch limit. If nothing passes the rules, say "no change today" and show what's being watched.
