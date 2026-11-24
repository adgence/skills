# Worked example: illustrative example account

> **Illustrative example account, not a real advertiser.** "Example Supplements Co." is Adgence's demo account (the files `apps/web/public/demo/myads_results.json`, `myads_google_results.json`, `myads_search_terms.json`, `myads_pause.json` and `myads_google_budget.json` on the Adgence site). Its numbers are made up for the demo and are used here only to show the structure, the arithmetic and the decision rules of this skill. **Never reuse these numbers, names or conclusions for a real run.** The competitor line at the end is real Adgence data from 23 Nov 2026 and is marked as such.
>
> **What the demo can't show.** The demo holds one 30-day period (24 Oct to 22 Nov 2026) per account, at campaign and ad set level only: no previous period, no ad-level rows, no daily series, no breakdowns, no target and no change log. So this example compares ad sets and campaigns **within** the period with the same noise rule the skill uses week over week, and marks every week-over-week field MISSING. A real Monday report fills those from `get_my_ads` with the compare flag (each node's `previousMetrics`) for the last complete Monday to Sunday. No real ad account was connected when this skill was built: a real-account test is still needed.

The answer as the skill writes it (Buyer mode, Quick depth: nothing watched, 0 credits on the account; the competitor line cost 1 credit in the real test).

---

**Example Supplements Co. · Meta and Google Ads · USD · 24 Oct to 22 Nov 2026 (demo period) · no target given: judged against the account's own averages**

## Verdict

**Call: FIX.** Week type: not readable (no previous period in the demo). Two lines are burning money at 3 to 6 times the account's cost per sale, and both problems sit after the click, not in the ads' pull: the Meta "Founder video test" ad set (CTR within 10% of Broad, conversion rate 63% lower) and the Google "Competitor names" campaign. Together they cost about $1,490 more over the period than the account's own efficiency would have. This week: pause or fix those two (proposals 1 and 2), and move the money to the lookalike and exact-match lines that beat the account.

## Scoreboard

Platform-reported conversions under each platform's own attribution; not added together as sales. Week over week: MISSING (demo has one period).

| Line | Spend | Conversions | CPA | ROAS | vs last week |
|---|---|---|---|---|---|
| **Meta, all** | $6,460 | 273 purchases | $23.66 | 2.31 | MISSING |
| Meta prospecting (3 ad sets) | $4,820 | 176 | $27.39 | 2.01 | MISSING |
| Meta retargeting (30-day visitors) | $1,640 | 97 | $16.91 | 3.20 | MISSING |
| **Google, all** | $3,520 | 226 | $15.58 | 3.53 | MISSING |
| Google non-brand (2 campaigns) | $3,210 | 130 | $24.69 | 2.23 | MISSING |
| Google brand | $310 | 96 | $3.23 | 17.03 | MISSING |

- Google's blended ROAS of 3.53 is flattered by brand search (17.03 on $310): judge Google on **non-brand, 2.23**, which is close to Meta prospecting's 2.01. Brand's cheap sales are people who already knew the brand, not a media win.
- Meta and Google together claim 499 conversions; the store's own order count for the period is MISSING, so blended MER (store revenue / ad spend) can't be given. Ask for it.

## Three changes that matter (ranked by money)

Noise rule (method §4): noise = √(1/n₁ + 1/n₂) on the two conversion counts; Real when the gap is more than twice the noise.

**1. Meta "Founder video test" costs 3.1× Broad per purchase: Real, High confidence. FIX after the click; don't judge the creative yet.**
- Row: $1,360 spend (21.1% of Meta), 18 purchases, CPA $75.56, ROAS 0.58, against Broad · Women 25-44 at $2,940, 121 purchases, CPA $24.30, ROAS 2.26.
- Is it real: +211% against a noise band of ±51% (18 and 121 purchases). Real.
- Money: $922.64 more than the same spend would have cost at Broad's CPA.
- Funnel step: CPM +3.7% ($9.58 vs $9.24), CTR −9.9% (1.36% vs 1.51%), **conversion rate −62.9% (0.93% vs 2.52%)**, order value −20% ($43.89 vs $55.00). The 3.1× CPA is almost all conversion rate: the founder video gets the click nearly as well as Broad's ads, then people don't buy.
- Facts: the numbers above. Hypotheses: a landing page or offer that doesn't match the founder story, a different audience in this ad set, or a smaller-pack default (lower order value). Missing: the landing URL of these ads, the audience settings, ad-level rows, last week.

**2. Google "Search · Competitor names" costs 5.7× the main search campaign per conversion: Real, High. FIX or pause.**
- Row: $690, 6 conversions, CPA $115.00, ROAS 0.48, against Search · Creatine gummies at $2,520, 124 conversions, CPA $20.32, ROAS 2.71.
- Is it real: +466% against a band of ±84% (6 and 124 conversions). Real.
- Money: $568.06 more than at the main campaign's CPA.
- Funnel step: CTR 3.58% vs 5.13% and conversion rate 1.13% vs 4.61%: people searching a rival's name click less and buy much less. Common for competitor-name campaigns; worth keeping only with a page built to win that comparison.

