# The angle gap method

How a senior creative strategist finds what customers care about that no competitor's ads say, and turns it into tests. Sources: Adgence's competitor-analysis notes (§4 positioning, §5 claims versus complaints), creative-strategy notes (layer stack, matrix, two-sided persona check, concept scoring), customer-research notes (sources, counting, confidence, pitfalls); Boring Marketer's competitive-intel skill (saturated / contested / white space, "unbought territory", the good-gap test); Corey Haines's customer-research skill (confidence table, sample bias, minimum data points); ScrapeCreators' comment-mining skill (buckets, "separate one loud comment from a repeated pattern"); Motion's creative-strategist skill (insight bar, "reading bets, not results"); April Dunford's positioning; and three Adgence test runs on 23 Nov 2026 (creatine gummies, dog itch chews, tallow skincare). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data can and can't say

**Competitor side.** The Ad Library shows what each brand pays to say right now: copy, creative, start date, landing page. Never spend, results or targeting (MISSING). Run time means they keep paying for it, not that it's profitable. Only live ads are visible, so "nobody says X" means "no live ad in the ads read says X".

**Customer side.** Comments and reviews show what people say unprompted. Reviewers are buyers with strong feelings; Reddit and YouTube hold non-buyers and skeptics, which is where objections live. Neither is a survey: counts are "of the N collected", never "% of the market".

**The gap is an inference across two samples.** Say both bases every time: "102 of 904 comments raise price; 5 of 272 competitor ads read mention it."

## 2. Get the right market

