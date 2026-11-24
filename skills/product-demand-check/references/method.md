# The demand check method

How a product researcher who has sourced and launched products decides go, test or no-go. Sources: Adgence's product-research and customer-research notes (Jungle Scout and Helium 10 checklists, Exploding Topics trend stages, Dunford positioning), Nexscope's amazon-product-research skill (weighted factor scoring, profit framework), FastMoss's fm-product-scout skill and its price-band case (lifecycle stage from leading indicators, units per competing listing by price band), ScrapeCreators' product-demand-research skill (demand from frequency, intensity, recency; "don't claim validation from a handful of comments"), and three Adgence test runs on 23 Nov 2026 (creatine gummies, posture corrector, wool sauna hat). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data can and can't say

| Field | What it really is | What it can't say |
|---|---|---|
| TikTok Shop `sold` (find_tiktok_shop_products, get_tiktok_shop_product) | TikTok's own **lifetime** unit count for that listing | When it sold. 400,000 units over three years and 400,000 last month look the same. Velocity needs `track` (kind tiktok_shop) or `get_tiktok_shop` read twice |
| TikTok Shop `sales_value` | Units × the price a buyer pays now for the cheapest option (`price_seen_on` says which page showed it) | Revenue. It's a floor and ignores discounts, bundles and returns. Left out when the price is 50%+ off (`promo_price`), so a few units at 90% off don't look like sales. Never call it revenue or GMV |
| TikTok Shop `units_sold_total`, `prices`, `top_shops` | Sums over the listings whose title has every word of the query (`matches_query`), all of them, not only the ones listed; `totals_over` says the base, `all_listings` the raw sum | A clean niche. The word check misses blends, stacks and multipacks that do name the product ("creatine gummies" with whey and collagen; a 3-pack of shower caps "for sauna"). Before the fix, "creatine gummies" put a creatine powder first (625,679 units) and "sauna hat" four winter hats (117,591 of 119,365 units) |
| Amazon `sold` ("2K+ bought in past month") | Amazon's own rounded floor for that listing, last 30 days | An exact count. Quote it as shown. Listings that share a review count are usually variants of one parent: count the pair once |
| Amazon `reviews`, `rating` | Lifetime reviews of the listing (or its parent) | Sales. Reviews are roughly 1 to 3% of orders (H) |
| Google Shopping | Listings across stores, with price, rating and review count | Sales. Ratings at retailers (Target, Walmart) run lower and thinner than on Amazon |
| Etsy | Listings with price, rating and the Bestseller badge | Listing reviews: the count shown is the **shop's** reviews |
| `signals` on find_products | Computed per listing: "highly rated on few reviews" (4.5+ on under 300), "premium price" (130%+ of median), "budget price" (70% or less), "established bestseller" (5,000+ reviews), plus Amazon badges | Anything about the market beyond the 16 to 48 listings returned |
| `price_range` on find_products | Min, median, max of the listings returned, multipacks and off-target items included | Clean price bands. Recompute from matching single units |
| Niche ads (find_competitor_ads with `niche`) | Live Meta ads matching the keyword, marketplaces dropped; per advertiser `ads_in_this_search` and `longest_running_days` | An advertiser's size (read the brand for `live_ads_total`), spend or results. When `relevance_note` is present the AI off-niche check didn't run and a word check did: `unconfirmed_advertisers` lists those that only mention the product in copy. Judge each one: in the re-tests that list held a sauna-cabin shop, an apartment building and a clothing brand, but also real creatine-gummy sellers (Crave, OMNI, Arrae). `other_market` marks ads aimed at another country |
| `sold_out` and `low_stock` on get_tiktok_shop_product | Options at 0, and options with 1 to 49 left | Demand on their own. Many sold-out options are often retired flavours; a short `low_stock` list on the main options is the moving signal |
| Selling `videos` on get_tiktok_shop_product | The 5 to 20 linked videos TikTok picks for the page, most viewed first or newest first (`videos` "newest", `newest_video`), with post dates and creators' @handles | All the affiliates. The dates are the leading indicator (§4) |
| Comments and reviews | What people who wrote said | Share of all buyers. Reviews hold buyers only; Reddit and YouTube hold non-buyers' objections |

**A product the tools can't see in a market is MISSING, not low demand.** No TikTok Shop listings in the UK? TikTok gives no product pages there. No Amazon results? Say so and score from what was read.

## 2. Clean the read before scoring

1. **Match titles.** `find_tiktok_shop_products` already drops listings whose title lacks a word of the query from its totals and says so (`totals_over`, `products_matching`): quote it. Then check the matches yourself: keep only listings that are the product (form, not just keyword). Keep variants (sour, for women, 2-pack) and note multipacks; drop substitutes (powder for gummies), look-alikes (winter hats for sauna hats) and blends or stacks where the product is one ingredient of several. Recount units, listings and prices when what you drop moves the numbers, and say how many were dropped and why. Amazon and Google Shopping have no match flag: clean them by title as before.
2. **Merge variants.** Amazon listings with the same review count and rating are one parent. Count their "bought in past month" once (take the highest).
3. **Normalise price** per unit when packs differ (2-pack $19.99 is $10 each). Per serving for consumables when the count is in the title.
4. **Season check.** Late November is Black Friday: Amazon tags most listings "Black Friday Deal" and TikTok discounts run deep. Monthly Amazon numbers in November and December run hot; say so.

