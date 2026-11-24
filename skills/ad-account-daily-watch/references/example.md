# Example: one morning's daily watch (ILLUSTRATIVE, demo account)

**This is not a real account and not a real run.** No ad account was connected when this skill was built. The numbers come from Adgence's demo example account "Example Supplements Co." (`apps/web/public/demo/myads_results.json`, `myads_google_results.json`, `myads_pause.json`, `myads_google_budget.json`), which is illustrative. The target CPA of $30 is an assumed input. The demo holds 30-day totals (24 Oct to 22 Nov 2026) only, so the 3-day and 7-day checks a real run makes couldn't be made here; where that changes a call, it says so. Use this for the **shape** of the answer, never its numbers. A real-account test is still needed.

Mode: Buyer. Accounts: Meta and Google Ads, USD. Target: $30 per purchase (assumed).

---

*Mon 23 Nov 2026. Example Supplements Co., Meta and Google Ads, USD. Window: the demo's 30 days, 24 Oct to 22 Nov (a real run reads the last 7 complete days, 16 to 22 Nov, with `days: 7`, never today, and puts `numbers_as_of` here).*

**Today in one line:** 4 moves to approve. If you approve all 4, your daily spend goes down by $58, plus 20% of Lookalike 2%'s budget.

## Proposals

| # | Move | Data (30 days) | Rule | Confidence | If approved |
|---|---|---|---|---|---|
| 1 | **Pause ad set "Founder video test"** (Meta, Prospecting · Creatine gummies · US) | $1,360 spent, 18 purchases, CPA $75.56 (2.5x target), ROAS 0.58 | Pause, clear: 5+ results and CPA at least 2x target | High | Stops every ad in it. Its $45 a day is not moved anywhere (the budget sits on the ad set), so account spend drops $45 a day |
| 2 | **Pause campaign "Search · Competitor names"** (Google) | $690 spent, 6 conversions, CPA $115 (3.8x target), ROAS 0.48 | Pause, clear | High | Stops it. Spend drops $25 a day |
| 3 | **Raise "Search · Creatine gummies" from $60 to $72 a day** (Google, +20%) | $2,520 spent, 124 conversions, CPA $20.32 (0.68x target), ROAS 2.71 | Scale | Medium here (3-day vs 7-day not checkable in the demo; High on a real run if both agree) | Spends up to $12 more a day. Judge again in 3 days on the days after the raise |
| 4 | **Raise ad set "Lookalike 2% buyers" by 20%** (Meta) | $520 spent, 37 purchases, CPA $14.05 (0.47x target), ROAS 4.30 | Scale, small and cheap: few results a week, but the cheapest in the account (a real run shows Meta's `learning.status` here; the demo has none) | Medium | Spends 20% more a day on this ad set. Its current budget isn't in the demo (MISSING): the preview shows the exact amount |

**Undo triggers:** #3 goes back to $60 if its cost per conversion on the days after the raise passes $30 over 3 complete days; #4 steps back down on the same test. #1 and #2 have no planned undo: they come back only as new tests.

**Why $72 and not $85 on #3:** the demo's own budget preview moves this campaign from $60 to $85 (+42%) in one step, funded by pausing #2. A jump that size can push Smart Bidding back into learning. One 20% step now, another in 3 days if the cost per conversion holds. Pausing #2 lowers spend; it doesn't need to be spent elsewhere the same day.

## Fix now

- None in the demo. A real run also checks: rejected ads (`review_my_ad`), the pixel's newest event, and whether the Meta account has a spending limit (`check_my_ad_account`).

## Watching, no action

- **Broad · Women 25-44** (Meta): CPA $24.30 on 121 purchases, ROAS 2.26. Passes the scale rule. Held one day so only one Meta prospecting ad set changes at a time and today's raises stay inside the daily cap (about $66: 20% of the demo's average $333 a day across both accounts).
- **Retargeting · 30-day visitors** (Meta): CPA $16.91, ROAS 3.20. Good, but capped by how many people visit: more budget mostly buys the same people more often.
- **Ad group "Gummies vs powder"** (Google, inside #3): CPA $46.67 on 9 conversions (1.6x target). In the "pause, likely" range, but on Google the cheaper fix comes first: the search terms behind it.
- **Brand · Example Supplements** (Google): CPA $3.23, ROAS 17.03. Brand search catches people already looking for you; a bigger budget rarely adds sales. Leave it.
- **Sour gummies · hook test** (Meta): paused, $0.

## Since your last changes

- First run: no change log yet.

Reply **"yes to 1 and 3"** (or "all", or "none").

---

## After "yes to all": the previews, in one message

Each move is previewed with `change_my_ads` without `confirm`. The demo's pause preview for #1 looks like this (fields from `myads_pause.json`, illustrative): current ad set "Founder video test", status ACTIVE, daily budget $45, $1,360 in 30 days, 18 purchases, ROAS 0.58; change: status PAUSED, "Stops every ad in this ad set." The real tool returns `current` (name, status, budget, budget level), `change` in words, and a `warning` on any budget raise over 20% in one step. None of these four moves triggers it; the demo's own $60 to $85 preview would ("That raises the budget 42% (1.4×) in one step...").

> 4 changes ready. Net change to daily spend: -$58, plus 20% of Lookalike 2%'s budget (shown in its preview). Reply "confirm" to apply all 4.

On "confirm": each change is called again with `confirm: true`, and the reply lists what each tool answered. Nothing else is touched.

## The log note (saved once, board "Daily watch", file "Example Supplements Co.")

> 23 Nov 2026, window 24 Oct to 22 Nov (demo). Proposed: 1 pause Founder video test (CPA $75.56), 2 pause Search · Competitor names (CPA $115), 3 Search · Creatine gummies $60 to $72, 4 Lookalike 2% +20%. User: [answer]. Tool answers: [per change]. Held: Broad (scale next), Gummies vs powder (search terms first).

## Next moves

- **Search terms behind "Gummies vs powder":** which searches cost money without converting, and which deserve their own keyword. Free.
- **Replace the founder video:** watch it (1 credit) to see where it loses people, then brief a new version or borrow a proven structure from a competitor. 1 credit, then free.
- **Make it daily:** a recurring task each morning that proposes and never acts on its own. Free.
