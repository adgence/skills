# The fatigue method

How a senior media buyer and creative strategist decides which ads are really tiring, why, and what replaces them. Sources: Adgence's my-ads and creative-strategy notes; Meta's documentation on frequency, the learning phase and the breakdown effect; Corey Haines's `ads` skill (its Meta decision system: frequency bands, swap rules, iteration order, "never pause without a replacement"); Motion's `find-iterations` and `performance-analysis` skills (fatigue causes, iteration cards, hook rate and hold rate); claude-ads `ads-creative` ("fatigue only from time-series delivery and response evidence, not age alone"); irinabuht12 `creative-fatigue-detection` (urgent, warning, healthy); practitioner write-ups (Whathead: "compare each ad with its own history"; Ryze: 7-day versus 30-day CTR). Thresholds marked (H) are heuristics: use them, say they are, and prefer the account's own history over any number here.

## 1. What fatigue is, and what it isn't

**Fatigue** is one ad losing its pull on the people it keeps reaching: the same people see it more often (frequency up), fewer of them act on it (CTR down), and each result costs more (CPA up), while the ad's own placement mix, audience and the market stay roughly the same.

Five things look like fatigue and aren't. The skill rules each one out before it calls an ad tired:

| Looks like fatigue | What it really is | How to tell | What to do instead |
|---|---|---|---|
| **Placement shift** | Meta moved the ad's delivery toward placements with lower CTR (Reels, Stories, Audience Network) | The ad's CTR **within each placement** holds; only the mix moved (§4) | Nothing, or a format cut for the placement now taking the spend. Never pause on it |
| **Seasonality or auction pressure** | Everyone's costs rose (Black Friday to Christmas, a sale week, a big launch in the niche) | The whole account's CPM rose by about as much, and the ad's CTR fell no more than the account's (§5) | Hold. Judge again after the peak. Refresh only ads that fell **more** than the account |
| **Audience saturation** | The ad set has run out of new people, so every ad in it is tiring together | Frequency rises across all ads in the ad set, reach flattens week on week, sibling ads decline together (§6) | A new audience or a broader ad set, not only a new ad. A new ad into the same pool tires fast too |
| **A loser, not a tired winner** | The ad never worked | It was never above the account's CTR or under its CPA, even in week 1 | Pause (with approval). There's nothing to refresh |
| **Too little data** | Noise | Under the data gates in §3 | Wait. Say when it will be readable |

**Age alone is never fatigue.** A 200-day ad with steady CTR, frequency and CPA is a durable winner, not a tired one. A 10-day ad can be fatigued in a small audience.

## 2. What the data can and can't say

The user's own account gives what competitors' ads never do: spend, impressions, reach, frequency, clicks, CTR, CPM, conversions and CPA per day or week. It still has limits:

- **`get_my_ads` with `daily: "ad"`** adds a per-day series per ad. Its exact fields come from the platform connection and haven't been seen on a real account yet: use frequency or reach from it **when present**; otherwise take frequency from `get_my_ad_breakdown` weekly rows. `compare_previous: true` adds the previous period of the same length.
- **Daily frequency is not the frequency practitioners mean.** Frequency for one day is impressions ÷ people reached that day, usually 1.0 to 1.3. The bands below (2.5, 4) are **7-day** frequency. Reach can't be added up across days (the same person counts again), so 7-day frequency comes from **weekly rows**, never from summing daily ones.
  - **Meta:** `get_my_ad_breakdown` with `level: "ad"`, `time_increment: "7"`, `ids` (up to 50): each row has `reach` and `frequency` for that week.
  - **TikTok:** `time_increment: "7"` returns `weeks`: one TikTok report per 7-day window from the range's first day (up to 26 weeks), so reach and frequency are TikTok's own figures per week. Frequency = impressions ÷ reach. A last window shorter than 7 days is named in `note`: leave it out of the trend.
  - **Google:** search ads have no frequency. This skill is for Meta and TikTok; for Google search, fatigue is the wrong lens (the search-terms skill applies).
