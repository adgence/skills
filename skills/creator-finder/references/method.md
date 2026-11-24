# The creator-finder method

How an influencer manager finds, vets and shortlists creators, and decides who to contact first. Sources: Adgence's creative-strategy and copy-and-scripts notes (creator brief, FTC disclosure), Corey Haines's influencer-marketing skill (tiers, vetting checklist, deal models, rate anchors), FastMoss's creator-outreach skill and its creator-shortlist case (selling power over followers, verdict per creator, "who to skip and why"), ScrapeCreators's influencer-prospecting skill (six-part scoring, contact path), the research shortlist (#13), and three Adgence test runs on 23 Nov 2026 (creatine gummies vs Create, dog probiotics vs Native Pet, tallow balm micro creators), re-run the same day after the tool fixes. Thresholds marked (H) are heuristics from those runs and practitioners: use them, and say they are.

## 1. What the data can and can't say

Every number comes from a creator's **public recent posts** (`get_creator` reads 10 to 20; `posts_read` says how many) and a platform search. It never shows a creator's rates, their sales, their reach in your target country beyond a small sample, or what a brand paid them. Write MISSING for those.

- **Views are the currency on TikTok, not followers.** A 200,830-follower account had a median of 364 views a post (mercercaiden); a 9,266-follower account had 6,387 (faithhnfitness). Followers are what a creator once earned; median views are what you'll get.
- **A post about the topic is the strongest fit signal there is.** A creator who posted about your category unprompted is worth several cold ones (Corey; FastMoss). It is also the only thing `find_creators` returns: every creator there has an evidence post.
- **Only recent posts are read.** "0 posts about the topic" in `get_creator` means none in the last ~20, not never (faithhnfitness reviewed Bloom's gummies in March; her last 20 posts don't include it).
- **Everything is one snapshot.** Follower growth, spikes and history need the full audience report (+60 credits, ask first) or a later re-check.

### What the fields really are

| Field | Tool | What it is | What it can't say |
|---|---|---|---|
| `evidence[]` | find_creators, find_brand_creators, find_similar_creators | Up to 3 posts that name the topic (caption match, or TikTok speech and text for empty captions), with views and date | Whether the post was paid (only `sponsored` hints); whether it sold |
| `posts_about_topic` | find_creators / get_creator | How many of the posts found (or of ~20 recent) name the topic | Older posts |
| `median_views` / `best_views` | get_creator | Middle and top views of the recent posts read. Instagram: reel plays (`views_note` says how many reels: kenny_selmon 28,689 median from 12 reels) | Photos and carousels have no views on Instagram; if no reel came back, the note says so: judge by likes against followers |
| `engagement_percent` | get_creator | Median of (likes + comments) ÷ views per post | It's by views, not by followers. Can read 0 on Instagram when the reels came back without likes (curvylanna): recompute from `posts` |
| `views_to_followers` | get_creator | Median views ÷ followers, rounded to 2 decimals | Dead reach rounds to 0 (mercercaiden 0.002, maiahenryfit 0.003): compute it yourself |
| `posts_per_week` / `posts_per_day` / `cadence` | get_creator | From the dates of recent posts with pinned ones left out (`pinned_left_out`); `posts_per_day` and a note instead when they span under a week (mercercaiden: 10 posts in 0.6 days, where the old field said 140 a week) | On Instagram the window can still stretch over a year when older reels mix in (`over_days` 374 for kenny_selmon, 545 for curvylanna, both active weekly or daily): recount from the `posts` dates |
| `sponsored_posts`, `sponsored_breakdown`, `campaign_tags`, `brands_tagged_in_sponsored` | get_creator | Posts that are paid or #ad-tagged, TikTok Shop affiliate posts (product link, shop hashtags) or carry a brand campaign tag (all-caps tags like #PETLABCOBFCM, "…partner" tags like #BEYONDPARTNER), split by kind | Codes in plain words and brand names without a tag; the creator's own handle can appear among the brands (home_swaps). **Read the captions** |
| `audience: countries` | get_creator (+1, TikTok only) | Countries of a sample of about 100 followers | Ages and gender (full report only). Low confidence: it's ~100 people |
| `audience: full` | get_creator (+60) | Ages, genders, countries, follower credibility | Only on a yes, for 1 or 2 finalists |
| `contact` | get_creator `include_contact` | The bio email (free), else the profile's published contact, else a lookup (+5 only when an email is found) | Whether they answer |
| `running_its_ads` | find_brand_creators (2) | Facebook pages running Meta ads that link to the brand's site, from up to 100 ads in all countries: whitelisted creators and partners | It mixes advertorial or affiliate pages with creators (K9HealthDaily for Native Pet). Pages, not handles |
| `selling_on_tiktok_shop` | find_brand_creators | Creators whose videos sell the brand's top 3 TikTok Shop products (US), most viewed first, with their `@handle` where TikTok returns it (17 of 38 for Create, 22 of 47 for Primally Pure); `may_be_clip` when the caption never names it; the brand's own account left out when its name matches | Sellers without a handle (links read `tiktok.com/@_/video/…`): name and video only. The brand's own account can still slip in under a longer brand name (Primally Pure in Primally Pure Skincare's list): drop it. A product from another shop can be among the 3 (Saje peppermint roll-on videos in Create's list): drop sellers whose captions are about another brand |
| `tagging_it` / `tagging_window` | find_brand_creators | Instagram posts tagging the brand's account: the newest 4 pages (about 84 posts); `tagging_window` gives the dates (Create: 16 to 23 Nov; Primally Pure: 14 Oct to 23 Nov) and whether older ones exist | The full roster. Events, retailers, gift bags and other brands tag too |
| `counts` | find_brand_creators | The size of each group; the compact answer shows the top 20 of each, `detail: "full"` all | Some hosts receive the full lists anyway (139,140 characters for Create): read the saved file by group |
| `similar_but_not_about_topic_yet` | find_similar_creators | Platform lookalikes of a seed creator who haven't posted about the topic | Fit to the product: they match the person (style, audience) |
| `brand_accounts` / `looks_like_brand` | find_creators | Accounts the tool thinks are brands, each with its reason (a company name, "I make" or "Handmade" in the bio, the brand's voice in captions, the product or a business word in the handle) | **It still misses some makers.** Tallow, first run: 9 of 16 missed; re-test: 4 caught, 3 of the 8 left were still makers or a persona (516homestead "grab it on our website", grassfedsoaps "we chose", oliviabrooks_skincare). Read every bio and caption ("our", "shop", "I make", "link in our bio") |
| `country` / `platform_counts` | find_creators | With `country`, TikTok posts from other countries are dropped; each platform keeps places and `platform_counts` says how many each gave | Instagram and YouTube creators are `country: "unknown"`; a follower range drops creators whose count couldn't be confirmed (the note gives the number) |

## 2. Where creators come from (the five sources)

| Source | Tool | What it finds | Best for |
|---|---|---|---|
| **Paid by a competitor** | find_brand_creators `running_its_ads` | Creators and partners whose pages run the competitor's Meta ads (whitelisting) | Seeing the competitor's creator strategy and price level |
| **Sells a competitor on TikTok Shop** | find_brand_creators `selling_on_tiktok_shop` | Affiliates and their best videos | Commission-only recruits; proof the category sells there |
| **Tags a competitor** | find_brand_creators `tagging_it` | Gifted, ambassador, PR and event posts this week | Micro and nano creators a brand already seeds; the ambassador model |
| **Posts about the topic** | find_creators | Creators with evidence posts on TikTok, Reels, YouTube | Genuine users of the category |
| **Looks like a great one** | find_similar_creators with `topic` | The seed's lookalikes, split into posted and not yet | Scaling a persona once one creator fits |

Read the competitor first: its roster tells you **who it already locked**, **which personas it pays for** (Create: moms around 40, GLP-1 users, ex-athletes), and so **which persona is open**.

## 3. Vet one creator: the measures

For each finalist, `get_creator` with `topic` (1 credit). Read the numbers, then the last 10 captions.

| Measure | How | Read it as (H) |
|---|---|---|
| **Median views** | `median_views` | What one post buys you. The number to plan on |
| **Best ÷ median** | `best_views` ÷ `median_views` | Over 10: one hit carries the account; plan on the median |
| **Views per follower** | `views_to_followers` | TikTok: 0.3+ healthy (faithhnfitness 0.69, oliviabrooks 0.57, dr.kelsyvickdpt 0.37). Instagram reels: kenny_selmon 1.52, curvylanna 0.37, catherine.lockhart 0.16, home_swaps 0.08 (reel plays, so compare Instagram with Instagram). Under 0.05: the followers don't watch any more (mercercaiden 0.002, clarahyee 0.01, samantha_ccole8 0.03, dealswithdc 0.04, terianxio 0.05) |
| **Engagement** | `engagement_percent` (by views) | TikTok 3 to 8% normal; under 1% weak (dealswithdc 0.2, terianxio 0.5). Instagram reels, by views: 2 to 4% seen on healthy accounts (kenny_selmon 2.1, catherine.lockhart 3.2, home_swaps 2.6); by followers (for photo posts, computed yourself): 1 to 3% healthy at micro (Corey) |
| **Posting rhythm** | `posts_per_week` or `posts_per_day`, checked against the dates | 1 to 7 a week = a creator. A `posts_per_day` burst of different products = an affiliate posting for commission (mercercaiden: 10 posts in 0.6 days, all TikTok Shop). Under 1 with a long gap = may have stalled (faithhnfitness: nothing from 9 Aug to 18 Nov) |
| **Sponsored share** | `sponsored_posts` ÷ `posts_read`, `sponsored_breakdown`, plus captions | Over 30%: their audience sees ads all the time. All affiliate = Commission only (mercercaiden 10 of 10). Codes and brand names in captions count even when unflagged |
| **Competitor ties** | captions, `brands_tagged_in_sponsored`, find_brand_creators | Paid by a direct competitor in the last 90 days: ask about exclusivity. Inside a competitor's live campaign (a hashtag like #PETLABCOBFCM): not now |
| **Topic share** | `posts_about_topic` of `posts_read` | 3+ of 20: the topic is part of who they are. 0: a one-off (or older than 20 posts) |
| **Audience country** | `audience: "countries"` (+1, TikTok) | Target country 50%+ for paid work; under 40% is a flag. ~100 followers sampled: Low confidence |
| **Brand fit from real posts** | the last 10 captions and the bio | Who they are (persona), what they promise, tone, what they'd never say. Anti-category posts ("creatine gummies are a sc@m", whatmojoloves) and DIY recipes that teach people to skip buying (thecarefreecoop) are fit risks |

### Fake-follower and fake-creator signals (cheap, before the +60 report)

- **Huge followers, tiny views** (views per follower under 0.02): bought, aged or dead audience.
- **Under 2,000 followers with 100k+ views on one product video**: a TikTok Shop push or a clip account, not an audience (seim42857, 1,203 followers, 505,196 views).
- **The same caption template across accounts** (three creatine accounts shared "#SummerVibes … #EnergyBoost" and "Blueberry Creatine Gummies"): one brand's seeded or clip network.
- **"Our", "link in our bio", the same story reposted**: a brand-run persona account (oliviabrooks_skincare sells Luminence; miya42631 "Dog paw Daily" names ShineMint in every post).
- **Engagement far above or below the account's norm on one post**, comments that don't fit the audience: ask for the full report.
- **A bio that says they're building their own brand** ("Building @shelterskin", catherine.lockhart): a future competitor, not a partner.
- Only the full report (+60) gives follower credibility, ages and gender. Offer it for 1 or 2 finalists, never in bulk.

## 4. Tiers and what each costs to work with

Followers say little about results, but they set the deal (Corey's tiers and starting ranges, negotiation anchors only):

| Tier | Followers | Typical deal | Starting range per post (Corey, not Adgence data) |
|---|---|---|---|
| Nano | 1k to 10k | Gifting, product for content | Free product to $100 |
| Micro | 10k to 50k | Paid or hybrid | $100 to $1,500 |
| Mid | 50k to 500k | Paid, rate card | $1,500 to $10,000 |
| Macro | 500k+ | Agent | $10,000+ |

Plan on **cost per 1,000 median views** (fee ÷ median views × 1,000), never cost per follower. Rates are MISSING until the creator answers.

## 5. Shortlist: five verdicts

Give every creator one of these, with the reason in one line (FastMoss: "a list without a verdict is the root cause of 'any more?'"):

| Verdict | Rule (H) |
|---|---|
| **Reach out now** | An evidence post on the topic (organic or for a non-competitor), views per follower 0.2+, engagement normal, target country plausible, no current competitor lock |
| **Seed first** | Fits the persona, healthy numbers, hasn't posted about it yet (lookalikes, taggers of other brands): send product, no ask |
| **Commission only** | TikTok Shop affiliates and deal accounts: high output, product-hopping, low engagement. Open-collab or commission, never a flat fee |
| **UGC for hire** | Good content, weak reach (bio says "UGC"): pay for videos you run as ads, not for their audience |
| **Skip** | Brands, makers and persona accounts (competitors); dead reach; anti-category; inside a competitor's live campaign; audience mostly outside the market |

Rank inside each verdict by **evidence × fit × median views × contactability**. Keep the persona mix varied: Meta clusters near-identical creator ads (Corey's paid-ads creator program), so five creators of one type buy one test, not five.

## 6. Outreach readiness

A creator is ready to contact when the shortlist row has:
1. **The evidence post** to mention (their own words about the category).
2. **The why-them line** (persona, audience, numbers).
3. **The offer for their verdict:** product (Seed), commission and a code (Commission), fee plus usage rights (Reach out), per-video fee (UGC).
4. **A contact path:** the bio email is free with `include_contact`; otherwise +5 only when one is found. Ask first; run it only for those the user will actually email.
5. **The asks to put in writing:** usage rights for ads (whitelisting, Meta Partnership Ads access, TikTok Spark code) for a set window, exclusivity if the category is tight, the disclosure (spoken and on screen near the start, plus the platform's paid-partnership label: copy-and-scripts §5).
6. **The tracking:** one code and one link per creator, set before they post.

Outreach copy (FastMoss's five parts): one specific recognition of their post, who you are in one line, why them, the terms, a one-line reply CTA. A TikTok DM of 50 to 80 words and an email of 120 to 180. Drafts only: never sent from here.

## 7. Decide, then plan

Every finding ends in a decision:

| Finding | Decision |
|---|---|
| The competitor pays one persona heavily | Don't fight there; take the persona it skips |
| The competitor's sellers share one message that keeps working (Native Pet: 12 TikTok Shop sellers posted dog-breath videos in Jan to Mar 2026, 8 over 400,000 views, at least 6 tying breath to the gut) | Borrow the mechanism (one symptom traced to the cause), never their claim or wording |
| Most "creators" in a niche are makers or brands | Source from competitors' taggers and lookalikes instead of topic search |
| Category sells through TikTok Shop affiliates | Run an affiliate open-collab and seed, don't pay flat fees |
| A creator's best post is a hit but the median is small | Pay on the median, or buy the hit as an ad (Spark code) |

Then a **first-wave plan**: 5 to 10 creators across 2 tiers and 2 to 3 personas, what each gets, the success measure (cost per purchase from their code; for UGC, the ad's CPA), and when to re-check (a month).

## 8. The insight quality bar

Every claim is traced to a tool result with its base ("median of 20 recent posts", "102 followers sampled"). Cut: lists without verdicts, follower counts as quality, "high engagement" without the number, brand accounts presented as creators, a creator without an evidence post presented as a fit, invented rates.
