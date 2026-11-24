# The search-term method

How a senior PPC buyer runs the search-term pass on a Google Ads account. Sources: Google Ads Help (match types, negative keywords, close variants, search terms report, Performance Max), the practitioner routines in the research (hireotto's two-stage prompt, Vicky Lalwani's 2-3x target CPL rule and cadence, kaancata's "boring" weekly routine on r/PPC, the r/PPC CRM warning), Adgence's my-ads notes (the "spent 2-3x target with no conversion" pause rule, small-sample rules), and the best existing skills, read in full: eliasmalmsandberg google-ads-search-term-mining and google-ads-negative-keywords, Corey Haines's ads skill (reading-google-ads-data, google-search-playbook, audit-guardrails), claude-ads ads-google and its GAQL notes, irina search-term-mining and wasted-spend-finder, and Helium 10's add / negate / watch. Thresholds marked (H) are heuristics: use them, say they are, and prefer the account's own numbers over any industry figure.

## 1. What the data is, and what it can't say

- **A search term is what someone typed.** A keyword is what the advertiser bid on. With broad and phrase match (and close variants of exact), one keyword pulls in many searches. The report shows each search with the keyword it matched and its clicks, cost and conversions in the date range.
- **The report is read whole, but it's still a sample of the searches.** `get_my_search_terms` pages through the whole report (up to 10 pages; `capped` says when there's more), so its `totals` are the report's own. But Google hides searches made by too few people. On small accounts half or more of the clicks can have no disclosed search term (Corey Haines's reading-google-ads-data), and the visible ones lean toward common searches. Always compare the report's clicks and cost with the campaigns' for the same dates (`get_my_ads`) and state the coverage as "the report shows {clicks} of {campaign clicks} clicks and {cost} of {campaign cost} spent". Never call the visible waste "all the waste", and never call the hidden part clean.
- **Conversions are what the account counts.** The column counts actions marked primary (plus any a campaign goal pulls in); one campaign can mix a page view, a signup and a sale. Read the conversion actions with `check_my_ad_account` (name, category, primary or not) before trusting a CPA. If the conversion action is a form fill, a "conversion" may be junk. Lead quality, closed deals and refunds live in the CRM: MISSING unless the user gives them. The r/PPC warning: a term can "convert" in Google Ads while those leads never close. Ask once, in the closing question.
- **Conversion lag.** Conversions are credited to the day of the click, and they arrive late (days for ecommerce, weeks for B2B). The last few days always look worse than they are. Default window: the last 30 complete days ending 3 days before yesterday (H): `days: 30, exclude_last_days: 3`. For lead gen with a sales cycle, or when the user says leads take longer, end the window 7 to 14 days ago, or read the lag (§9).
- **Performance Max and Shopping are different.** See §8. `get_my_search_terms` is built for Search ad groups; never assume a row came from a Search campaign without the campaign type.

## 2. The numbers that drive every decision

From the user (ask once, or read the brand note):
- **Target CPA or CPL** (cost per conversion they can afford). For ecommerce with conversion value, a **target ROAS** works too: the cost at which a term is "too expensive" is then value-based.

From the account (compute, never assume):
- **Account CVR** for the window: conversions ÷ clicks, from the report rows or `get_my_ads`. Use the campaign's own CVR when campaigns differ a lot.
- **Expected conversions** for a term = its clicks × the CVR.

## 3. The four queues

Every row with spend lands in exactly one queue, with its reason.

### Negate (block the search)

