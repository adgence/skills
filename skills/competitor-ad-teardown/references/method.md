# The teardown method

How a senior creative strategist and media buyer reads a competitor's ads. Sources: Adgence's competitor-analysis and creative-strategy notes, Dara Denney's ad library analysis, Motion's competitor-watch and creative-strategist skills, Foreplay's recipes, and five Adgence test runs (Crave Creatine, Gymshark, Oh My Chewy, ClickUp vs monday.com and Asana, Tropic Skincare; 23 Nov 2026). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data can and can't say

The Ad Library shows each live ad's creative, copy, start date, platforms, landing page and how many copies of a creative run. It never shows spend, results or targeting: write MISSING for those, every time.

- Run time means the brand keeps paying for an ad. It does not mean the ad is profitable.
- Only live ads are visible. Stopped ads show up only once the brand is tracked (`track`), so one read can't say what they killed, and pace only counts ads still live.
- New products are too young to judge by run time: use launch pace instead.

### The sample: check it before anything else

**`find_competitor_ads` reads a brand on purpose** (`brand_read`): its 60 most-seen ads (by impressions), its 50 newest and 50 live 90+ days, merged (Crave, 23 Nov 2026: 119 ads, as some carry two tags), each tagged on the ad (`read_as`), plus Meta's exact counts of all live ads and of those past 30 and 90 days. So lifespan is exact ("245 of 719 live ads have run 90+ days", Meta's count), pace comes from the `newest` list and start dates, and the window check below is about which tag a finding rests on. `live_ads_total` is Meta's exact count; every other measure is "of the N read, out of M live".