- **The category as buyers say it** ("creatine gummies", "dog itching", "tallow balm"), not the brand's own words. The customer topic and the competitor set must be the same market.
- **Check the competitor set before trusting any absence.**
  - A competitor with 0 live ads (`why_none`) can't claim anything: say so, and don't count it as "absent".
  - A competitor whose ads are all from the last 7 days or a sale (Fat and the Moon: 3 ads, all started today, all a holiday sale) tells you about offers, not positioning.
  - **Single-category brands:** their testimonials, offers and founder posts often never name the product ("I've noticed a huge transformation in my skin"). When the page name or website names the word, `product` now reads the brand as single-category and counts every ad (`single_category`: The Tallowed Truth, 115 of 115; before that fix it kept 52 of 99). When neither does and the note says most ads name the product, page past the matches (`product_match: false`, saved after them) and code the rest as on-product if the brand sells nothing else.
  - **Multi-product brands:** read them with `product` set to the category's word (`compare_competitor_ads` or `find_competitor_ads`): ads naming it in the copy, headline or landing link are listed, every ad read is saved (matches first), and `product_filter` says matched of read (Arrae: 31 of 135 about creatine; Toups and Co: 20 of 123 about tallow). Say that base. Every word you pass must match, so pass one word. It's the word in the ads, not the user's: a brand that names its product line ("Arrae Tone") may leave the category word out of some ads, so when the match looks low against what the brand sells, read again with the line's name (1 credit) and merge.
  - **Thin set** (fewer than 2 competitors with 20+ relevant ads (H)): the ad side can't prove a gap. Find who really advertises in the category (`find_top_advertisers`, 3 credits), then read the 2 or 3 leaders (`compare_competitor_ads`, with `product` for the multi-product ones), before calling anything "unclaimed". In the tallow test the user's three brands had 6 tallow ads between them; the real leader had 104, and the top gap changed completely.
  - `find_top_advertisers` ranks by `ads_matching_niche` (ads in this keyword search) with `total_live_ads` (Meta's count for the whole account, every product) beside it: Ancestral Cosmetics (11 in the tallow search, 180 in all) now leads, and Toups and Co (3 in the search, 518 in all) sits third. `relevance_by` says which check judged the niche. When it's "words" (the AI check is off), the check drops ads without the niche's words but can't tell a seller from a mention, and `unconfirmed_advertisers` lists those with the words only in ad copy. Drop the ones that don't sell in the niche (the tallow search kept a tattoo-removal clinic and two vaginal-dryness ads) and say so; the list also holds real sellers (The Tallowed Truth, both its pages), so check before dropping. A brand often runs a founder page beside its brand page ("Ben Corkery Founder at The Tallowed Truth", 7 ads, against 2 on the brand page): add them up when you rank.
  - **Season:** in a sale week the newest batch is offers. On 23 Nov 2026, 46 of Ancestral's 113 ads read started that day (Black Friday) and about 44 of The Tallowed Truth's 115 were a sale. Code them as offer and leave them out of "new angle" calls.
- **Country:** one market. Mixing markets makes "nobody says it" meaningless.

## 3. Read what competitors say (the claims side)

`compare_competitor_ads` reads each brand once (1 credit each, free when it has none, reused free for 24 hours) and saves every ad it read; page each brand's saved ads with `find_competitor_ads` `more_from` (free). Paging is compact by default (one short line per ad, text clipped to 110 characters: good for counting, not for coding); use `detail: "copy"` for the ads whose copy you code (full copy, headline, CTA and brand, no media; 25 to 30 long-copy ads a page). Copy lines carry no `read_as`, so page the same ads `compact` when you need the tags. The view's full lists no longer reach the agent (fixed 23 Nov 2026: Claude Code got 25 copy lines from `more_from`, not the full ads).

**Where the ads come from.** For one brand the library is read in three parts, tagged on each ad (`read_as`): the most seen, the newest, and ones live 90+ days. `brand_read` gives Meta's exact counts for the whole brand (live, 30+ days, 90+ days). Measure pace and lifespan from those counts and tags, never from the order of the list. Stopped ads aren't in the library: a brand's switched-off tests are invisible unless you track it.

**Code every relevant ad against one codebook** (built in §5), from its primary text and headline. Skip ads flagged `catalog` only when they carry no written copy. The flag now marks feed ads (template text, or next to no words), and dynamic-creative ads with written copy come back `dynamic: true` instead (before that fix, 36 of Create's 38 flagged ads had full copy, including the price and the women-over-45 tests). A feed carousel can still carry real primary text above its product cards (both of Ancestral's flagged carousels did), so code what's written. For each angle and each brand record three numbers:

| Number | Means | Why |
|---|---|---|
| All | Ads read that carry the angle | Volume of the claim |
| Proven | Of those, live 60+ days (H) (or `read_as` running_90_days) | What they keep paying to say: their proven message |
| New | Of those, started in the last 14 days (`read_as` newest marks the newest batch) | What they're testing right now |

Then a status per cell:
- **Owns**: 5+ proven ads (H), or the angle sits in their longest runners.
- **Claims**: some ads, mostly not proven.
- **Testing**: only new ads (last 14 days). A competitor launching your angle this week is the most time-sensitive finding in the report.
- **Absent**: none in the ads read (with the base).

**What copy coding misses.** The text is often a template reused across different videos (Crave: one text on 44 of 122 ads). Angles carried only in the video (on-screen proof, a reaction, a persona) are invisible until watched. Watch 2 to 4 ads where it matters (§6) and check `my_ad_library` first (free; earlier breakdowns carry `audience.pain_point`, `persona`, `awareness_level`). Copy-only counts are Medium confidence at most.

**Brand language matters.** Regexes over-match: "lab" matches "PetLab", "pit" matches "pitch", "focus" matches "Ford Focus" in comments, a landing URL with `/discount/` reads as a sale. Code from the ad text only (never the URL), and spot-check 3 hits per angle before writing a number.

## 4. Read what customers say (the voice side)

**Reuse first (free):** the board file "Customer language", and `collect_customer_comments` on the same topic within 7 days comes back free (`reused: true`). A page someone already collected comes back free from `collect_comments`.

**Mix sources by what each one holds:**

| Source | Who speaks | Use it for |
|---|---|---|
| Reddit, YouTube comments | Buyers and non-buyers, skeptics | Objections, alternatives ("just use powder"), the category's enemies |
| TikTok Shop, Amazon, app stores | Verified buyers | Product failures, what the category delivers or doesn't |
| A competitor's Trustpilot or listing | That competitor's buyers | Complaints about them: trust gaps and experience gaps |
| Comments under a competitor's top ad | Their audience, live | Objections their ads trigger |
| Forums, Facebook groups (`find_forum_threads`) | Niche communities | Personas and jargon |

A topic collection with three sources costs about 4 to 5 credits (Reddit 3 pages, 2 TikTok Shop listings, YouTube free; the creatine one cost 4); with no sources named, Reddit and YouTube (about 5). It says `pages_planned` and `max_credits` when it starts, and `get_customer_comments` reports what it actually cost (`credits_charged`). One competitor review page is 1 (Amazon live 2).

**Check what was collected before counting.** A topic search can pull pages about a different product: the "creatine gummies" collection held a creatine **powder** listing (55 of 117 texture mentions and 54 of 88 price mentions came from it); "dog itching allergies" held a hot-spot spray listing (96 "spray" matches). `get_customer_comments` lists every page with a key (p1, p2…), its title, comments and `about_topic`, and `pages_note` flags review listings whose title doesn't name the topic. Say which pages are off-product, then count without them: `pages: [the other keys]` on `count_mentions` and `get_customer_comments`, or subtract the page's share using each theme's `by_page`. A Reddit thread without a title is now judged from its link and text; a titled thread that doesn't name every word of the topic still reads `about_topic: false` ("I got suckered into trying beef tallow" in a "tallow balm" pull): read the title before dropping one. A count that still mixes an off-product page is a floor and loses a confidence level. If the host refuses `pages` or `min_rating` (an input validation error from a stale tool list), subtract with `by_page` and read buyers with `source` and `contains`.

**Count across sources in one go.** `count_mentions` takes `collection_ids` (a topic collection plus a competitor's review page from `collect_comments`): each comment is counted once, and `by_page` keeps them apart.

**Read in this order** (customer-research notes): low ratings first (`max_rating: 3`), then most liked, then long 4 to 5 star reviews (`min_rating: 4`). Clip verbatim. Then count.

**Count with `count_mentions`** (free), 10 to 20 themes, several phrasings each:
- Check `matched_terms` on every theme and drop terms that match the wrong thing.
- **Two-sided themes** ("chalky" vs "not chalky", "itch" vs "still itchy", "price" vs "great price") are split into the complaint and the praise, or reported as "mentions, both directions". Never call a two-sided count a complaint count.
- **People, not likes, rank themes.** Likes weight intensity (log scale (H)); one viral comment can carry most of a theme's likes (one 10,000-like comment carried 12,160 likes of the price theme): say so.
- **Off-topic threads:** Reddit drifts (a creatine thread held kratom comments). Spot-check the examples.
- **Mentions are not stances.** A term count says who raised a word, not what they think of it: "olive oil" matched 48 tallow comments, many neutral or approving. For the theme your top gap rests on, read the matches most-liked first and hand-code the stance (of the 60 most-liked add-in mentions, 26 blamed the add-ins or asked for a tallow without them, 5 defended them). Report it as a floor with how many you read.
- **Buyers and skeptics split.** TikTok Shop reviews skew happy (18 of 451 tallow reviews were 3 stars or less), and they often praise what Reddit fears: 21 of the 24 buyer reviews that mention breakouts say the skin cleared; the 3 that broke out were all one men's balm. Report the two sides apart; a fear non-buyers hold is still an objection your ad has to meet.
- **One listing's problem is not the category's.** Read `by_page`: on the creatine gummy listing "formula changed" (5) and "too sweet" sat on one brand's reformulation; a theme that lives on one page is a fact about that product.

**When is a theme real?** (H, customer-research notes) 3+ distinct people across 2+ independent pages. Confidence: **High** = 3+ sources and 20+ mentions, consistent; **Medium** = 2 sources or 8 to 20 mentions; **Low** = one source or under 8. Under about 50 relevant comments in total, everything is directional.

## 5. Build one codebook for both sides

The rows of the matrix are the **angles**, defined once and applied to ads and comments alike:
1. Start from the customer themes (they define what matters).
2. Add every angle the competitors' ads use that customers don't raise (these become "over-bought" rows).
3. Add personas (identity words in comments: "as a woman", "my 13 year old", "German shepherd") and experience themes (subscription, shipping, cancel).

Each row gets one angle name in plain words (pain, desire, objection, persona or experience), the customer number (people, share, sources, likes, confidence) and one cell per competitor (owns / claims / testing / absent, with all/proven/new counts).

## 6. Watch only where the text can't tell you

Watch (1 credit each; free if watched in the last 30 days or a copy of a watched creative) when:
- A competitor "claims" a gap angle in copy and you need to know how they prove it on screen.
- A competitor's newest batch touches your angle (watch one of the batch).
- The user's own angle (their flavor, their mechanism) may already be shown visually somewhere.

Save each one's DNA with `save_ad_dna`. Two to four ads is enough for this job; a full teardown is a different skill.

## 7. Cross them: the gap types

| Type | Rule | Decision |
|---|---|---|
| **Open gap** | Real customer theme (3+ people, 2+ sources) and Absent at every competitor, or only Testing at one | Take the ground. Strongest when the theme is an objection non-buyers raise |
| **Trust gap** | Competitors Own or Claim it, and customers contradict it (complaints about exactly that claim) | Win with proof the others don't show, not a louder claim |
| **Counter-position** | A competitor Owns the opposite of what customers want (sells as a feature what customers blame) | The strongest opening: they can't follow without changing the product. Only if the user's product truly differs |
| **Race** | Absent except one competitor Testing it in the last 14 days | Move this week, or differentiate the execution; track that competitor |
| **Persona gap** | An identity shows up in comments but never in ads | Test a persona-specific hook, if the persona is one you can serve |
| **Experience gap** | Complaints about subscriptions, shipping, refunds at a competitor | An offer or guarantee angle; quote, never accuse |
| **Table stakes** | 2+ competitors Own it and customers praise it | Say it, but never as your lead |
| **Over-bought** | Competitors claim it heavily; customers rarely raise it | Don't lead with it; it isn't what buyers think about |
| **Off-limits** | The gap exists because it's regulated or unsafe: drug comparisons, medical or weight claims, marketing to minors | Avoid, or name the compliant version |
| **Thin** | Fails the "real" bar on either side | Report as directional; suggest the read that would settle it |

**The good-gap test** (Boring Marketer, adapted): before ranking a gap, ask why nobody runs it. *Hard* (needs proof, honesty, a product truth others lack) is a real opening. *Worthless* (buyers don't care) or *forbidden* (compliance) is not. Write the reason in one line.

**Absence is weaker than presence.** "Nobody says it" rests on the ads read and on copy coding; a gap whose ad side rests on a thin set, a sale week or copy-only coding is Medium at most.

## 8. Rank, then decide

Score each candidate 1 to 3 on four things (H; creative-strategy notes):
- **Evidence:** people × sources × intensity.
- **Gap:** absent everywhere (3), testing at one (2), claimed weakly (1).
- **Fit:** can the user say it truthfully? Needs their facts: dose, price per serving, ingredients, guarantee, how it tastes. Unknown is MISSING, never assumed.
- **Safety:** no health, weight, income, drug or minor-targeting claim needed (3), wording needs care (2), risky (1).

Rank by the product. Show the top 3 to 5, then the avoid list.

For each of the top gaps write the **angle card**:
- The angle in one sentence: pain or desire × persona × the promise, at an awareness stage (Schwartz).
- Why nobody runs it (the good-gap reason).
- The proof the user needs, and which of it is MISSING.
- 3 hooks (visual / on-screen text / spoken), lifted from verbatim lines; mark which words are verbatim.
- The structure to borrow (a competitor ad with its link and days live), never their claims or wording.
- The format that suits the stage (creative-strategy §5).
- The success measure: purchases or CPA for a new angle; hook rate for a new hook.

Then a **testing matrix**: 2 to 3 angles × 2 to 3 hooks each, one format per angle, named `Concept_Angle_Hook#_Format`. Mark what each test teaches even if it loses.

## 9. The insight quality bar

Every gap is evidenced on both sides with bases, non-obvious, and tied to a decision. Cut: "customers care about price" without the competitor side; gaps that fail the good-gap test; themes from one loud thread presented as the market; angles that need a claim the user can't make. If a finding doesn't change what the user tests, it goes to the evidence section or is cut. Motion's test applies: if it could fit any brand in the category, it isn't specific enough.

## 10. Compliance on everything publishable

Hooks and angles never assert a personal attribute ("Struggling with your weight?"), never promise health, weight, income or guaranteed results, never compare to a drug ("replaces Apoquel"), never target minors, never invent testimonials. Quote customers only as data in the report; a real comment shown in an ad needs permission. Complaints about a named competitor are quoted with their link, never summarised as an accusation.
