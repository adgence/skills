# The copy method

How a senior direct-response copywriter and creative strategist writes ad copy that can be proven: from counted customer words and competitors' long-running ads, never from the model's idea of the category. Sources: Adgence's copy-and-scripts, creative-strategy and customer-research notes (Copyhackers, CXL, Schwartz, Meta and TikTok guidance, FTC), Corey Haines's ad-creative skill (grounded inputs, AI tells, RSA rules, testable hypotheses), Motion's write-hooks skill (pain sources, the 5-test hook judge), Corey's hook system (three layers, no-duplication rule, hook matrix), claude-ads (no negatives without a search terms report), Kim Barrett's headline matrix and generic-language killer, ScrapeCreators' comment mining, practitioners (Pravatà, Godzic, Aazar Ali Shad, Old Bones, Anthropic's `/rsa`), and Adgence test runs on 23 Nov 2026 (sour creatine gummies on Meta, a freelancer invoicing app on Google, a new pickleball paddle brand on Meta and TikTok, and after the tool fixes a new walking pad brand on Meta and the invoicing RSA again). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. The rule that makes copy provable

Every line carries a **proof tag** and a **source**:

| Tag | Means | Source shown |
|---|---|---|
| **V** verbatim | The customer's words, unchanged or with filler cut (mark cuts with …) | Comment link, likes, page |
| **T** trimmed | A verbatim phrase shortened or re-ordered to fit a limit; meaning unchanged | Comment link |
| **W** written | Your joining words, built on a counted theme | Theme name and count ("price objection, 14 people, 3 pages") |
| **S** structure | A mechanic or format borrowed from a competitor's long-running ad (never its claim or wording) | Ad link and days live |
| **F** fact | A product fact the user gave (price, dose, guarantee, feature) | "You told me" or MISSING |

A line with no source isn't written. A line that needs a fact the user hasn't given carries a placeholder in capitals, `[YOUR DOSE PER SERVING]`, and is listed under MISSING. Never invent numbers, timeframes, outcomes, reviews, customers or personas.

Most AI ad copy fails on grounding, not on writing (Corey Haines). Adgence fixes the grounding by pulling it live; this method is what turns it into copy.

## 2. Is there enough to write? (the grounding check)

| Level | What you have (H) | What you do |
|---|---|---|
| **Ready** | 50+ relevant customer comments or reviews from 2+ different pages, and for Meta or TikTok 3+ relevant ads live 30+ days (or watched ads in the library) | Write the full set. Confidence per theme as in §4 |
| **Thin** | Under 50 relevant comments, or one page only, or no long-running ads | Write, mark every theme Low or Medium, and offer the top-up that fixes it (usually 1 to 2 credits) |
| **None** | No customer words about this product or category | Don't write. Offer the quick grounding (about 3 to 4 credits) and run it on a yes. Never fall back to ungrounded copy |

"Relevant" means about the product, its category or the problem, from people who'd buy it. Count after cleaning (§3).

**The cheapest grounding that works** (found in the tests):
- **DTC and apps:** one pre-purchase debate thread ("are X worth it", "X scam", "X vs Y") + one page of a competitor's reviews, an owners' thread ("X was a game changer") or the comments under its most-viewed ad or creator post + one niche ad search with `min_days_live` 30 and `detail: "copy"`. About 3 credits (the walking pad re-test: 4, 334 comments and 27 ads). Many DTC brands have no Trustpilot page (the pickleball test's came back empty): use the reviews of a best-selling Amazon listing instead (`find_products` marketplace amazon, then `collect_comments`, about 4). Debate threads hold non-buyers' objections; reviews hold buyers' outcomes; you want both.
- **SaaS and services:** the main competitor's review pages first, Trustpilot and G2 (switchers' reasons, in full sentences; 1 credit each), then a switching thread ("leaving X", "X raising prices again", "X alternative"). G2 can't be read. "Best tool for Y" threads on Reddit are full of vendors: use them last. Add the competitor's site traffic (1) only to size its brand demand. About 3 credits.
- **Already collected pages are free:** `collect_comments` on a link collected before reopens it at no charge. Notes saved by this skill and by customer-review-mining keep those links and ids.

## 3. Read the words like a researcher

- Read by likes first (`sort: likes`): a like is a free vote that the comment is right. Then low ratings on rated sources: 1 to 2 stars are objection copy, 4 to 5 stars are outcome copy, 3 stars are the gap (Aazar Ali Shad).
- **Clean before counting:** drop bots and moderator boilerplate, link-only replies, vendors plugging their own product ("I built", "full disclosure", "I work at"), giveaway and emoji-only lines. In the invoicing test, 21 of 36 Reddit comments were founders or staff pitching their own tools (18 disclosed, 3 link-only): they show what buyers ask for, never how buyers talk, and no line may be sourced to one.
- **Jokes are signals.** "Yall gonna love kidney stones" (73 likes) is an objection wearing a joke: it tells you what the ad must not trigger and what a skeptic thinks.
- **Know who's talking:** comments under an ad or creator post are viewers, mostly not buyers; Reddit threads skew skeptical and expert; Trustpilot skews to support experiences (in the invoicing test, 10 of 100 FreshBooks reviews praised human phone support). Say who the words come from.
- **Code each useful line:** pain, desire, objection, failed solution, trigger moment, comparison; JTBD force (push, pull, anxiety, habit); awareness stage; intensity 1 to 5; identity words ("as a contractor", "newbie").

## 4. Count, then rank the themes

- `count_mentions` counts several pages in one call (`collection_ids`, each comment once) and returns `by_page` for every theme. Keep that split in the message map: it tells you whether a theme lives on one page only (in the walking pad re-test, durability was 28 of 120 comments in the "worth it" debate and 5 of 214 in the owners' thread: an objection of people deciding, not a complaint of owners).
- Give 3+ phrasings per theme, then **spot-check the examples** and `matched_terms`: whole-word matching catches the wrong direction ("powder is tasteless" matched a "powder tastes bad" term set) and word starts catch the wrong word (`last*` matched "on sale last week"). Report the count after your spot-check, as a floor.
- Rank by **people** (distinct comments), then **intensity**, then **gap** (does a long-running competitor ad already say it?). Frequency decides the body copy and the order objections are answered; intensity decides hooks (a 5%-intensity-5 theme can beat a 30%-intensity-2 one).
- Confidence (H): **High** = 20+ people across 3+ pages. **Medium** = 8 to 20 people on 2+ pages, or 5+ reviewers on one review page (many independent voices, one source: never above Medium). **Low** = under 8 people, or one discussion thread (people answer each other there, so one thread is one conversation, whatever its count).
- Weight likes logarithmically (H): one viral comment shouldn't decide the angle.

## 5. Map the market before writing

1. **Awareness** of the people you'll reach (Schwartz), from how they talk: symptoms only = unaware; names the problem = problem aware; names a category or mechanism = solution aware; names brands = product aware; asks for codes = most aware. The opening changes with it: story or startling fact → name the pain in their words → mechanism or difference → proof or offer → deal and CTA.
2. **Sophistication** from the competitors' ads: when every long-running ad already makes the same claim ("tastes like candy", "more power and spin"), that claim is table stakes. Stage 3 to 4 markets need a mechanism plus proof; stage 5 needs identity and experience. Skepticism words ("scam", "gimmick", "BS", "does this actually work") in roughly 10%+ of comments (H) mean lead with proof, not promises.
3. **The gap table:** for each theme, which competitor ads already use it (with days live). Themes customers raise that no long-running ad answers are where new copy earns its keep. Themes every competitor uses are entry fees: cover them in body copy, don't lead with them.
4. **Pick 3 to 5 angles** (one angle = one pain or desire × one persona). Score (H): evidence × gap × fit to the awareness stage × whether the user can prove it. Never pick an angle that needs a health, weight, income or guaranteed-result claim.

## 6. Write it

### Hooks (video)
Three layers written separately and never repeating each other (Corey's no-duplication rule): **visual** (what's on screen in the first second: mid-action, product in motion, a surprising prop), **on-screen text** (3 to 8 words, readable with sound off), **spoken line** (12 words or fewer, sounds like a person). Build each hook from a cell of the hook matrix: segment × motivation (verbatim source) × format. Ten hooks across ten cells beat thirty rewordings of one.

**The 5-test judge** (Motion; about a third of first drafts pass): it stops a stranger mid-scroll; you can name its trigger; it's a complete thought; a real person would say it; it has personal stakes. Discard failures and write a replacement from a different source, don't patch them.

Always fail: brand or product before tension; benefit with no pain; "5 reasons why" announcements as the spoken line; "Introducing", "Discover", "Stop scrolling"; commands without the why; generic POVs; demographic callouts with no emotion; in-group jargon for cold traffic (except when the user's buyers really are that in-group, as pickleball players are).

Proven structures to fill with verbatim: callout of the **situation** (never the person's attribute); a verbatim objection said back ("'Creatine gummies are a scam.' Fair."); contrarian; "I tried X so you don't have to"; old way vs new way; choice between two things the buyer really weighs; proof-first; confession; reply to a real comment.

### Primary text (Meta)
- The first 125 characters are all most people read in Feed, and about 40 in Reels: the hook must be inside them.
- Five primary texts = five different angles (pain, outcome, proof, objection, offer), never synonym swaps. Use the short form (under 150 characters) for proof and offer angles and the long form (a short story in the customer's words, still opening on the hook) for problem-aware cold traffic.
- Structure for long form: hook (verbatim pain) → the moment it bites → the product as the mechanism → proof (fact the user gave) → objection answered → offer and CTA.

### Headlines
- Meta: about 27 to 40 characters, below the creative; it complements the primary text, never repeats it.
- Static ad headline: 5 to 8 words, a complete thought, makes the image tense.

### TikTok ad text
1 to 100 characters, no emojis or # in non-Spark ads; Spark Ads use the creator's caption.

### Google responsive search ads
- 15 headlines of 30 characters or fewer, 4 descriptions of 90 or fewer, callouts of 25 or fewer. Display paths (2 × 15) are set in Google Ads; the Adgence create tool doesn't take them.
- Each headline must stand alone and make sense next to any other: Google combines them.
- Mix (H): 3 to 4 **keyword** headlines (the searched phrase, exactly), 3 to 4 **benefit** headlines in the customer's words, 2 to 3 **proof** or objection headlines (facts the user gave), 2 **offer or CTA** headlines that name the action, 1 **brand** headline. Descriptions add proof and answer objections; they never repeat a headline.
- Pin only when a line must show (a legal line, the brand): pinning cuts the combinations Google can test.
- **Competitor names:** using one as a keyword is a bidding choice; putting it in ad text can be restricted under Google's trademark policy. Keep competitor names out of headlines and descriptions unless the user has checked.
- **Keywords need volume:** `get_my_keyword_ideas` (Keyword Planner, needs a connected Google Ads account) gives volume, trend and bid range. Without it, list keyword themes from the customer words and mark volume MISSING. `get_website_traffic` shows a competitor's top 5 search keywords only, usually its own brand: useful to size branded demand, not to build a keyword list.
- **Negative keywords:** never suggest a negative list without the account's search terms report (`get_my_search_terms`) (claude-ads rule). Generic starter negatives overblock.

## 7. Kill the AI tells (check the whole batch)

Never write: contrast reveals ("It's not X, it's Y", "Not because X. Because Y."); negation lists ("No X, no Y, no Z"; one plain "No card required" is fine); trailing pile-ons; self-answered questions and colon reveals ("The result? 3x faster."); stock phrases ("Say goodbye to", "Unlock", "Take it to the next level", "Game-changer", "Elevate"); em dashes; hook slop ("Stop scrolling", generic "POV:"); CTA slop ("Get started today", "Join thousands"); vague superlatives ("best", "leading") without a number. Vary sentence shapes: ten lines that all open the same way read as generated. Clear beats clever; human beats polished (Kim Barrett).

## 8. Compliance (every publishable line)

- **Personal attributes (Meta):** describe the situation or product, never assert the viewer's health, body, age, finances or identity. "You" is fine when it isn't tied to an attribute.
- **Health and supplements:** no cure, disease or guaranteed-result claims; no "safe for your kidneys" or other safety claims without substantiation; performance claims need evidence (FTC: generally controlled human trials; testimonials don't substitute). Dose and third-party test claims only with the user's own document.
- **Testimonials:** only real customers' words, attributed, with permission for ads. A Reddit or competitor's-review comment can inspire a line (tag V or T) but is never presented as your customer's review, and other brands' names in a quote stay out.
- **Comparative claims:** never say a named competitor is worse without proof the user holds; "most gummies are underdosed" is a claim about others: frame it as the viewer's doubt ("'Gummies are a scam.' Fair.") and answer with your own proof.
- **Income and SaaS ROI:** no guaranteed revenue; time or money saved needs the user's case study.
- **Paid creators:** disclosure on screen and spoken, near the start.

## 9. Plan the test

- Test order inside a concept: hook → body → CTA and offer → primary text and headline. Change one element per variant.
- **Meta:** load 2 to 5 primary texts and up to 5 headlines as one ad's text options (Meta rotates them), or 2 to 10 versions in one ad set to read each separately. Hook tests: same body, 3 to 5 hooks. 3-2-2 for an established angle: 3 creatives × 2 primary texts × 2 headlines.
- **Measure:** a new hook on hook rate (3-second views ÷ impressions) and hold rate; a new angle or concept on purchases or CPA (signups or demos for software). Write hypotheses that can lose: metric, comparison, expected direction ("beats the taste hook on hook rate among cold traffic"). Old Bones' review-language test lifted add-to-carts 47% with no more purchases: judge copy on sales.
- **Name** every ad `Concept_Angle_Hook#_Body#_CTA#` so results map back.
- Ads that look alike (same person, same opening) are treated by Meta as one ad: new concepts need a new angle, persona or visual opening, not new words on the same footage.

## 10. What the data can't say

- Competitors' spend, results and targeting: MISSING. Run time means they keep paying for an ad, not that it's profitable.
- `find_competitor_ads` keyword searches can include advertisers outside the niche. When the AI check can't run, a word check runs instead (`relevance_note`) and names the advertisers it couldn't confirm (`unconfirmed_advertisers`): real competitors sit in that list next to strangers (a vascular clinic and a clinical study for "walking pad", a cruise line for "pickleball paddle"), so drop by hand only the ones that don't sell the product. Catalog ads (`catalog`) have no written creative: skip them, unless a full written body is there. `other_market` can misfire on English copy: read before dropping.
- Comment counts are floors from word matching; shares are of the comments collected on those pages, not of the market.
- A thread's comment count can exceed what `collect_comments` returns: `comments_on_page` and `stopped` say why (Reddit counts deleted and hidden replies). Report what was read ("122 of the 144 the thread shows").
- `find_conversations` mixes countries unless you pass `country` (the pickleball search returned mostly r/PickleballPhilippines; with `country: "US"` the walking pad search left out 4 such pages). A community not named for a country can still be anyone: say where the speaker is when the comment does (one top walking pad comment was from Scotland).
- `account` `since` counts every charge under the user's login: other chats or agents running at the same time land in `by_you` too. The run's own spend is the sum of `credits_charged`.
- G2 refuses automated reading (nothing is charged): for SaaS use Trustpilot, app stores, Capterra-style threads and switching threads ("leaving X", "X raising prices"), which in the invoicing test held the clearest reasons to switch.
- Similarweb keywords and volumes are estimates.

## 11. The quality bar

Every line: sourced, specific to this product, sayable by a person, inside its character limit, compliant. Every theme: counted with its base. Every recommendation: tied to a test with a measure. Cut: lines any competitor could run unchanged; claims the user can't prove; detail dumps. If a line doesn't survive the self-check, replace it rather than soften it.
