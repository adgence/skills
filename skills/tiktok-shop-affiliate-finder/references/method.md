# The affiliate finder method

How a TikTok Shop operator or agency reads a category and builds a recruit list. Sources: Adgence's product-research notes and the TikTok Shop notes in the Adgence skill; FastMoss's open skills (creator outreach and its glossary: selling power over followers, leading indicators, product lifecycle stages); Corey Haines's influencer-marketing skill (vetting checklist, usage rights, disclosure); ScrapeCreators's influencer-prospecting skill (scored prospect list, contact path); the r/ecommerce Monday affiliate routine and Reacher's cross-brand creator agent (research notes); and Adgence test runs on 23 Nov 2026 (creatine gummies US, lip oil US, creatine gummies UK), re-run after that day's tool fixes. Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data can and can't say

**Never visible, write MISSING:** each creator's GMV or units sold, commission rates, who got samples and who posted (the user's own affiliate data lives in TikTok's Seller Center, not in Adgence), a shop's ad spend, and how sales split between creator videos, LIVE and the shop's own posts.

### What the fields really are

- **`sold` is TikTok's lifetime count** for the product, since it was listed. A 2023 product and a 2-month-old product can show the same number. For sales per day, `track` the shop (kind `tiktok_shop`) or read `get_tiktok_shop` again later: `since_last_check` then gives units per product since.
- **`sales_value` is units × the price shown today:** a floor, never revenue. A promo price wrecks it, so the tools leave it out when the price is 50%+ off and flag `promo_price` (Myprotein's UK creatine gummies: 18,669 units at £0.46, 95% off). Say "sales value (floor)", and still treat a 30 to 49% discount with care.
- **A keyword search ranks what TikTok ranks,** not what matches. "creatine gummies" in the US put EKKO creatine *powder* first (625,679 units). Each listing now carries `matches_query` (the title has every product word of the query) and the totals are over the matching listings only (57 of 58 there, 610,876 units against 1,236,563 raw). The flag is a word match: a "creatine monohydrate gummies" 11-in-1 blend with collagen and biotin passes it. Read the titles and recount if you drop more.
- **Prices can differ between tools for the same product** because TikTok shows different promotions on the search and on the shop page the same day (Ovrload crealyte one-month: £17.90 in a search, £16.90 in the shop read). `price_seen_on` says where a price came from; quote it with the price.
- **Stock:** `sold_out` lists options at 0, `low_stock` options with 1 to 49 left. Many sold-out options usually means retired variants, not a sell-out (Beast Bites: 9 of 14 options at 0, the rest in stock from 73 to 4,427).
- **Selling videos (`get_tiktok_shop_product` `videos`) are the ones TikTok picks for the page** (5 to 20), not every affiliate. `videos: "newest"` orders that pick newest first and `newest_video` gives its latest date, but it can't add videos TikTok didn't pick: the list freezes on a product's big hits, so new small sellers stay invisible there (see §2).
- **The creator on a selling video comes with a display name; the tool resolves the real handle for about the first 10 videos** (`handle`, with a working link). The rest keep the display name and an `@_` link (on Vital Source, 10 of 20 resolved, and knotips's and ttsTam's later videos stayed `@_` though their first ones resolved). A video with the same `creator_id` as a resolved one is the same creator. For the rest, watching the video (`break_down_ads`) returns the real handle ("Cami•mode" is @mcamila0301), or the display name may be the handle. Say "matched by name" until confirmed, and give the video link.
- **`find_brand_creators` reads the US TikTok Shop only,** from the brand's top 3 shop products (Vital Source's: mostly its pH-balance gummies, so most of its 34 sellers aren't creatine sellers). Its result can be very large (Kulfi: 41 sellers, 30 taggers; Vital Source: 34 sellers, 40 taggers, 128,375 characters on 23 Nov 2026, though the tool is described as compact). About half the sellers have a `handle` (19 of 34). `may_be_clip` marks videos whose caption never names the brand or product: clip and repost accounts, or lazy captions. `tagging_window` says which dates the Instagram taggers cover (the newest 84 posts: Jun 2025 to Nov 2026).
- **`get_creator` on an affiliate:** `sponsored_breakdown.tiktok_shop_affiliate` now counts shop-linked posts (uma_star03: 10 of 10; 50SinExcusas 10 of 10), and `campaign_tags` names brand or platform pushes (#TIKTOKSHOPBLACKFRIDAY). `cadence` counts posts over their real date span with pinned posts left out, and gives `posts_per_day` when they span under a week, as affiliates post in bursts (uma_star03 2.5 a day, SwillTok 6.9). Affiliates' median views are low by design (uma_star03 301, SwillTok 1,280) while single shop videos hit 1 to 3 million: judge them on their shop videos, not their median. The newest posts also show whether they still post the competitor's product.
- **Audience countries** (`get_creator` `audience: "countries"`, +1) come from a small sample of followers (99 for SwillTok, 154 for Dr Karan Rajan). Directional only.
- **No product pages in the UK, Germany, France, Spain or Italy,** so no selling-video lists there; Brazil has price, units and seller only. There, read shops (`get_tiktok_shop`), verified buyers' reviews (`collect_comments` with `country`) and creators by the leading competitor's brand or product name with `country` (`find_creators` topic "ovrload crealyte", country GB: 15 GB sellers of Ovrload, all shop-linked posts from Aug to Nov 2026). A category topic still fails there even with `country`: "creatine gummies" with GB gave 1 GB creator and 10 `country: "unknown"` ones from the semantic search, all American. Treat "unknown" as not local, and check the best with audience countries.
- **Answers are reused free when the same request comes again** (24 hours for a search, 12 for a product or shop read). A reused answer from before a tool change can lack new fields (`matches_query`, `promo_price`, `price_seen_on`): ask again with `refresh: true` when you need them.

## 2. Read the market first (5 minutes, 1 to 3 credits)

| Measure | How | Read it as |
|---|---|---|
| Matching set | Products that really are the user's product type, from the search | Everything below is "of the N matching" |
| Concentration | Top shop's share of the matching units | Over 50% (H): one brand owns the shelf; affiliates already know it |
| Price bands | Quartiles of matching prices (product-research §3) | Where the user's price sits; commodity cluster or brand-led |
| Brand vs generic | Branded shops versus look-alike listings | Many generic clones = a price fight; affiliates go where commission per sale is highest |
| Stage (lifecycle) | Post dates of each top product's selling videos | See below |

**The lifecycle read** (FastMoss's leading-indicator rule, adapted to what Adgence shows): new creator videos lead sales by about a week, so the age of the selling videos tells you where a product is. Read the page with `videos: "newest"`; `newest_video` is the date to judge, counted from today.
- **Growth:** most selling videos posted in the last 90 days (H). Creators are flooding in. Affiliates are actively choosing this product: recruit from its sellers now.
- **Peaked, still worked:** the page's videos are 3 to 6 months old (H), but its sellers' own newest posts (`get_creator`) still show the product this week, in small numbers. The big push is over; volume affiliates are still earning on it. (Vital Source creatine gummies, read 23 Nov 2026: 20 page videos from 1 May to 5 Jul 2026, 141 days old, yet 50SinExcusas posted 3 #vitalsource videos on 21 Nov at 500 to 650 views each, and uma_star03 posts creatine daily. Read in July, the same page said growth: the date of the read matters.)
- **Mature / plateau:** the top videos are all 6+ months old (H) and nothing recent made the list. Big lifetime units, but the creators who made it are cold on it or moved on, and some may be open to a new product. (Beast Bites: 15 videos from Jul 2023 to Jul 2025, 425,965 lifetime units.)
- **New:** few units, few videos. A bet, not a signal.
The page's list freezes on a product's big hits, so it can't show new small sellers: that's why the sellers' own newest posts are the check. Medium confidence on one read; High once the shop is tracked for a few weeks (units per day).

## 3. Sort every creator into an archetype

The single most useful call: different archetypes need different offers.

| Archetype | How to spot it | What to offer | Watch out |
|---|---|---|---|
| **Volume affiliate** | Posts 2+ a day in bursts (`cadence`, H), many unrelated shop products, nearly every post shop-linked (`sponsored_breakdown`), median views under ~2k, but some shop videos at 1M+ | Free sample + open or targeted commission. Cheap, many of them. A numbers game | Their median says nothing; their hit rate does. Expect most samples to produce small videos |
| **Category creator** | Posts about the topic or niche repeatedly, views close to or above followers, fewer products | Sample + higher commission, or hybrid (small fee + commission), plus Spark rights | Fewer, pricier, best content |
| **Repeat seller of one product** | 2+ selling videos on the same competitor product (knotips 4 on Vital Source, ttsTam 4, uma_star03 3), or still posting it in their newest posts | Same as above: repeat posting means the product paid them (inferred) | Ask before assuming they're free: they may be under a retainer |
| **Exclusive partner** | Co-branded product or kit (Kulfi lists "Lexie's Kulfi Holiday Kit"; lexallynn has 3 Kulfi selling videos and a bio pointing to "@Lexie Lynn"), a name that also appears in the brand's `running_its_ads` (a "Sylvia" sells Kulfi on TikTok Shop and a "Sylvia Van Hoeven" runs Kulfi's Meta ads: a name match, inferred), founder of a rival brand (Dr Karan Rajan founded his own supplement brand) | Usually skip. Name them so the user knows | Recruiting them is unlikely and can start a fight |
| **Deals account** | A deals name or bio ("Taze_Deals", "lousdealz", SwillTok's Crocs, HeyDude and Nike posts), any product that's on offer, captions about price ("4 bags cheaper than 2 from their own site") | Sample + commission for volume during sales; not a brand voice | Sells the discount, not the product: expect sales to stop when the offer does |
| **Clip or repost account** | `may_be_clip`, captions unrelated to the product, famous-person clips (deals_detector: Sam Sulek clips selling Beast Bites, 25.1M views) | Skip for the brand; note that the competitor's reach partly comes from them | Rights and policy risk |
| **Brand's own account** | Shop name as creator (Beast Sports Nutrition's own taste test, 5.7M views) | Not a recruit: it's their content strategy | Shows what the brand itself films |
| **Critic or expert** | "Stop wasting money on creatine gummies", "most gummies have no creatine" | Not a recruit. Their objection goes in the brief (show proof) | High reach, negative frame |

## 4. Score the recruits (each 1 to 3, H)

- **Proof:** has a shop video selling this product type. 3 = 1M+ views on a competitor's product; 2 = 100k+; 1 = posts about the topic, no shop proof.
- **Recency:** 3 = selling video in the last 90 days, or still posting the product in their newest posts; 2 = 90 to 365; 1 = older.
- **Repeat:** 3 = 3+ videos on one product; 2 = 2; 1 = one.
- **Fit:** language, persona and audience match the user's buyer (Vital Source's sellers are Spanish-language women's-fitness creators; Beast Bites's are men's gym creators).
- **Available:** 3 = no exclusive sign; 1 = co-branded kit, own brand, or a retainer hint.
- **Risk** (flag, not a score): health or body claims in their captions (glute growth, cellulite, "BBL" on Vital Source), clip content, missing disclosure.

**Tiers:** Recruit now (Proof 3, Recency 3, Available 3), Send a sample (strong Proof or Fit, one weak spot), Watch (old proof or unclear identity), Skip (exclusive, clip, critic, or risk you can't brief around). Never present a creator as a fit without an evidence video link.

## 5. What makes the selling videos sell

Watch the top 2 or 3 selling videos of the product nearest to the user's (`break_down_ads`, 1 each, free if watched in 30 days). Write their DNA and look for what the set shares: the hook, how fast the product appears, whether the creator eats or applies it on camera, the claim, the CTA ("tap the orange cart"). Mark what's borrowable (structure, demo, pacing) versus not (their claims, especially body or health results). This becomes the affiliate brief.

## 6. What buyers say (the brief's proof list)

Verified buyers' reviews on the leading competitor product (`collect_comments`, 1, any country) counted with `count_mentions` (free). Read the low ratings separately (`get_customer_comments` `max_rating: 3`). What buyers praise is what affiliates should show; what they complain about is what the user's affiliates must prove on camera (Ovrload UK: taste mentioned in 130 of 200 reviews, "better than powder" in 43; 19 of 200 rated 3 or less, mostly a bitter aftertaste after the first seconds and gummies clumping together). Under ~50 relevant reviews, call it directional.

## 7. Decide, then plan

| Decision | When |
|---|---|
| Poach the growth product's sellers | A competitor is in growth and its sellers fit: invite them with a sample and a better commission |
| Revive the mature product's sellers | A competitor is mature: its old top sellers have proof and may be free; check they still post shop content |
| Seed volume affiliates | The category runs on volume posters: a sample budget and an open plan, judged on hit rate over 4 weeks |
| Pay a few category creators | The category is trust-heavy (supplements, skincare claims): fewer creators, better content, Spark rights |
| Don't fight here | One brand owns most units, prices are collapsing, or the category's objection ("gummies have no creatine") needs proof the user doesn't have yet |

Then the plan: who to invite (the ranked list), the sample count (MISSING: their budget and margin; ask), the brief (from §5 and §6), the commission line (MISSING: the user sets it; never invent a rate), what to measure after 2 and 4 weeks (videos posted per sample sent, views per video, orders: all in their Seller Center), and which videos to Spark once posted.

**Outreach drafts** stay drafts: one line on why them (their video, its views, the product), the sample offer, what to show, the disclosure line. Never sent by the skill.

## 8. Compliance in every brief

TikTok Shop affiliate videos are paid content: disclosure in the video and caption, the platform's own label; no health or body-change promises (glute growth, cellulite, weight loss), no "results in 2 weeks", no fake testimonials. Flag competitor videos that rely on such claims as risky to copy.

## 9. The insight quality bar

Every finding is evidenced (a video link with views and date, a product with units, a review count), non-obvious and tied to a decision. Cut: lists of creators with no archetype or next step, "they use UGC", follower counts as a ranking, lifetime units presented as current momentum, sales value presented as revenue.