- **Meta's `ctr` counts all clicks** (likes, profile taps, "see more"). For fatigue, link CTR is the cleaner read: `inline_link_clicks` and `inline_link_click_ctr` are now in the default fields. Say which CTR you used.
- **Ranges end yesterday by default** (today is a partial day and is left out; `days: 28` is the 28 full days to yesterday). `numbers_as_of` says when the platform was last read. Conversions arrive late (Meta reports purchases up to 7 days after the click with its default 7-day click window), so the last 3 days understate conversions and overstate CPA: judge CPA on windows that end 3 days ago (`exclude_last_days: 3`), or say the last days are incomplete.
- **Meta weekly rows start on the range's first day**, so pick a range that is a whole number of weeks (28 days ending yesterday) or the last row is a partial week.
- **Adgence's own "click rate falling" alert** (`whats_new`, kind `myads.tiring`) fires when an ad's click rate over its last 3 days is under 70% of the 7 days before, each window with at least 1,000 impressions, at least 10 clicks in the earlier window, and spend in the last 3 days. Its text says what it's based on and that it's "a signal from clicks only, not results". It reads click rate only (the platform's clicks, not link clicks), from a short window, with no frequency, placement or seasonality check. Treat it as "check this ad", never as the verdict. `whats_new` marks alerts read once shown (unless `mark_read: false`).
- **Spend, budget and audience changes the user made** also move these numbers. Ask, or say "if nothing was changed on [date]".
- **What it can't see:** profit (margins, returns), the store's own sales, and what people think. MISSING unless the user says.

## 3. Data gates: is this ad readable yet?

Judge an ad only when all hold (H):

| Gate | Rule | Why |
|---|---|---|
| Live long enough | Out of learning: at least 7 days live and, where conversions are the goal, about 50 optimisation events in a week on the ad set (Meta's documented learning bar). On Meta, read it: the ad set's `learning.status` in `get_my_ads` (LEARNING fails the gate; SUCCESS passes; FAIL = learning limited, judge on the trend and say so; unknown: fall back to the age and events rule). TikTok: infer it | Learning-phase numbers swing on their own |
| Enough impressions | At least 1,000 impressions in each window compared, ideally 3,000+ | CTR differences on fewer are noise |
| Enough conversions for CPA | Spend of at least 1 to 2 times the target CPA in each window, or 10+ conversions | Otherwise judge on CTR and CPM only, and say so |
| Real baseline | A baseline window of 7+ days from the ad's own good period (its first 2 to 3 weeks after learning, or its best 7-day stretch) | Fatigue is a fall from the ad's own level |
| Still spending | Spent in the recent window | A paused ad isn't tiring |

Ads under the gates go to "too early to read" with the date they'll be readable.

## 4. The placement check

`get_my_ad_breakdown` with `level: "ad"`, `breakdowns: ["placement"]`, `time_increment: "7"`, `ids: [the ad]` (Meta returns `publisher_platform` and `platform_position` per row).

1. For the baseline week and the latest week, compute each placement's share of impressions and its own CTR.
2. **Within-placement test:** did CTR fall inside the placements that carry most impressions (say Facebook feed and Instagram feed)? If yes, fatigue is likely.
3. **Mix-adjusted CTR:** apply the latest week's per-placement CTRs to the baseline week's mix. If the mix-adjusted CTR is close to the baseline CTR (within about 10%, H), the drop came from the mix moving, not from the creative tiring.
4. Say it plainly: "Its CTR fell from 1.6% to 1.1%, but in feeds it held at 1.7%; Meta moved 40% of its delivery to Reels, where it has always run at 0.6%. That's a placement shift, not fatigue." (Illustration of the wording, not real data.)

Read placement CPA with Meta's **breakdown effect** in mind: Meta moves budget to where the next result is cheapest, so a placement with a worse average can still be the efficient margin. Don't exclude placements from this skill.

## 5. The seasonality check

Run the account-level trend over the same weeks: `get_my_ad_breakdown` with `level: "account"`, `time_increment: "7"`. Then for each candidate ad:

- **Relative decline** = the ad's CTR change minus the account's CTR change over the same weeks. An ad whose CTR fell 25% while the account's fell 20% has a relative decline of 5 points: mostly market. An ad that fell 25% while the account held is tiring.
- **CPM:** if account CPM rose 30%+ (H) in the window and the ad's CPM rose by about as much, rising CPA is the auction. If the ad's CPM rose much more than the account's, Meta is finding it harder to deliver (a quality or fatigue signal).
- **Known peaks:** Black Friday to Cyber Monday and the weeks before Christmas raise CPMs across Meta (practitioner reports put Q4 well above Q1). The user's own sale week changes CTR and conversion rate for every ad. Name the period when the window overlaps one and say what to re-check after it.

Today's date matters: in late November, assume seasonality is in play until the account trend says otherwise.

## 6. Creative fatigue versus audience saturation

Read the ad set, not only the ad (`get_my_ads` nests ads under ad sets):

- **One ad declining, its siblings in the same ad set holding:** creative fatigue. Refresh that ad.
- **All ads in the ad set declining together, frequency rising across the ad set, weekly reach flat or falling:** the audience is saturating. A new creative alone buys a few weeks; the lasting fix is a new or broader audience (a new ad set, broad targeting, a lookalike, partnership ads from a creator's audience). Say both.
- **Account-wide frequency above about 3.5 for a week (H, Corey Haines's "hitting the wall"):** the account is short of new people, not of new ads.

## 7. The signals, and how to stack them

Per ad, baseline versus the latest full week (or the last 7 days versus the 7 before, when no clear baseline exists):

| Signal | How | Reads as (H) |
|---|---|---|
| **7-day frequency** | Weekly rows (§2) | Prospecting: under 2.5 safe, 2.5 to 4 warning, over 4 critical. Retargeting: under 4 safe, 4 to 6 warning, over 6 critical. Small audiences (B2B, local): higher before it hurts. Rising matters more than the level |
| **CTR, within placement** | §4 | Down 20%+ from the ad's own baseline: warning. Down 30%+ from its peak: the concept is worn (my-ads notes; Adgence's own alert uses 30%) |
| **CPM** | Ad versus account (§5) | Ad's CPM up 30%+ over 2 weeks while the account's isn't: a leading signal, often before CTR moves |
| **CPA / cost per result** | Windows ending 3 days ago (§2) | Up 20%+: warning. Up 40%+ or above 1.5 times target for 2 weeks: replace (Corey Haines's scaling-ad bar) |
| **Hook rate** (video) | 3-second views ÷ impressions (Meta: `actions` with `video_view`; TikTok: `video_watched_2s`) | Falling while frequency rises: the opening is worn. The cheapest fix |
| **Hold rate** (video) | ThruPlays (Meta `video_thruplay_watched_actions`) ÷ 3-second views; TikTok `video_watched_6s` ÷ `video_watched_2s` | Steady hold with falling hook rate: keep the body, change the hook |
| **Comments** | `get_my_ad_comments` (both Meta threads by default; `since` the latest week; leave out `from_brand` replies) | "Seen this a hundred times", "stop showing me this": a direct fatigue signal; count them, quote one |

On Meta the default fields of `get_my_ad_breakdown` now include `video_play_actions`, `video_thruplay_watched_actions`, `video_p25_watched_actions`, `video_p100_watched_actions`, `inline_link_clicks` and `inline_link_click_ctr`: no `meta_fields` needed (passing `meta_fields` replaces the defaults, so list them all if you add one, e.g. `video_p50_watched_actions`). These are standard Meta insights fields, but the call hasn't run on a real account yet: if a field comes back empty, say which metric is MISSING.

**Confidence:**
- **High:** frequency rising into the warning band **and** within-placement CTR down 20%+ **and** one more (CPM, CPA, hook rate, comments), after the placement and seasonality checks.
- **Medium:** two signals, checks done.
- **Low:** one signal, or the checks couldn't run (TikTok placements, too few impressions).

## 8. The verdicts

Each ad gets exactly one:

| Verdict | Rule | Action |
|---|---|---|
| **Replace now** | High confidence, and CPA is above target or rising 40%+ | Launch the replacement this week. Pause the tired ad only once the replacement is live and spending (never pause without one) |
| **Prepare a replacement** | Medium confidence, or High with CPA still on target | Brief and produce now; replacements take 1 to 2 weeks to make and exit learning |
| **Healthy** | No signal, or signals inside the bands | Keep. Name the date to re-check |
| **Placement shift** | §4 | Keep. Optionally a cut for the placement now taking spend |
| **Market, not fatigue** | §5 | Hold until after the peak |
| **Audience saturating** | §6 | New audience or ad set, plus one new concept |
| **Loser** | §1 | Pause on approval; nothing to refresh |
| **Too early** | §3 | Re-check on [date] |

**Urgency** orders the "Replace now" and "Prepare" ads by money at stake: share of the account's spend in the last 7 days × how far CPA is over target. A tired ad taking 30% of spend comes before a tired ad taking 2%.

## 9. What replaces it: the refresh ladder

Pick the cheapest fix that matches what wore out (Corey Haines's iteration order: hook, then visual, then format, then copy; Motion's iteration types):

| What the data says | Fix | Keep |
|---|---|---|
| Hook rate falling, hold rate steady | **New hook** on the same body: a new first frame and first line | Body, offer, format |
| Hook and CTR falling, frequency high, hold steady | **New visual opening or new talent** with the same script | Angle, offer, structure |
| CTR down 30%+ from peak, or frequency critical, or this concept was already refreshed twice | **Retire the concept:** new angle or new format at the **same awareness level** | What the buyer needed to hear (the pain, the proof) |
| Siblings tiring together | **New audience** plus one new concept | The proven ads, moved or duplicated to the new audience |
| Hook fine, CTR fine, CPA up | Not creative fatigue: check the landing page, price, stock, tracking (conversion rate after the click) | The ad |

**Meta's retrieval system treats ads that look alike as one ad** (Meta's guidance as reported by Jon Loomer): a new text over the same footage, same person and same opening is an iteration, not a fresh ad to the system. When frequency is the problem, change what people **see** first (the first frame, the person, the setting), not only the words.

**Where the replacement structure comes from**, in this order:
1. **The user's own winners** that haven't tired: their structure at the same awareness level (watched with `watch_my_ad`).
2. **`my_ad_library`** (free): Creative DNA already saved in the workspace, filtered by `awareness` (the tired ad's level), and by a different `format` or `hook_type` than the tired ad. Counts come with it (`hook_types`, `formats`, `awareness` tallies).
3. **Competitors' long runners in the niche:** `find_competitor_ads` with the niche and the user's country and `min_days_live: 60` (1 credit). Long run time means they keep paying for it (never "profitable"). Skip catalog ads, drop advertisers outside the niche (a keyword search without its niche filter returns anything that mentions the word: in the Swedish test, 4 of 23 "klänning" ads were from dress sellers), watch 2 or 3 with `break_down_ads` (1 each), save their DNA. In a non-English market, search the local word and the English one: they return different advertisers (local shops versus international ones selling into the country).
   - **Seasonal ads left running are weak evidence.** A midsummer ad still live in November, or a Mother's Day line live since February, shows the advertiser didn't switch it off, not that the occasion sells out of season. Borrow the structure (an occasion everyone knows, the product worn there, the detail) and swap in the occasion that's coming.
4. **Organic videos** for hook and format ideas: `find_viral_videos` (up to 3 credits). Views prove the hook stops people, not that it sells.

Each replacement is written as a **refresh card**: the tired ad (id, name), the verdict and its evidence, what to keep, what to change, the reference structure (ad link and days live, or the user's own ad), 3 hooks (visual, on-screen text, spoken), the test metric (hook rate for a new hook, hold rate for a new body, CPA or purchases for a new concept), and the production effort (low, medium, high).

## 10. Restarting a tired winner

`duplicate_my_ad` (Meta only) copies an ad, optionally into another ad set, **created paused**, and with `keep_social_proof: true` reuses the original post so its likes and comments carry over. It previews first (call without `confirm`), and runs only on the user's yes with `confirm: true`.

When it helps (H): the ad was a real winner, the fatigue is audience-level (§6), and the copy goes into a **different** ad set (a new or broader audience). Copying a tired ad into the same audience mostly buys the same tired people again: say so. Prefer a new ad into an existing ad set (`create_my_campaign` with `into_ad_set_id`) for a new hook; never edit the live winner's creative (that builds a new creative, restarts review and loses its social proof).

## 11. The quality bar

Every flagged ad shows its trend (baseline versus now, with dates and the base of each number), the checks that were run, a verdict, a confidence and a next action. Cut: "this ad is old" as a reason; one-day dips; a list of every ad with its CTR; replacement ideas that don't say what to keep. If a finding doesn't change what the user does on Monday, it goes to the evidence section or is cut.