The read is still a mix chosen by Adgence, not a random sample: its median age means little (Crave's read has a median of 62 days because it is built from three parts), and its share of ads past 90 days is set by the tag, not by the brand. Quote Meta's counts for lifespan, and the tags for everything else. Before this read existed, the library's own order decided what was seen (Crave's 120 were its oldest; Gymshark's 120 its sale week): if a result ever comes back without `brand_read`, fall back to the start-date check below.

**Sample window check (do it every time):** look at `running_30_days_plus` against `live_total`, and at the start dates of the ads tagged newest.
- **Most live ads past 30 days** (Crave: 541 of 719): an account of anchors. The proven message comes from the 90+ day and most-seen tags; the newest tag shows what they're betting on now.
- **Almost nothing past 30 days** (a new brand, or one that rebuilt its account for a sale): proven winners don't exist yet. Say so in the verdict: "nothing here has run long enough to call a winner". Don't run the signal ladder on it; read it as "what they're launching now". Offer `track` to build the picture.
- **Without `brand_read`** (start dates only): a median of 60+ days means the old runners were read and recent pace will look like a collapse (never say "they slowed down"); a median under 14 days with nothing past 30 means only the newest were read.

Never conclude from absence in a sample: with 120 of 719 read, "no launches since November" and "they run no video" can't be said. The `min_days_live` and `media` filters only filter the ads already read: an empty filtered result means "none in the ads read", not "none at all".

**Seasonal check:** if the read falls in a sale period (Black Friday to Cyber Monday, Christmas, a launch), say so. Sale ads tell you about offers, not about the creative that wins the rest of the year. Timing advice ("don't launch into their 65%-off week") is often the most useful line for a founder.

### What the fields really are

- **The `hook` field is the ad's primary text,** not the video's opening. Brands reuse one text across many different videos (Crave: one text on 42 ads). So count **copy families** from the text, and **concepts** from the creative itself: `creative_id` when present (Meta's id for the creative; ads sharing it are copies), otherwise the thumbnail or the watched video. Never count concepts from copy alone.
- **A `catalog` note on an ad** marks dynamic and catalog ads, from Meta's own format (DPA, DCO) (feed products and text): count them apart and never watch them for creative lessons.
- **Formats in the list are the library's label.** Without watching, a whole account labelled "carousel" may be dynamic or catalog ads: give format counts Low confidence until watched.
- **Copy-less dynamic carousels** are catalog ads for ecommerce. For software and apps, call them "dynamic product carousels"; the word "catalog" reads wrong to a SaaS client.
- **A niche search's `ads_in_this_search`** is how many of that brand's ads appeared in that search, not its account size (brands showing 1 ad there ran 96 to 264). Read the brand before calling it small.
- **`product_filter`** (with `product`): "53 of 155" means 53 of the ads read name the product in copy, headline or link. The brand's `live_ads_total` still counts every product (Arrae: 920 live US ads, many of them not creatine). All ads read stay saved, the unmatched ones marked `product_match: false`. Words miss the product's own ads that never name it, so also count the unmatched ads landing on the product's pages (Arrae, 23 Nov: 53 name creatine, 39 more land on its Tone creatine pages: about 92 creatine ads of 155). `single_category` means the page name is the product, and every ad counts.
- **A dynamic ad can lose its copy in the brand read.** Some come back copy-less and marked `catalog` while a keyword search returns their words (Create's 81-day "Sour Grape" and "Juice Box Bundle" ads; an Arrae "Tone Sour Watermelon" ad). Before calling an angle absent from a brand, run the angle as a niche keyword (1 credit).
- **`get_brand_ad_profile`** reads its own sample for the `country` you pass (default every market: always pass it). It names survivors only once its checks span a week; before that each test says "too early to call". Use its test groups and launches per week. It now marks a variant stopped only when a later near-full read misses it (Crave, 23 Nov evening: 12 snapshots, 0 stopped, the 12 Nov group all live), and names the page even when called by id. Its weekly pace comes from the ads read, so gaps between the read's parts look like weeks with 0 starts: when its pace disagrees with the start dates of the ads you read, trust the start dates.

## 2. Read the whole account

Page through the saved ads with `find_competitor_ads` `more_from` and `offset`, asking for `detail: "compact"` and `limit` 60: one short line per ad, so 60 ads read in one page (about a third the size of full ads). `detail: "copy"` gives the whole primary text for coding angles, without media, and carries no `read_as` tag. Each line names the advertiser (`brand`) and, for partnership ads, the creator page it ran under (`shown_under`). Tag every ad read.

| Measure | How | Read it as |
|---|---|---|
| Volume | `live_ads_total` | Size of the operation |
| Pace | Start dates of the ads read, per week; last 4 weeks vs the 12-week average | Testing budget. A floor, given the sample check |
| Lifespan | Median days live; share past 30 and 90 days | A low median with a few 300+ day ads = a test-and-kill machine with a few anchors |
| Copy families | Distinct primary texts; how many ads each carries | Their proven words |
| Concept rate | Distinct creatives (thumbnails, watched videos) / ads read | New ideas, or one winner reskinned? Not countable on an account that's almost all carousels or catalog: say so |
| Format mix | Video, static, carousel, catalog (library labels; Low until watched) | Where their money goes |
| Landing mix | Product page, collection, home, quiz, advertorial or listicle, signup or demo page (from the link) | How they convert. Several advertorials = cold traffic at scale; one page for every persona = a personalisation gap |
| Platform spread | Meta, TikTok, Google (Google: `still_showing` ones only; their words via `get_ad_media`) | Spread = bigger budget. Google search = they buy intent. A Google display ad's words show which claims they lead with off Meta |
| Brand language | Phrases repeated across many ads, verbatim | The words they've proven |

## 3. Which ads matter: the signal ladder

Only on a sample that holds old ads (see the sample check). Read ads in **age cohorts** (Motion's competitor-watch): long-runners 60+ days (evergreen winners), survivors 15 to 60 days (the **strongest** signal of what's working now: they've had enough data and kept running), testing zone 7 to 14 days (moderate), fresh tests under 7 days (strategic intent, not results). What survivors share is the finding; fresh tests tell you what they're thinking about next.

| Status | Rule (H) | Confidence |
|---|---|---|
| Proven winner | 90+ days live, or 60+ days with 3+ copies | High |
| Being scaled | New variants of an old concept still launching, or 3+ copies live. Copies = `same_creative_count` plus the same creative under different ad ids | High when stacked with run time |
| Likely winner | 30 to 90 days | Medium |
| In test | Launched in a same-day batch in the last 14 days | Low: a bet, not a result |
| Catalog / evergreen | Catalog, dynamic, retargeting offers | Ignore for creative lessons |

When almost nothing has run 30+ days (a new or seasonal account), say "no proven winners visible", name the one or two that have, and lead with pace, offers and what they're launching.

Confidence on every finding: **High** (stacked signals), **Medium** (one strong signal), **Low** (run time only, or library labels).

**Signal strength** (Motion):

| Signal | Strength |
|---|---|
| A 15+ day survivor carrying a new angle | Very high: a proven message they're scaling |
| 3+ fresh tests exploring the same new theme | High: a coordinated bet |
| They promote something the user doesn't have | High: a competitive gap |
| Format mix shift (e.g. UGC share doubled) | Medium |
| Volume spike or drop | Medium: budget moved |
| One ad with an unusual approach | Low to medium: a one-off test |
| Copy variations on existing themes | Low: skip unless a pattern forms |

**Stopped ads aren't in the library's answer.** Only tracking shows what they switch off. When it does, a stop isn't always a loss: a sale ended, budget moved to a hotter ad, a newer version replaced it, or it hit its goal. A quick stop (under 5 days) with no promo in it usually is a loss; 2+ weeks before stopping usually means it had a fair run.

## 4. Tag every ad with fixed values

Use Adgence's Creative DNA labels so report counts match the saved library:

- **Hook type:** problem, question, pov, shock_stat, before_after, testimonial, demo_first, unboxing, listicle, discovery, fake_text, green_screen, challenge, offer_first, other
- **Format:** ugc_talking_head, founder_story, product_demo, unboxing, before_after, slideshow, faceless_broll, skit, reaction, green_screen, voiceover_demo, static_image, carousel, motion_graphics, other (catalog or dynamic counted apart)
- **Emotional trigger:** curiosity, fomo, insecurity, relief, social_proof, authority, humor, aspiration, frustration, surprise, belonging, urgency, guilt, pride, comfort
- **Awareness:** unaware, problem_aware, solution_aware, product_aware, most_aware
- **Angle** (report only): pain, aspiration, identity, convenience, taste/experience, cost saving, mechanism/ingredient, authority, social proof; for software also time saved, consolidation, collaboration, AI
- **Persona:** who's on screen, who the copy addresses. For software: role (agency, sales, ops, founder), company size, industry
- **Offer:** none in ad, discount %, bundle, subscription, guarantee, free gift; for software free plan, free trial, demo, template, webinar, migration help. Never borrow an offer from a brand's other ads
- **Proof:** reviews/stars, lab test, expert, numbers, demo, press; for software named customers, logos, G2 or Capterra, user counts

Weight the counts: what carries their proven winners is their **proven message**; what their last 4 weeks share is **what they're testing now**.

## 5. Watch the ads that matter

- **Check `my_ad_library` first** (free): ads of this brand already broken down in the workspace are reused, not re-bought, and saving again would overwrite them.
- Pick **distinct concepts**: one per proven message, plus 1 or 2 from the newest test batch. Skip catalog ads.
- `break_down_ads` takes **up to 10 per call; send 3 at a time when the videos are long**: three long videos with their frame sheets already fill most of a reply (tested 23 Nov 2026). Copies of a creative already watched come back free (`same_creative_as`), so pick one ad per `creative_id`. It can return some ads now and the rest shortly after (`credits_pending`): save each batch's DNA as it arrives. Ads watched in the last 30 days are free; `get_ad_media` on a just-watched ad (free) returns all of its frames when the batch sent only two.
- Carousels and statics return the first card or the image only; say so.
- Write each one's Creative DNA:
  - **Hook** in three layers, quoted exactly: first frame, on-screen text, spoken line.
  - **Beat sheet:** time, what's on screen, on-screen text, what's said. Mark the product's first appearance, the proof, the offer and the CTA.
  - **Pacing, measured, never estimated:** "14 cuts in 22 s, product on screen at 3.5 s".
  - **Message:** promise, proof, mechanism, objection handled, offer.
  - **Score (H), 1 to 5 each:** hook strength, clarity of the promise, proof, pacing, product visibility, CTA, native feel; plus compliance risk (none, some, high).
  - **Why it likely works**, and what's borrowable (structure, mechanic, pacing) versus not (their claims, footage, wording, brand).
- Look across the watched set for the common factor; it's often not what you'd guess (Crave's long runners ranged from 0 to 14 cuts; showing the product by 3.5 s was the shared trait).

## 6. What they're learning

- **Name the bet of each test cluster** (Motion): ads with the same angle but different hooks, formats or creators are one question. Say the question: "they're testing which creator converts on one script", "whether UGC beats a screen recording for this feature".
- **Promotions:** sales, launches, events, webinars, partnerships: dated, so they explain spikes.
- **Sub-products:** ads that send traffic to several domains or products are several strategies; read each apart.

- Ads launched the same day are one test. Say what differs: hook, format, persona, offer or **landing page** (Crave sent the same creative to 5 advertorial pages on one day; Gymshark sent the same copy to its site and to an Instant Experience).
- **One snapshot: never name winners**, whatever the profile tool says. Offer `track`.
- With tracking history: survivors, losers, and the kill rate per launch week.

## 7. Positioning

**Through a buyer's eyes** (Motion): if someone deciding between the user and this competitor saw these ads, what would they now think? Which of their claims the user can't match today; which claims the user could make better but doesn't advertise; which proof would sway the buyer; how their creative quality compares. This is the line a sales team or founder remembers.

From their ads and store (`get_store`, Shopify only; software pricing pages aren't readable: MISSING): who they target, what they claim, how they prove it, at what price (bands, share on sale, compare-at prices, bundles, subscriptions), with what guarantee (from ad copy). **Compare the user's price with theirs**, per unit or serving when both are known. Name the two axes buyers trade off in the category and place both brands.

**Their landing pages:** if your environment can open web pages, read the 2 or 3 pages their ads send the most traffic to: headline, offer, price and proof, quoted exactly (Boring Marketer's competitive-intel). If it can't, say the landing type comes from the link only.

**Absence is a finding** (Boring Marketer): no ads on a platform, no proof, no offer, no new launches in months all tell you something.

**But absence in one account isn't absence in the category.** An angle this competitor never uses may be another rival's whole campaign. Before naming an angle as the user's empty ground, check the rivals selling the same product (`compare_competitor_ads` with `product`, or a niche keyword search). Crave never says "sour" in 119 ads, yet on 23 Nov 2026 three rivals sold sour creatine: Arrae has run "The Sour Way to Sculpt" (15 ads) and "3 Sour Flavors, 1 Crave-worthy Creatine Routine" on its gummy variety pack for 93 days, aimed at women ("sculpt lean muscle definition without the bloat"); Create runs sour flavor drops (Sour Grape since September, "3 sour cherry gummies a day" for toned arms); Animal Pak launched "Sour Surge" creatine chews for lifters ("Seriously SOUR. Seriously STRONG.", 31 days). Watching them showed what the copy couldn't: Arrae's sour ad is a silent jar-and-bite clip with a body-image line and a neutral face, Animal Pak's a pack shot. The opening is a framing nobody shows (the sour hit on a face as proof), not "sour" itself, and not "sour for lifters" either.

**Cross-check ads against the store:** products the ads push that the store shows sold out, or offers in ads that don't match the store, are real findings.

## 8. Where they're weak

- **Angles, personas and awareness stages they never use**, checked against the category (§7) before you call them empty.
- **Landing mismatches:** persona ads that all land on one generic page; offers the page doesn't carry.
- **Risky claims:** health or results claims, comparative claims without proof, templated testimonials, profanity, numbers that don't match between video and copy. Flag them as risky to copy.
- **B2B risk checks** (software and apps): superlatives and dated claims ("#1 tool of 2025"), the same claim with different numbers across ads, other brands named, creator posts without a paid-partnership disclosure, garbled text and test URLs left in live ads.
- **Claims versus complaints** (Deep, or a next move): reviews (`collect_comments`: Trustpilot 1, Amazon 2, TikTok Shop 1) or the comments under their most-viewed ad or creator post, counted with `count_mentions` (free; split two-sided themes into helped and caused).

## 9. Decide, then plan

| Decision | When |
|---|---|
| Borrow the structure | A proven winner's format or mechanic fits the user's product and doesn't need the competitor's brand to work |
| Counter-position | They own an angle the user can flip or beat |
| Take the empty ground | An angle, persona or stage they skip, and no rival in the category runs (checked) |
| Avoid | Risky, saturated, or needs their brand equity |
| Ignore | Catalog noise, sale-only ads, one-off tests |

Then a **testing matrix**: 3 to 5 concepts ranked by evidence × gap × fit to the user's budget × ease of production. Each: hypothesis; angle + mechanic + format; 3 hooks (visual, text, spoken); the reference ad with link and days live; the success measure (purchases or CPA for a new concept, signups or demos for software; hook rate for a new hook; hold rate for a new body); what we learn even if it loses. Name each ad `Concept_Angle_Hook#_Format`. Mark concepts that need facts only the user has (their dose, their proof, their customers). When nothing was watched (Quick read), call the matrix a draft from ad copy only.

## 10. The insight quality bar

Every finding is evidenced, non-obvious and tied to a decision. Two tests (Motion): take the numbers away, and the reasoning must still hold (it explains what's happening in the buyer's mind, not just a count); and it must not fit any brand in the category. Lead with the one finding that changes the most and give it room; mention the rest in proportion. If everything feels equally important, judgment is missing. Label figures **read** (straight from a tool), **worked out** (with one line on how) or **blank** (and why); a wrong number is worse than a blank.

Calibrate the tests to the user's budget (Motion): smaller budgets start with problem-aware and solution-aware buyers; scaling budgets add unaware buyers to grow beyond existing demand. Cut: ad lists with no "so what"; "they use UGC" as an insight; run time called profit; advice that fits any brand; detail dumps. If a finding doesn't change what the user does, it goes to the evidence section or is cut.