## 3. The six scores

Each is 1 to 5 (5 = good for the user), with its evidence line (numbers, base, source) and a confidence. Thresholds are (H).

### Demand: do people buy this now?

Sum what you can see, from matching listings only, and name each source.

| Score | Rule (H) |
|---|---|
| 5 | Three sources agree: Amazon "bought in past month" summed 20K+ across matching listings, TikTok Shop matching units 100K+ lifetime, and 3+ real advertisers with ads 90+ days |
| 4 | Two strong sources: Amazon 5K to 20K a month, or TikTok 10K to 100K lifetime plus 2+ advertisers with ads 30+ days |
| 3 | Amazon 1K to 5K a month, or TikTok 1K to 10K lifetime, one source clearly selling |
| 2 | Under 1K a month visible anywhere; a few listings selling a few hundred lifetime units |
| 1 | Nothing selling in any market read (and the market is one the tools can see) |

Organic interest (find_viral_videos views, comment volume) can lift confidence, never the score: views aren't purchases. Buying-intent comments ("link?", "where do I buy") count more than views.

### Saturation: is there room for one more?

Start at 5 and take a point for each flag present (floor 1). Name every flag you counted.

- **Review moat:** the top 5 matching Amazon listings average 5,000+ reviews (Jungle Scout's older rule: the top 10 averaging under ~500, with 3+ under 50, means room).
- **Clone shelf:** many sellers with near-identical titles or images, or the **same ad copy word for word across different advertisers** (posture: one "Suffering from scoliosis…" text on Axzenia and three Corecare pages).
- **Discount race:** half or more of the matching listings show a discount, or 30%+ show 50%+ off (TikTok Shop flags these `promo_price`); a price floor under a third of the median.
- **Trust collapse:** "scam", "doesn't work", "fake" in 10%+ of comments (H), or ads attacking the category itself ("All posture corrector ads you see online are fakes").
- **One seller owns it:** the top shop or brand holds 60%+ of matching TikTok units, or one brand fills most of the Amazon page.

Counter-signal (add one back, max 5): new listings already selling, i.e. "highly rated on few reviews" or "New on Amazon in past month" listings with 1K+ bought in the past month. New entrants are getting through.

### Price headroom: does the user's price sell?

1. Per marketplace, the matching listings' P25, median, P75 and max. Where does the user's price sit?
2. **Units per listing by price band** (FastMoss's method: the tallest band is also the most crowded, so divide units by the listings fighting for them). Bands of about $10, or the natural gaps. Report the median listing's units per band, not just the leader's, so one giant doesn't make a band look easy.
3. **Rating per band:** if the premium band isn't rated higher, its premium is brand alone and attackable.

| Score | Rule (H) |
|---|---|
| 5 | The user's price sits at or below P75 where listings sell, and the band's median listing sells well |
| 4 | Above P75 but proven sellers exist at or above it, in the channel the user will sell in |
| 3 | Above P75 in one channel, proven in another (Amazon ceiling $30, Etsy median $38: sell the $39 hat on DTC and Etsy, not Amazon) |
| 2 | Above every proven seller; only a lone outlier sells there |
| 1 | Above the max of every marketplace read, or the category sells on price alone (a discount race and a sub-$15 median) |

### Ad competition: is paid social proven, and is there room?

From the niche ads read (after dropping off-niche advertisers):

| Score | Rule (H) |
|---|---|
| 5 | 2 to 6 real sellers advertising, at least one ad past 90 days, nobody with copied creative, room for a new angle |
| 4 | Proven paid demand with one strong leader you'd need to out-create, or real sellers whose ads are all 30 to 90 days (young: paid demand only partly proven) |
| 3 | No real advertisers yet (paid social unproven: marketplaces and organic carry the category), or only ads under 30 days |
| 2 | Many advertisers on the same claims, discount-led offers |
| 1 | Cloned creative across pages, fake-anchor discounts ("regular $234"), risky health claims everywhere: an expensive auction full of tactics you shouldn't copy |

A niche search's count per advertiser is the ads that matched the keyword, not the brand's size; never call a brand small from it. Read the leader with `find_competitor_ads` (competitor) if size matters to the call.

### Fixable complaints: can a better version win?

Count themes with `count_mentions` (several phrasings each; spot-check examples), then sort each theme:

- **Fixable by the product:** fit and sizing, materials that chafe, packaging that melts, dose transparency, flavour, too many units per serving, velcro that fails.
- **Fixable by the brand:** shipping, returns refused, "not as advertised" (honest listing).
- **Category-level (not fixable by a better version):** "the whole category doesn't work", "a cheaper substitute does the same job" (powder versus gummies), safety doubts about the ingredient.

| Score | Rule (H) |
|---|---|
| 5 | The top 2 complaint themes are fixable, each 5%+ of comments, and no leader claims the fix |
| 4 | Fixable complaints lead, but a leader already claims the fix (you'd compete on proof) |
| 3 | Mixed: fixable themes exist but a category-level objection is as loud |
| 2 | Category-level objections lead |
| 1 | Category-level objections lead and are amplified (thousands of likes, a debunk video) |
| MISSING | Fewer than 50 relevant comments: say "directional" and score with Low confidence, or leave it MISSING |

Word counts over-count: "not gritty" matches "gritty", "fits perfectly" matches a sizing theme, and "better than powder" matches a price-versus-powder theme. Count complaints from reviews rated 3 or lower: pass `max_rating` 3 to `count_mentions` itself (it takes the same filters as `get_customer_comments`), and give praise themes separately. A product page's first 300 reviews skew to 5 stars: say the share of reviews rated 3 or lower, with its base (Bloom: 8 of 131 read).

A topic collection can still hold a page about another product: `get_customer_comments` lists the pages with keys and `about_topic`, and `pages_note` names any review page whose title doesn't name the product. Leave those out with `pages`, and read the `url` on the examples. `collect_customer_comments` with `tiktok_shop` now reads only best sellers whose title matches.

### Margin: only when the user gives a landed cost

| Score | Rule (H) |
|---|---|
| 5 | Price ≥ 4× landed cost and ≥ $30 |
| 4 | 3× to 4×, ≥ $25 |
| 3 | 2.5× to 3×, or 3×+ under $25 (little room for ad cost) |
| 2 | 2× to 2.5× |
| 1 | Under 2× |

Show the line: price, landed cost, marketplace fee (Amazon referral about 15%, TikTok Shop and Etsy fees vary: say "about"), and what's left for ads and profit per unit. No cost given: MISSING, and the verdict says what landed cost would make it work (price ÷ 3).

## 4. Lifecycle stage (from FastMoss's two-axis model, rebuilt on Adgence data)

Units are lifetime and lag; the **leading indicators are new videos and new sellers**. Read the stage from:

- the post dates of the leader's selling videos (get_tiktok_shop_product with `videos` "newest"; `newest_video` is the latest): most of them from the last 90 days = still growing; the newest more than a year old = momentum has peaked (Beast Bites, 23 Nov 2026: newest 2025-07-06);
- new listings selling: "New on Amazon in past month" or "highly rated on few reviews" with 1K+ a month;
- niche ad start dates: a cluster of ads started in the last 60 days = sellers piling in.

| Stage | What you see | What it means for entry |
|---|---|---|
| New | Few listings, small lifetime units, fresh videos, no proven ads | A bet. Test cheap |
| Growth | New listings selling, recent videos, ads launching | The best window |
| Peak | Huge lifetime units, leaders' videos old, many sellers, discounts starting | Enter only with a clear difference |
| Plateau | Old videos, discount race, cloned listings | Closed for a me-too product |

Say the stage is inferred, and offer `track` on the leader's shop to turn lifetime counts into units per day.

## 5. The verdict: gates, then scores

Averages hide deal-breakers (a 5 on demand doesn't rescue a 1 on margin), so the verdict uses gates first (Nexscope's weighting is kept only to order the scores: demand and margin first).

- **No-go** when any of these hold: Demand ≤ 2 in every market the tools can see; Margin ≤ 2; Saturation ≤ 2 **and** Price headroom ≤ 2 (a crowded shelf that only sells cheaper than you); Saturation ≤ 2 **and** the user's product is the same as what's sold (no answer to the leading complaint: a me-too into a closed shelf); Fixable complaints = 1 and the user's version doesn't change the objection. When the me-too gate fires, say what version could pass it (the conditional Test).
- **Go** when Demand ≥ 4, no score below 3, Margin ≥ 4 (or the user's cost is unknown: "Go if your landed cost is under $X"), the overall confidence is Medium or High, **and** the price headroom score isn't Low confidence (demand proven at $20 says little about $39).
- **Test** in every other case, including thin data. A Test always names the cheapest experiment that settles the weakest score, with its success measure (e.g. "50 units on Etsy at $39 in 30 days", "a paused Meta test at $20 a day for 7 days, target cost per purchase under 1/3 of price").

**Confidence:** High = three independent sources agree (two marketplaces plus ads or comments); Medium = two; Low = one, or under 50 relevant comments, or most of the read had to be dropped as off-target.

**What would change my mind:** every verdict ends with the one fact that would flip it (a landed cost, a cleaner review read, units per day from tracking).

## 6. Positioning the opening

When the verdict is Go or Test, name the opening in one line built from the scores: the segment, the price band, the fixable complaint to solve, the proof to show. Examples of the shape (not data): "the honest-dose gummy at $35 that publishes its lab test", "the fit-engineered brace sold by chest size". Never carry a competitor's claims, wording or health promises into it; flag risky claims (health, results, scoliosis, "fixes posture forever").

## 7. The quality bar

Every score has a number, its base and a source. Every finding changes the decision or goes to the evidence section. Cut: listing products with no "so what", "TikTok sales are huge" without saying which listings match, lifetime units presented as monthly, sales value called revenue, and validation claimed from a handful of comments.
