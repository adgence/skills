# Example: a finished fatigue check

Two parts, labelled. **Part A** is a real run of this skill on 23 Nov 2026 (Founder mode, no ad account connected, creatine gummies, US): its competitor ads, run times and pacing are real tool results from that day and must never be reused for another run. **Part B** shows the shape of the connected-account answer using Adgence's demo example account ("Example Supplements Co.", an invented company used in Adgence's demo recordings, `apps/web/public/demo/myads_*.json`). Part B's numbers are illustrative, not a real account and not a real run; never quote them to a user.

No real ad account was connected when this skill was built. The connected path is written from the tool code and still needs a test on a real account.

---

## Part A: real run, no account connected (23 Nov 2026)

*As of 23 Nov 2026, Meta, US.*

**No ad account is connected to Adgence, so I can't read your ads' trends, and nothing below is a judgment on your ads.** Connecting takes about a minute on your Adgence account page, Ad accounts section.

Once it's connected, the check is free and takes about two minutes: each ad's weekly frequency, link CTR, CPM and CPA against its own first weeks, then three checks before I call anything tired: placement (did CTR fall inside feeds, or did Meta move the ad to Reels?), the market (this is Black Friday week; an ad is only tiring if it fell more than your whole account), and the ad set (if every ad in it is falling together, you're out of new people). "CTR keeps going down" fits all of these, and only one means "make new ads".

### Replacements lined up now, by what wore out

What I read: creatine gummy ads live 60+ days in the US (23 ads, 10 advertisers; I dropped 4 off-niche advertisers and 4 catalog ads because the niche filter didn't run), 5 Crave ads already broken down in your library, and 3 watched today. Run time means they keep paying for it, not that it's profitable.

| If this wore out | Replacement structure | Evidence |
|---|---|---|
| **The hook** | "I messed up" fake-out: list each benefit as "it's not because...", land on a harmless twist tied to a real offer | Arrae, [1520225566815245](https://www.facebook.com/ads/library/?id=1520225566815245), 93 days; 25 s, 8 cuts, first cut 3.7 s |
| **The face and setting** | One-take identity endorsement: a customer whose job your buyers respect | Tasty Gains, [1010803008019352](https://www.facebook.com/ads/library/?id=1010803008019352), 173 days; 95.4 s, no cuts (labelled "image" in the library; it's a video) |
| | A sport nobody sells creatine in | Crave, 805897681821369, 408 days; 22 s, 6 cuts |
| **The concept** | Making-of process with the reason the dose holds | Crave, [1115849997026202](https://www.facebook.com/ads/library/?id=1115849997026202), 499 days; 35.4 s, 13 cuts |
| | Powder versus gummy race, one take | Crave, 1390279629735742, 106 days; 41.3 s |

**Avoid:** "most creatine gummies don't even have creatine in them". Crave has run it 499 days and OMNI runs it in two ads (84 days) with a lab claim about 4 of 6 bestsellers: crowded, and a comparative claim you'd need your own lab report for.

**This week:** connect the account; meanwhile shoot one new opening (the fake-out) and one new face in a new setting. **One question:** what's your target cost per purchase?

Credits: 4.

---

## Part B: the connected-account shape (illustrative, demo account)

*Illustration only. Demo example account "Example Supplements Co." (invented), Meta, USD, 24 Oct to 22 Nov 2026, from Adgence's demo recordings.*

The demo recording holds 30-day totals per ad set, not the per-day or per-week series this check needs, so it shows **how the skill refuses to call fatigue from totals** and where the real verdicts would go.

**The verdict (3 lines):** Of the three prospecting ad sets, "Founder video test" reads as a weak test, not a tired winner: $1,360 spent, ROAS 0.58, CTR 1.36% over 30 days, below "Broad · Women 25-44" (CTR 1.51%, ROAS 2.26) and "Lookalike 2% buyers" (CTR 1.82%, ROAS 4.3). Totals can't show whether any of them is tiring: that needs the weekly rows. Nothing here should be refreshed yet; one test may be paused.

**The refresh list** (what the real answer fills in from `get_my_ads` daily and `get_my_ad_breakdown` weekly rows):

| Ad set | Spend (30 d) | CTR | ROAS | Frequency 7-day, wk 1 → wk 4 | Link CTR, wk 1 → wk 4 | Placement check | Market check | Verdict |
|---|---|---|---|---|---|---|---|---|
| Broad · Women 25-44 | $2,940 | 1.51% | 2.26 | MISSING (not in recording) | MISSING | not run | not run | Too early to judge from totals |
| Founder video test | $1,360 | 1.36% | 0.58 | MISSING | MISSING | not run | not run | **Likely a loser, not tired**: ROAS 0.58 against its campaign's 2.01 in these totals; the weekly rows confirm whether it ever worked. Pause on approval |
| Lookalike 2% buyers | $520 | 1.82% | 4.3 | MISSING | MISSING | not run | not run | Too early to judge from totals |

**The action**, previewed first (the demo's pause recording): pause "Founder video test", status ACTIVE → PAUSED, $45 a day, "Stops every ad in this ad set". It runs only on the user's yes, and only once a replacement is live in its place.

**What a real connected run adds:** each ad's weekly frequency and link CTR from its first weeks to now, the within-placement CTR, the account's own CPM trend over Black Friday week, sibling ads in the same ad set, comments that say "seen this", and for each tired winner a watched breakdown and a refresh card from Part A's kind of structures.