**3. Google ad group "Gummies vs powder" costs 2.8× the exact-match group: Real, High. TEST a comparison page.**
- Row: $420, 9 conversions, CPA $46.67, ROAS 1.18, against Creatine gummies · exact at $1,240, 74 conversions, CPA $16.76, ROAS 3.28.
- Is it real: +179% against ±71% (9 and 74). Real. Money: $269.19 more than at exact match's CPA.
- CTR 3.39% vs 6.36%, conversion rate 2.20% vs 5.44%: a question-stage search ("vs powder") landing on a buy page.

**Also, a winner, Real but only just:** Meta "Lookalike 2% buyers" at CPA $14.05 (37 purchases, ROAS 4.30) against Broad's $24.30: −42% against a band of ±38%. It saved $379.01 against Broad's CPA on 8% of Meta spend. A scale candidate once it holds a second week (Medium confidence).

## Best and worst ads

Not watched (Quick depth), and the demo has no ad-level rows. In a real Standard run: the best and the worst ad by CPA among ads with enough spend, watched with `watch_my_ad` (1 credit each), compared on hook, pacing and offer.

## Breakdown surprise

MISSING: the demo has no breakdown rows. A real run reads placement and country with `get_my_ad_breakdown` and reports one or two segments that pass method §6.

## Google search terms (the period)

From the demo's search terms list: four terms with clicks and no conversions cost $190.90 together. At the non-brand conversion rate of 4.04%:
- "creatine gummies side effects": 74 clicks, $86.30, would expect 2.99 conversions: right at the rule's line of 3, so weak evidence by numbers alone.
- "are creatine gummies a scam": 37 clicks, $44.10 (expected 1.49): not proven by numbers.
- "creatine gummies free sample": 41 clicks, $38.90; "creatine gummies recipe": 29 clicks, $21.70: wrong intent, negatives regardless of numbers.
- The so-what: "side effects" and "scam" are objections, not junk. They're buyers asking for reassurance. Answer them on the landing page and in an ad before deciding to block them.

## Alerts

None in the demo. A real run lists every alert `whats_new` returns (ad rejected, "click rate falling", "click rate well above your account's") and re-judges each on CPA.

## The market (real data, 23 Nov 2026)

Crave Creatine (Meta page 705334959318799) is tracked weekly in this workspace. `whats_new` on it (re-read 23 Nov, 19:50 UTC, after the fixes): "only one check so far: changes show from the next one" (checked 14:42 UTC, 120 ads read; Meta's live count not recorded on that older check). **Reported as: first check; launches and stops show from next Monday's check.** A live read the same evening (US, 1 credit) gave Meta's own counts: 719 live ads, 541 running 30+ days, 245 running 90+ days. Its newest ads started 18 to 21 Nov: at least 10 launches in the reported week (2 images, 8 videos, all on the same "Creatine is a natural fuel your body already produces" copy, landing on the gummies page), so Crave is refreshing creative on its proven message rather than testing new angles. Low confidence as a cause of any CPM move.

(Before the 23 Nov fix, `whats_new` compared two reads of this page taken 7 minutes apart and listed 14 "new" and 16 "stopped" ads, ads live 16 to 502 days: sampling noise, which the tool no longer reports.)

## What we did

Not given: no change log was supplied.

## Proposals (for approval; this report changes nothing)

1. **Pause the Meta ad set "Founder video test"** ($45 a day in the demo's preview), or keep it and send its traffic to a page that matches the founder story. Row: $1,360, 18 purchases, CPA $75.56.
2. **Pause Google "Search · Competitor names"** and move its $25 a day to "Search · Creatine gummies" ($60 to $85 a day, a 42% step: the demo's preview; the skill would suggest splitting it into two steps of about 20% to keep Smart Bidding stable). Row: $690, 6 conversions, CPA $115.00.
3. **Add negatives** "free sample" and "recipe" to the search campaign.

Each runs only after a preview and the user's yes to that change.

## Next week's tests

1. **Founder video, new landing page.** Hypothesis: the founder video sells the click but the page doesn't close. Send the same ads to a page that opens with the founder's story and the full-size pack. Budget: the ad set's current daily budget. Call it after it spends 1 to 2× the account CPA per version or after 7 days; win = conversion rate within the noise band of Broad's 2.52%.
2. **"Gummies vs powder" comparison page** for the "vs powder" ad group. Win = CPA within the noise band of the exact-match group.
3. **An objection ad** answering "side effects" and "is it a scam" with the brand's own proof. Win = purchases at or under the prospecting CPA.

## Data gaps

Week over week, ad-level rows, breakdowns, daily series (lag check), the target or margin, the change log, store revenue (MER), and the landing URLs: all MISSING in the demo. The competitor tracking has one check. Credits for the competitor line: 1.

**One question:** what's your target cost per purchase, or your gross margin? With a 40% margin, break-even ROAS is 2.5, and then Meta prospecting at 2.01 and Google non-brand at 2.23 are both under water, which changes every call above.