A term goes here when **either**:
1. **Spend rule (H):** cost ≥ 2x target CPA with 0 conversions (3x when the account's CVR is low or the term is close to the product), **and** enough clicks that zero isn't luck: clicks ≥ 3 ÷ CVR. (The "rule of three": with a 5% CVR, 60 clicks with no conversion leaves about a 5% chance a normal term would show none. The chance of zero by luck is (1 − CVR)^clicks; quote it.)
2. **Intent rule:** the searcher plainly wants something the user doesn't sell, at any spend (see §4). Intent negatives don't need a sample; they need a careful match type.

A term that converts but at far above target (CPA > 2x target with 3+ conversions) is not a negative: it's a bid or match-type problem. Put it in Watch with "bid down or tighten match", never block a converting search.

### Add (make it a keyword)

A term converts at or under target with **2+ conversions (H)** and isn't already a keyword (check `get_my_keywords` and the row's status, §6). (eliasmalmsandberg adds any term with one conversion; Corey Haines's guardrail is right that one conversion on one click is noise.) Add it as **exact match** to the ad group that already wins it, so it gets its own bid, ad relevance and Quality Score. One conversion is a lead, not a pattern: Watch.

Size the adds with Keyword Planner (`get_my_keyword_ideas`): monthly searches tell you whether it's worth its own keyword (very low volume exact keywords can go "low search volume" and stop serving); the top-of-page bid range tells you what it costs to show.

### Watch (decide later)

Everything with spend that doesn't qualify: under the sample, research intent that sometimes converts ("vs", "reviews", "is X a scam"), 1 conversion, converting above target. Each watch row says **what would move it**: "negate if it reaches 2x target with no conversion", "add if it gets a second conversion".

### Leave (working)

Converting at or under target and already a keyword. One line in total, not a list.

## 4. Intent classes

Read each term for what the searcher wants. These are the classes practitioners negate on sight (Vicky Lalwani: "jobs, free, login, DIY") and the ones that need judgement:

| Class | Examples | Default |
|---|---|---|
| Jobs and careers | jobs, careers, hiring, salary, internship | Negate (phrase) |
| Free and cheap seekers | free, free sample, torrent, pdf download, cheap (when the user is premium) | Negate the phrase, never the single word "free" when "free shipping" or "free trial" could be the user's own offer |
| Do it yourself | how to make, recipe, DIY, homemade, template | Negate, unless the user sells the kit, course or template |
| Existing customers and support | login, sign in, customer service, phone number, cancel, refund, tracking | Negate in prospecting campaigns; never in a brand campaign without asking |
| Wrong product or meaning | another product with the same word, a different industry | Negate the word that makes it wrong |
| Wrong place | a city or country the user doesn't serve; "near me" for an online-only seller | Negate (or fix location targeting, which is the real cause) |
| Education and research | what is, meaning, side effects, vs, reviews, is it a scam, best | **Judge by numbers**, not on sight. Research terms often convert later; negate only on the spend rule |
| Competitor brands | another company's brand name | **The user's call.** Buying competitor names can be a strategy. Show the cost and conversions; if they don't want them, negate in non-competitor campaigns |
| The user's own brand | brand name, brand + product, brand + reviews | **Never negate** (see §7) |

**No negatives from imagination.** Starter lists ("free", "reviews", "scam", "how to") are how accounts block their own buyers: a starter "reviews" negative blocks "[brand] reviews". claude-ads and Corey Haines's guardrails both forbid proposing negatives without the search terms report and an overblocking review; this skill proposes only negatives that a row in this account's report supports, and checks each against every converting search.

**N-grams.** Waste hides in words, not whole searches. Split every term into single words and two-word phrases, add up cost, clicks and conversions per word across all terms, and look for words with real spend and 0 conversions spread over many small terms (for example one word spread over a dozen small searches, none converting). One phrase negative on the word replaces a dozen exact negatives. Only propose a word negative after checking every converting term that contains the word.

## 5. Match types for negatives

Negatives behave differently from keywords (Google Ads Help):

| Negative type | Blocks | Use when |
|---|---|---|
| Exact `[term]` | Only that exact search | One bad search; safest; default in `add_my_keywords` |
| Phrase `"term"` | Any search containing those words in that order | A bad phrase or word: "free sample", "recipe", "jobs" |
| Broad `term` | Any search containing **all** the words, any order | Rarely; a single broad word negative blocks a lot. Negative broad "free trial" does not block "free" alone (Corey Haines's match-type gotcha) |

- **Negatives don't match close variants.** A negative for "recipe" doesn't block "recipes"; add the plural and common misspelling yourself.
- **Negatives have a 10-word and 80-character limit** per keyword.
- **Conflicts:** a negative that matches one of the user's own keywords stops that keyword serving. Check every proposed negative against the keyword list before proposing it.
- **Adding keywords:** `add_my_keywords` now defaults keywords and negatives to **exact** when no match type is sent (a harvested search should match itself). Send `match_type` explicitly anyway, so the preview shows the choice: exact for harvested search terms, phrase for a bad word. (`create_my_campaign`'s `search_ad` defaults the same way: keywords and negatives to exact.)

## 6. Where a negative lives: ad group, campaign, shared list, account

| Level | Reach | Through Adgence? |
|---|---|---|
| **Ad group negative** | That ad group only | **Yes:** `add_my_keywords` with `negative: true`, one ad group per call |
| **Campaign negative** | Every ad group in one campaign | No: the user adds it in Google Ads (Keywords, Negative keywords, Campaign), or the skill repeats it per ad group, which is slower and easy to miss when ad groups are added later |
| **Shared negative list** | Every campaign the list is attached to | No: Google Ads, Tools, Shared library, Negative keyword lists. The right home for intent negatives (jobs, free, DIY) that apply everywhere |
| **Account-level negatives** | The whole account, including Performance Max | No: Google Ads account settings |

A shared list blocks nothing until it's attached to a campaign (claude-ads GAQL notes): when reading what's already blocked, count a list only for the campaigns it's attached to.

**Who owns which search** (eliasmalmsandberg's cross-campaign rule): brand > specific product > general category > competitor. When two campaigns or ad groups win the same search, the lower one gets the negative. The same keyword in two ad groups bids against itself.

**A list structure that stays readable** (adapted from eliasmalmsandberg): "Universal" (jobs, wrong industry: every campaign), "Research" (only for campaigns that should take buyers only), "Brand exclusions" (non-brand campaigns only, never the brand campaign), "Competitors" (non-competitor campaigns), plus campaign-only lists. The skill suggests the list each copy-ready block belongs in.

So the skill **writes ad group negatives itself** for problems specific to one ad group (a term matched by the wrong keyword, routing a search to the ad group that should win it), and **hands the user a copy-ready list** for campaign, shared-list and account negatives, saying where to paste it.

Before proposing any negative, read what's already blocked at every level so nothing is proposed twice: `get_my_keywords` with `negative: true` (ad group negatives, synced about daily: `synced_at`), and GAQL through `get_my_ad_breakdown` for campaign negatives and shared lists (queries in the SKILL). The row status in `get_my_search_terms` comes with the report itself and settles it: ADDED (already a keyword), EXCLUDED (already blocked), ADDED_EXCLUDED or NONE.

**Routing (sculpting).** When a converting search is matched by the wrong ad group (wrong ad, wrong landing page), add it as an exact keyword where it belongs and as an exact negative in the ad group that wrongly wins it. Both are ad group changes, so both can go through Adgence.

## 7. Never block the brand

- Never negate the user's brand name, or any word that appears in their brand searches, product names or offers. Before proposing a phrase or broad negative, test it against: the brand name, the product names in the keywords, and every converting search term. "reviews" as a negative blocks "[brand] reviews", which often converts best.
- Brand terms in a non-brand campaign: the usual move is a brand **negative in the non-brand campaign** so brand searches go to the cheaper brand campaign, but **only if a brand campaign exists and covers them**. Otherwise blocking brand searches loses the cheapest conversions in the account. Ask before proposing it.
- Brand campaigns aren't cleaned like prospecting: support and login searches in a brand campaign are customers; flag them, don't block them unasked.

## 8. Performance Max, Shopping, Display

- **Performance Max** has no keywords and no ad groups. Its search data comes as **search term insights** (search categories with clicks and conversions, no cost per category in the API) and, since 2025, a search terms report in Google Ads. Negatives for PMax go in **campaign negative keywords for PMax or account-level negatives, in Google Ads**; `add_my_keywords` can't reach them (it needs a Search ad group). Brand exclusions are a separate PMax setting.
- PMax often takes searches the user's Search keywords should win, including brand. Say so when PMax spend is large next to Search; the fix (brand exclusions, account negatives) is done in Google Ads.
- **Shopping** campaigns have no keywords; negatives are campaign or shared-list level: hand them to the user.
- **AI Max for Search** matches searches beyond the keyword list and can send clicks to other pages on the site. When a campaign uses it, read its searches and the landing pages it chose in this pass (Corey Haines's playbook); a buyer landing on a careers or blog page is a finding.
- **Display and video** have no search terms: not part of this pass.

## 9. Windows, cadence and small accounts

- **Default:** last 30 complete days, ending 3 days ago (H). Every 2 weeks (Vicky Lalwani's cadence; weekly for big accounts).
- **Small accounts** (under about 100 clicks in 30 days, or under about 10 conversions): widen to 60 or 90 days before judging, and say why. Intent negatives still apply.
- **Conversion lag:** when the user's conversions take longer, end the window later. GAQL can show the lag (`segments.conversion_lag_bucket` with `metrics.conversions` on the campaign resource); untested on a real account.
- **Broad match or AI Max on:** the search surface is much larger; run the pass weekly (eliasmalmsandberg).
- **Once a quarter:** check that old negatives aren't blocking searches the business now wants (new products, new audiences).
- **After a change:** judge the effect over the next full window, not the next day.

## 10. What "good" looks like

- **Waste found** = cost of the Negate queue, stated as an amount and as a share of the report's cost, and of the account's cost.
- **Projected effect (inferred, say so):** if the negated searches bring no conversions later, the account CPA over the same window would have been (cost − negated cost) ÷ conversions. Never promise it: blocked budget is usually spent on other searches.
- **Adds:** expected effect is control (own bid, own ad), not new volume; say it.

## 10b. Beyond the queues (Full depth)

The report shows more than waste (eliasmalmsandberg's mining framework):
- **Clusters with no home:** several converting or high-CTR searches sharing a modifier no keyword covers ("for women", a city, a use case) point to a new ad group with its own ad and page.
- **Buyers' words:** words repeated in converting searches belong in headlines (`create_my_campaign` can add an ad to the ad group, paused).
- **Page gaps:** a relevant search with a high click rate and a conversion rate far below the account's means the ad promises what the page doesn't deliver.

Report at most three of these, each with its rows.

## 11. The insight quality bar

Every row in a queue has a reason a buyer would sign. Cut: "consider adding negatives", lists without cost, single-word negatives without a brand and keyword check, negating research terms on sight, calling hidden spend clean, invented CRM outcomes. The verdict names an amount, a share and the two or three moves that matter most. The evidence ends with three labelled lists (Corey Haines's audit guardrails): **facts** (what the rows show, with the window), **inferences** (with confidence), **missing** (and how it could change the answer).
