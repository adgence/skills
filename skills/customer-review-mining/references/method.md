# The review mining method

How a senior customer researcher turns reviews and comments into counted, decision-ready customer language. Sources: Adgence's customer-research, competitor-analysis, copy-and-scripts and creative-strategy notes (Copyhackers, CXL message mining, SplitBase, JTBD forces, Schwartz, Sales Safari); Corey Haines's customer-research skill (confidence table, recency window, money quotes); Motion's audience-research skill (five buckets, existing versus growth audiences); ScrapeCreators' comment-mining (questions, objections, buying intent, exact language to reuse); boringmarketer's reviews skill (rivals' complaints turned into promises); Aazar Ali Shad ("1-stars are objection copy, 5-stars are transformation copy, 3-stars show the gap"); Michael Godzic via Solo Ad Lab (narrow questions, not one summary); Old Bones' review-language test; and three Adgence test runs on 23 Nov 2026 (beef tallow balm, period tracker apps, AG1). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data can and can't say

Reviews and comments tell you what people say and feel, in their words. They never tell you how many customers feel it, what converts, or what a competitor sells. Every count is "N of the M comments collected", never a share of the market.

### The corpus check: do it before any theme

Say what the corpus is before you count anything. It changes every conclusion.

| Check | How | Why it matters |
|---|---|---|
| Size per source and page | `by_source` and `pages` from `get_customer_comments` (each page's comments here and `on_page`, what it shows) | Under about 50 relevant comments per source, findings are directional (H). TikTok Shop listings show tens of thousands of reviews and about 300 are read: under 1%, so say the share |
| Buyers or browsers | Reviews (TikTok Shop, Amazon, Trustpilot, App Store, Google Play, Shopify) hold **buyers**. Reddit, YouTube, TikTok, forum and ad comments hold **browsers**: people deciding, doubting or arguing | The gap between the two is often the main finding (tallow: buyers happy, browsers afraid of clogged pores) |
| Rating spread, per page | `max_rating: 3` and read `matching`; the page key on each comment | Reviews are J-shaped. TikTok Shop tallow: 42 of 1,051 at 3 stars or lower, all 42 on 2 of the 4 listings (24 Lopeie, 18 FORGE SKIN), none in the 451 read from the other two. Trustpilot AG1: 17 of 198. Under about 30 low-rated reviews, objections from ratings alone are thin: add browser sources. A listing with no low ratings in the set read may simply not have served them: say so, never "no complaints" |
| Dates | Dates on the page you read; `since` to filter | Google Play's set ran 2018 to 2026; App Store's was all recent. A Reddit thread can be 3 years old. Weight the last 12 months (Corey Haines's recency window) and say the span |
| Topic match | `name_the_topic`, `on_pages_about_it` and each page's `about_topic` (topic collections) | Comments off the product (creator chatter, jokes, other products) dilute counts. A review page with `about_topic` false is another product (a creatine powder in a "creatine gummies" set): leave it out with `pages` |
| Market and language | Subreddit names (`find_conversations` with `country` drops most other-country communities, not all); `language` on each review | Other-country subreddits and Spanish reviews on US TikTok Shop show up (tallow: 716 of 1,051 read as English). English terms miss other languages: count with `language: "en"` and say so, or add the other language's words |
| Who was invited | Trustpilot and brand sites invite reviews; short "great product and service" lines | Invited reviews skew positive and short; don't read their praise share as satisfaction |

### What the fields really are

- **`likes`:** Reddit upvotes, YouTube and Google Play helpful votes. **null** on App Store and TikTok Shop reviews; mostly 0 on Trustpilot. So "most-liked" only ranks within a source that has likes; compare likes within a source, never across.
- **`rating`:** present on rated sources only. `max_rating` and `min_rating` drop every unrated comment, so on Reddit, YouTube or TikTok they return nothing.
- **Default sort is "relevant"** (names the topic, then newer and better liked). Use `sort: "likes"` for the community's consensus wording and `sort: "recent"` to check freshness.
- **`count_mentions` counts one id or several (`collection_ids`), each comment once.** A topic collection counts as one; pages collected one by one with `collect_comments` each have their own id. Count buyers and browsers in separate calls: their gap is the finding. Pages of the same kind (three threads, two listings) can go together.
- **Both readers share filters:** `source`, `pages` (keys p1, p2... or scrape ids), `min_rating`, `max_rating`, `since`, `until`, `language`, `min_likes`. So a theme inside low ratings is `count_mentions` with `max_rating: 3`; the last 12 months is `since`. Every theme returns `by_page`: which listing or thread it sits on.
- **`page` and `pages`:** every comment carries its page key; `get_customer_comments` lists the pages (title, link, comments here, `on_page`, `about_topic`, `stopped` when fewer were read than the page shows, and why).
- **Whole-word matching.** A term matches its plural; `refund*` matches word starts; straight and curly apostrophes match each other ("can't" finds "can’t"). It can't read direction: "not greasy" counts as "greasy", "less breakouts" counts as "breakouts", "no intrusive ads" counts as "ads", "restore my data" counts as a privacy worry. See §3.
- **TikTok Shop review prompts.** Buyers answer TikTok's prompts ("Effectiveness:", "Absorption:", "Hydration Effect:", "Packaging:"), so those words are inflated. Don't count a prompt word as a theme.
- **Amazon** returns at most 100 reviews for 2 credits and can fall back to Amazon's own summary snippets. Those comments carry `summary_snippet` (topic, mentions, positive, negative): report them as Amazon's summary, never as reviews, and keep them out of counts.
- **`language`** is a best guess on review sources (TikTok Shop, Amazon, app stores, Trustpilot); short lines may have none.
- **A collection's cost** comes back on `get_customer_comments` (`credits_charged`) once it's done: pages are charged as collected (about 1 each, YouTube and app stores free), pages collected before are free.

## 2. Read before you count (clip, then tag)

Read in this order, in pages of 50 (`limit: 50`), per source:
1. **Low ratings** (`max_rating: 3`) on rated sources: broken promises, deal-breakers, billing anger. 2 to 4 stars are the most candid.
2. **Most-liked** (`sort: "likes"`) on sources with likes: the community's consensus wording.
3. **Long 4 and 5 star reviews:** outcomes, before and after, surprise benefits, why they bought.
4. **Seeds** with `contains`: "tired of", "finally", "switched", "worth it", "scam", "refund", "cancel", "gave me", "stopped taking", "broke me out", "wish".

Clip verbatim, then tag each clip (customer-research.md §4), with five buckets that each feed a different decision (Motion's audience-research):
- **Pain** (the problem before) → the hook and the problem beat.
- **Trigger** (what finally made them buy) → hooks and targeting moments.
- **Objection** (what almost stopped them; in positive reviews it's past tense: "I was skeptical but...") → the body, FAQ and guarantee.
- **Transformation** (what changed, in their words) → proof and outcome lines.
- **Standout language** (lines that work as a hook with no edit) → the language bank.

Also tag: product failure versus experience failure (shipping, damage, billing, support), JTBD force (push, pull, anxiety, habit), awareness level, identity language ("as a nurse", "at 54"), and the alternative they compare against ("just make a smoothie", "pen and paper", "the butcher").

Don't count while reading. Count afterwards.

## 3. Count it (and count it right)

1. List 8 to 15 candidate themes from your clips.
2. Give each 3 or more phrasings in the customer's words, including misspellings and other languages present.
3. **Two-sided themes are split** into helped and caused, or praise and complaint, with direction phrases. Examples from the tests:
   - "greasy" matched 160 of 1,051 tallow reviews, mostly "not greasy". Complaint phrasing within low ratings: 4.
   - "symptoms" matched 110 period-app reviews, nearly all praise for tracking symptoms, not complaints about health scares.
   - "ads" matched 84 app reviews, including "no intrusive ads".
4. **Read the examples `count_mentions` returns for every theme.** If the top examples point the wrong way, rewrite the terms and count again. Report the corrected count only.
5. **Count inside ratings** with `count_mentions` and `max_rating: 3` (complaint words among 1 to 3 star reviews) or `min_rating: 4` (outcomes among fans); for one theme, `get_customer_comments` with `contains` and the same filter gives `matching`. Tallow: breakout or irritation words appear in 6 of the 42 low ratings, 4 of them on one listing.
6. Drop terms in `unmatched_terms` and terms that match the wrong thing (`matched_terms` shows each term's count).
7. **Quote the theme's `comments` number, never a sum of `matched_terms`:** terms overlap ("goes a long way" 58 already includes "a little goes a long way" 37).
8. **Leave out voices that aren't customers:** the brand's own replies in a thread, rival brands plugging themselves ("check us out", "there is Core7"), founders plugging the app they're building ("my husband is an app developer", "I can add you as an alpha tester": 3 of 44 comments in a 2026 period-app thread, and 2 of them inflated the privacy count), affiliates, bots. Read the examples to spot them and subtract them.
9. Every two-sided count is a floor; every keyword count is approximate. Say so once.

**Hand counts:** in a small set you read in full (say the 17 low-rated reviews), you may count by hand. Label it "counted by hand from the 17 read".

## 4. Rank: what matters

Rank by **frequency** (distinct comments), then **intensity** (how charged, 1 to 5: long, emotional, specific, "never again"), then **uniqueness** (does a competitor already own it?).
- Frequency decides the body copy and the order objections are answered.
- Intensity decides hooks: a theme in 5% at intensity 5 can beat one in 30% at intensity 2.
- Likes count logarithmically (H): one viral comment never outweighs a pattern. One thread is capped: it can't make a theme High alone.

**Confidence** (combines the expert notes and Corey Haines's table):
| Level | Rule (H) |
|---|---|
| High | 20+ comments, 3+ distinct pages, at least 2 source types (or buyers and browsers both) |
| Medium | 8 to 20 comments, or 2 pages, or one source type |
| Low | Under 8, or one page; a theme with 1 or 2 mentions is "anecdotal" |

**Saturation:** stop collecting when a new page adds under 5% new themes. One more page of the same source rarely changes the ranking; a different source type often does.

## 5. Diagnose

- **Buyers versus browsers:** what buyers praise (proof you can use) against what browsers doubt (objections the ad must answer before the click). When they disagree, the browsers' doubt is the ad's job and the buyers' words are its proof. Period apps, Nov 2026: privacy is almost absent from 471 recent app reviews (the "my data" matches were people praising backups) but is the top theme among people choosing an app (8 of 44, the two most-liked comments). Reviews alone would have missed it.
- **One seller or the category:** `by_page` shows whether a complaint is spread across listings or sits on one. Tallow: 13 of the 16 order and packaging complaints in low ratings are on one cheap listing. That's a promise the user can beat with visible packaging, not a category truth.
- **The alternative:** what people compare against ("make a smoothie", "pen and paper", "the butcher's tallow"). That's the real competitor in the ad.
- **Awareness mix** per source: TikTok comments skew problem-aware; app stores and Amazon product-aware. It decides how the ad opens (creative-strategy.md).
- **Sophistication:** skepticism markers ("does this actually work", "scam", "placebo", "just eat vegetables") in roughly 10 to 20% of browser comments suggest a stage 4 to 5 market (H): lead with mechanism and proof, or identity, not bigger claims.
- **Product versus experience failures:** damaged jars, auto-renew refunds and slow couriers belong to operations, not to ads. Report them apart.
- **Competitor reviews (rival mode):** their 1 to 3 stars are their unmet needs; their 4 and 5 stars are the category's table stakes (hard to differentiate on). What everyone praises is not a promise.

## 6. Decide, then plan

| Decision | When | Example from the tests |
|---|---|---|
| **Lead with it** | A pull or pain with High confidence and high intensity that no competitor owns | Period apps: "just let me track my period" without pop-ups |
| **Pre-empt it** | The top browser objection, even if buyers rarely raise it | Tallow: clogged pores, answered with "just tallow and jojoba" |
| **Prove it** | A transformation buyers describe often, in their words | Tallow: "a little goes a long way", soft skin |
| **Promise against them** | A rival's repeated complaint the user can truly beat (ask them) | AG1: Stevia taste and auto-renew refunds |
| **Fix, don't advertise** | Experience failures (shipping, damage, billing) | Melted or opened jars on TikTok Shop |
| **Avoid** | Health, weight, diagnosis or guaranteed-result claims, even when customers say them | "Clears acne", "heals eczema", "detects PCOS" |

Then the plan: 3 angles ranked by evidence × intensity × gap × ease, each with the verbatim quote it rests on, 3 hooks (visual, on-screen text, spoken) built from customer words, the objection it must answer, and the success measure (purchases or CPA for a new angle; hook rate for a new hook; add-to-cart is not enough: Old Bones' review-language page lifted add-to-carts 47% with no more purchases). Plus: 3 to 5 FAQ lines for the landing page, the words to reuse and the words to avoid.

## 7. Outputs (pick what the question needs)

- **The corpus card:** sources, counts, buyers or browsers, rated share, date span, what wasn't read.
- **Theme table:** theme · bucket · people (count, base) · sources · likes (where they exist) · intensity · confidence · 2 or 3 verbatim quotes with links.
- **Buyers versus browsers** split when both were read.
- **Objection map:** objection · count · where it bites (pre-click, pre-purchase, post-purchase) · verbatim · the counter-evidence buyers give · the asset needed.
- **Language bank:** 15 to 30 verbatim phrases tagged hook, pain, outcome, callout or objection-answer. Never paraphrased.
- **Words to avoid:** claims that would break ad policy, and words customers find off-putting.
- **For rival mode:** a claims-versus-complaints line per competitor.

## 8. Pitfalls

- Self-selection: reviewers have strong feelings; invited reviews skew positive.
- Loud minority: one thread or one viral comment can't make a theme.
- Fake and incentivised reviews: bursts on one date, near-duplicates, "received free".
- Sarcasm and negation: never judge direction from keywords; read the examples.
- Context drift: Reddit replies argue with each other (time is money, banana sugar); YouTube comments talk about the creator.
- Over-polishing: the value is in the verbatim.
- Confirmation bias: report themes that contradict the brief.

## 9. Cost (from the code, 23 Nov 2026)

- `collect_customer_comments`: each page is charged at its price as collected: Reddit, Trustpilot, TikTok, TikTok Shop and most pages 1; YouTube, App Store, Google Play and Steam 0. Pages per source: one source alone reads 5 Reddit threads, 5 YouTube videos or 4 pages of any other; two sources read Reddit 5, YouTube 2, others 2 each; three or more read Reddit 3, YouTube 2, others 2 each, about 12 pages at most. The start result says `pages_planned` and `max_credits`; `get_customer_comments` says what it cost. The same topic, sources and country collected again within 7 days is free (`reused: true`). Realistic runs: 0 (app stores) to about 12.
- `collect_comments`: one page, 0 (YouTube, app stores, Shopify product pages), 1 (most), 2 (Amazon, at most 100 reviews; `credits_pending` while it runs). A page already collected in the workspace is reopened free (`reused`).
- `find_conversations`: 1 when pages are found, printed as `credits_charged`; the same search again soon is reused free.
- `find_forum_threads`: 1 (+1 with Blind), free when nothing is found.
- Reading and counting: free. `track` a page, keyword or subreddit: 1 per check.

## 10. The insight quality bar

Every finding is counted with its base, quoted verbatim, labelled buyers or browsers, and tied to a decision. Cut: "customers love the product" as an insight; themes that don't change what the user writes, fixes or tests; a summary paragraph ("a mushy paragraph you cannot use"); counts whose examples point the wrong way.
