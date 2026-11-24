# The weekly digest method

How a senior creative strategist reads a week of competitor ad moves. Sources: Adgence's competitor-analysis notes (weekly tracking), Motion's competitor-watch skill (baseline, weekly cohorts, signal strength, "inactive does not mean failed"), Octolens's weekly competitor insights (one-line takeaway, quiet week in one line, under 500 words, openings for us), boring-marketer's competitive-intel WATCH mode (zero questions, deltas only, "no changes" is a complete report), Corey Haines's reverse-engineer-marketing Watch Mode (observed, inferred, not found; launch radar; timing is a lead, not a verdict), the code of Adgence's tracking tools, and real runs on 23 Nov 2026 (Crave Creatine, Arrae, Bloom Nutrition), before and after that day's tracking update. Thresholds marked (H) are heuristics: use them, and say they are.

## 1. The core idea: the value is in the change

"Arrae launched 11 Black Friday ads on 19 and 20 Nov" is intelligence. "Arrae has 990 live ads" is a fact sheet. Every line of the digest answers "what should I pay attention to this week, and what do I do about it?" A quiet week gets one line. Never pad.

## 2. What the data really is (checked in the code, 23 Nov 2026)

- **A tracked check** (`track` kind `ads`) reads the competitor's Meta page **in the item's own country** (`country`, default `ALL` = every country) and saves the ads it read, tagged with the tracking id. Track a one-market brand's competitors in that market: a worldwide read of a 1,128-ad account once held none of the 9 US Black Friday ads a US read held (observed 23 Nov). Items tracked before the country input existed are `ALL`; `change_tracking` can't change a country, so re-track them (stop, then `track` with `country`).
- **A check is a read on purpose, still a sample for big accounts.** Each check reads the page's most-seen ads, its newest, and its 90-day runners (each ad tagged `read_as`), plus Meta's exact counts of all live ads and of those past 30 and 90 days. Observed 23 Nov (US): Arrae 139 ads read of 991 live (45 past 90 days, all 45 in the read), Crave 99 of 719. The newest slice is what sees launches: Crave's read on the older tools leaned old and showed 0 launches; the same week read this way showed 30 (14 on 16 Nov, 9 on 18 Nov, 7 on 20 Nov).
- **`whats_new` compares each item's own checks**, never a one-off `find_competitor_ads`, `compare_competitor_ads` or `get_brand_ad_profile` read of the page, nor a read in another country. Without `since` it compares the latest check with the previous one, so weekly checks meet last week's whatever the weekday; with `since` (YYYY-MM-DD) it compares with the check at or before that day. Until a second check exists it says "only one check so far" and gives the counts.
- **What it reports for ads:** `live_ads_now` / `live_ads_then` and `live_ads_change` are **Meta's own counts** (exact), `ads_read` the read's size; `running_30_days_plus` / `running_90_days_plus` (then and now) are Meta's counts too. `new_ads` are launches: ads not in the previous check **whose start date is on or after the previous check's day**; older ones that just weren't sampled last time are counted apart in `not_launches`. `stopped_ads` are told **only when both checks read (nearly) the whole page** (95% of the live count or more); otherwise `stopped_note` says so and `live_ads_change` is the exact net change. `crossed_30_days` / `crossed_90_days` are ads that passed the mark between the checks. Lists are compact by default (5 each, with a `_count`); `detail: "full"` gives up to 20 with copy.
- **Legacy checks** (made before 23 Nov's tool update) stored no Meta count: on them `live_ads_now` falls back to the read size (120 for Arrae and Crave). If `live_ads_now` equals `ads_read` on a big page, it's the read size, not the account: take the account size from `compare_competitor_ads`.
- **Tracking by name picks the page running ads.** `track` with a name or website keeps only pages whose name or Instagram matches, then takes the one with live ads in that country (the verified one, or one with 3x the next one's ads); otherwise nothing is tracked and the candidates come back. Tested 23 Nov: "Ritual" chose the vitamin brand's verified page (@ritual) though `find_advertiser` lists eight "Rituals Cosmetics" pages first. A page tracked by id shows its name after its first check, or at once with `name`.
- **`whats_new` and alerts:** without `tracking_id` it lists every tracked item in the workspace (other people's too) and the user's unread alerts, and marks them read unless `mark_read: false`. With a `tracking_id` it shows no alerts and marks none unless `mark_read: true`.
- **`run_now`** (`change_tracking`) runs one check at once at the normal cost (1 per ads item), once per item per 10 minutes, and leaves the schedule as it is. The next weekly comparison then spans from that check, not from last week's.
- **Stores:** `whats_new` compares the newest two store reads since tracking began, and a one-off `get_store` read of the same store counts as one (observed: gymshark.com compared a tracked check with a manual read 10 minutes later). Check `compared` before calling anything a week's change.
- **Spend, results and targeting are never visible: MISSING.** Run time means they keep paying, not that it's profitable.

## 3. What one snapshot can say, and what needs history

| Signal | From one snapshot (week one) | Needs two checks a week apart |
|---|---|---|
| Launches this week | Yes: ads with a start date in the last 7 days (of the ads read) | |
| Crossed 30 / 90 days this week | Yes: ads live 30 to 36 or 90 to 96 days | |
| Same-day batches (tests started) | Yes: 3+ ads with one start date | Which variants survived |
| Offers and timing (a sale started) | Yes, from copy and headlines | When it ended |
| Account size, ads past 30 and 90 days | Meta's counts (`brand_read`) | Their weekly change (exact) |
| Stopped ads, kill rate, survivors | No | Only where the tool tells stops (whole reads); otherwise the net change in Meta's count |

Week one is never empty: report launches, crossers, batches and offers from start dates, and say plainly that stopped ads and survivors start next week.

## 4. Read every comparison before reporting it

The tool now does most of the validation (own checks only, launches by start date, stops only on whole reads). What's left is judgement:

1. **A week, not minutes.** `compared.days` should be 6 to 8. Shorter means a `run_now` or a changed frequency in between: report the span ("in the 2 days since Thursday's check") or fall back to start dates. "Only one check so far" means week one: use the snapshot (section 3).
2. **Launches are launches** (`new_ads`, by start date). Count them with their base: "30 launched since 16 Nov, in a read of 99 of 719 live". The read is a sample, so the count is a floor. `not_launches` is sampling: never report it.
3. **Stopped: what the tool says, nothing more.** On a big page there's no `stopped_ads`, only `stopped_note` and `live_ads_change`. Report the net change ("Meta's count fell by 120, from 991 to 871") and never name an ad as killed. When `live_ads_change` is a big drop and last week's batch or sale ads are missing from a read that has a `newest` slice, say "likely ended" (Medium). On a small page where stops are told, check why before calling a loss (section 6, "inactive does not mean failed").
4. **Crossers are reliable** (days live is absolute): an ad crossing 30 or 90 days was live at the check. Check it isn't catalog. Meta's `running_30_days_plus` and `running_90_days_plus` then and now show whether their survivors grew.
5. **Mind the legacy end.** If `live_ads_then` equals the read size on a big page (a check from before the update), don't report `live_ads_change`: use the baseline note's Meta count instead.

## 5. Each competitor's status (from Motion, thresholds (H))

| Status | Rule (H) |
|---|---|
| **Quiet** | 0 to 1 launches and no new 30-day crosser of a new concept |
| **Active** | 2+ launches in line with their usual pace (launches per week over the last 4 weeks, from start dates) |
| **Launching** | A same-day batch of 5+, or launches at 2x their usual pace, or a new product in the ads |
| **Shifting** | A new angle, persona, offer or landing page across 3+ new ads; a new concept crossing 30 days; or a 90-day runner stopping on a complete read |

A brand can be launching and shifting. On a sample, pace is a floor: never say "slowed down" from a sample. A read with no `newest` slice can't see launches at all (Crave's older read showed none in a week it launched 30): say "no clean read" rather than "quiet".

## 6. Signal strength: what goes first (Motion's ladder, adapted)

| Signal | Strength | Why |
|---|---|---|
| A new concept crossing 30 days, or a batch crossing 90 still live | Very high | Survived several weeks of their money |
| 3+ new ads on one new theme, offer or persona | High | A coordinated bet: name it |
| A sale or offer start (or end) | High for timing | Changes what the user should launch this week |
| A proven winner relaunched with a new overlay or offer | High | They trust that creative most |
| A new product or landing page in the ads | Medium to high | Where their money is heading |
| Volume change in Meta's live count (10%+ week on week (H)) | Medium | Budget or season |
| One odd ad | Low | Note only if it repeats |
| Copy variations on old themes | Low | Skip |

**Inactive does not mean failed** (Motion): an ad stops because it lost, because a promo ended, because a new iteration replaced it, or because budget moved. Check for dates or sale words in the copy and for a newer sibling before calling a loss. Quick stops (under 5 days, no promo) lean to "lost" (H).

**Timing is a lead, not a verdict** (Corey): a launch before a traffic jump is a candidate cause, never proof.

**Launch radar** (Corey): when 2+ of these land in the same few weeks, flag a likely launch: a new product page in their ads, a new store product (`whats_new` on a tracked store), a batch of ads to one new landing page, a new TikTok Shop product.

## 7. From change to decision

| Decision | When |
|---|---|
| **Time it** | They started a sale or big launch: don't put a full-price cold test into their discount week, or answer with your own offer on your best long-runner |
| **Borrow the structure** | A new 30 or 90-day crosser's format or mechanic fits the user's product |
| **Counter** | They moved onto the user's angle or persona: sharpen the user's difference, don't copy |
| **Watch** | A new concept that may matter: break it down now (1 credit) or wait a week to see if it survives (free) |
| **Ignore** | Catalog ads, copies of old themes, one-off tests |

Each opening for the user carries its evidence (ad link, days live, count with base) and a success measure: purchases or CPA for a new concept, hook rate for a new hook.

## 8. Week-over-week from the baseline note

The tools keep pulls, not the digest's view. The skill keeps its own baseline (Motion's practice), as a note on the board "Competitor watch": for each competitor the page id and name, the country, Meta's live count, ads read, the launch dates and ids of this week's launches, the 30 and 90-day crossers, open sales, and the status. Next week, compare Meta's count and the open items against it: did the sale end, did last week's batch reach 14 days, did the crossers keep running.

## 9. Monthly extras (first digest of the month)

- `get_website_traffic` (1 per site, up to 5): visits and paid-social share for the user and competitors; line the change up with the month's launches (a lead, not a verdict).
- `get_brand_ad_profile` per competitor (the user's country; free if pulled in the last 24 h): weekly launch pace for 12 weeks and test groups. Its survivor verdicts count only when its checks span a week or more.

## 10. The quality bar

Every finding is evidenced (link, days live, base), non-obvious, and tied to a decision. Cut: lists of ads with no "so what", account sizes with no change, "they use UGC", run time called profit, findings from a mixed comparison, and anything that would read the same any week. Under 500 words for the digest itself; the evidence goes last.
