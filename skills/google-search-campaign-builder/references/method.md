# The Search campaign method

How a senior paid-search buyer plans a first Google Search campaign from what competitors run. Sources: Corey Haines's `ads` skill and its Google Search playbook and RSA output spec (coreyhaines31/marketingskills), AgriciDaniel/claude-ads (`ads-google`, budget-allocation), irinabuht12-oss/marketing-skills (bid strategy, search-term mining, copy variants), practitioner search-term workflows from the research (hireotto, Vicky Lalwani, kaancata on r/PPC), Adgence's copy-and-scripts and my-ads notes, the Adgence code, and two Adgence test runs (sour creatine gummies vs Create and Crave; a beauty booking app vs GlossGenius and Vagaro; 23 Nov 2026). Thresholds marked (H) are heuristics: use them and say they are.

## 1. What the data can and can't say

**Competitors' Google ads** (`find_competitor_ads`, platform `google`, by domain or by name): every ad shown in the last 30 days, with format (text = Search, image = Display, video = YouTube), first shown date and `days_live`, the advertiser and a link. `live_ads_total` is Google's own estimate: say "about".
- **`days_live` is first shown to last shown,** not "still running today". Each ad now carries `last_shown` and `still_showing` (false = Google last showed it over a week ago): count still-showing ads as running, and the rest as stopped inside the 30-day window.
- **By domain, only the brand's own ads are listed.** Ads from other advertisers pointing at the domain (resellers, brand-bidders, a platform's customers) come apart in `other_advertisers_on_domain`, and `google_note` gives their total and Google's estimate for the whole domain (verified 23 Nov: glossgenius.com, US: 2 GlossGenius, Inc. ads, 118 ads from salons using it, about 6,000 for the domain). Read by the brand's name when its own ads are few.
- **A domain search returns every advertiser whose ads link to that domain.** For a shop that can mean resellers or brand-bidders (17 other advertisers linked to trycreate.co); for a booking or website platform it means the platform's customers (the first 40 ads for glossgenius.com were all salons). Count the brand's own advertiser apart. If it isn't in the result, search by the brand's name (another credit) and say why.
- **Copy is readable for 1 credit an ad.** `get_ad_media` on a Google ad returns a picture of the rendered ad: headline, description, display path, sitelinks, price and rating extensions. It is one combination Google showed, not the full set of headlines they wrote. Keywords, bids, match types, budgets, spend and results are never visible: MISSING.
- **Landing paths name their ad groups.** /esthetician/software, /salon/software, /switchtoday, /creatine/gummies: each is an ad group or a campaign. Same-day launch batches (in `started`) are rebuilds or tests.

**Similarweb** (`get_website_traffic`): visits per month, channel shares (search_paid is the paid search share), and **5 top search keywords per site** with volume and CPC.
- Top keywords mix organic and paid and are mostly brand terms. They are never "the keywords they bid on". Use them for: CPC anchors, the size of a competitor's brand demand, and spotting off-audience traffic.
- **Two-sided sites** (marketplaces, booking platforms, apps with consumers and businesses): their keywords are their consumers' searches ("hair salon near me", "vagaro login"). For a campaign aimed at the business side, those are negatives.
- Paid search visits = visits × search_paid %, an estimate. Small sites (`small_site`) are the least precise.

**Keyword Planner** (`get_my_keyword_ideas`, free, needs a connected Google Ads account): average monthly searches, 12-month trend, competition, and the top-of-page bid range in the account's currency, from seeds and a URL. The only source of real volumes and bids. Without it, every volume except Similarweb's top keywords is MISSING.

**The user's own account** (when connected): `check_my_ad_account` (status, billing, auto-tagging, conversion actions, Google's recommendations, which "often want you to spend more"), `get_my_search_terms` and `get_my_keywords` if they already run Search.

## 2. The intent ladder (where the money goes first)

From Corey Haines's Google Search playbook, adapted:

1. **Brand** (the user's own name): cheap, always on, its own small budget. A new brand with no searches skips it.
2. **High-intent non-brand**: buying the product type ("creatine gummies", "esthetician booking software"). Most of the budget lives here.
3. **Problem or switch**: has the problem the product solves ("creatine without powder") or is leaving a rival ("vagaro alternative"). Good when the page answers it.
4. **Comparison**: "best X", "X vs Y", "X review". Higher CPC, needs a page that compares honestly. Add after rung 2 converts.
5. **Competitor names**: own campaign, own budget, small, only if the economics work. Never the bare name of a two-sided site.
6. **Informational** ("what is", "side effects", "how to"): skip or negate.

Don't skip rungs: broad and informational spend before rung 2 proves itself is how small budgets vanish.

**Search harvests demand, it doesn't create it.** If the category's searches are tiny in Keyword Planner, say so and point the budget at Meta or TikTok instead.

## 3. Structure

- **Ad groups share their campaign's budget.** A split between intents is only enforced by separate campaigns. Competitor and switcher terms always get their own campaign so they can't drain the core.
- **The ad-group ceiling (H):** an ad group needs about 10 clicks a day to teach you anything in two weeks. Ad groups ≤ daily budget ÷ (10 × CPC). $32 at $2.06 allows 2; $50 at $5 allows 1. Fewer, well-fed ad groups beat many starved ones.
- **Themed ad groups:** 4 to 10 keywords sharing one intent and one landing page. Two keywords that need different pages go in different groups. One keyword lives in one ad group only.
- **Mirror the leader where it's proven, skip where it's crowded.** Their profession or product pages show which segments pay; segments nobody's ads speak to are the gap (Low confidence until more ads are read).

## 4. Match types and bidding

- **Phrase and exact only at launch** (Corey; claude-ads). Broad only after about 30 conversions a month in the campaign, smart bidding live and a tight negative list (H). Adgence's `create_my_campaign` and `add_my_keywords` both default a keyword with no match type to exact; set `match_type: "phrase"` where you want phrase.
- Adgence creates Google Search with goal `traffic`, `awareness` or `engagement` only, and no bid strategy field. After creating: set bidding in Google Ads. By conversions in 30 days (H, Corey and irina): 0 to 15, Maximize clicks with a max CPC cap or manual CPC; 15 to 30, Maximize conversions; 30+ stable, target CPA near the trailing actual. Change targets in 10 to 15% steps.
- Settings to check in Google Ads before switching on (Corey): Search Partners and Display Network off, location "Presence", language. Adgence can't set these yet.

## 5. Negatives

- **Every negative needs a source.** claude-ads refuses negatives without a search-terms report; Corey ships starter lists. A new campaign has no search terms, so use evidence: wrong-intent terms in competitors' Similarweb keywords, wrong-audience traffic on two-sided sites, trade and job searches the competitors' own ads reveal (a competitor sitelink "Wholesale Sign Up"), competitors' brand words in the non-brand campaign, and a short general list (jobs, salary, course). Print the reason next to each.
- **Match mechanics** (Corey): negative broad blocks searches containing all its words, phrase blocks the phrase in order, exact blocks only that search. `create_my_campaign` now takes each negative as plain text (exact) or `{ text, match_type }` and sends the match type explicitly (exact by default); its preview prints them as `[exact]` and `"phrase"`. Choose per negative: phrase for a word that should block every search containing it ("jobs", "salary", a two-sided site's consumer phrase like "near me"), exact for one specific search ("vagaro login"). Read them back after creation with `get_my_keywords` (negatives only, free; synced about daily) to confirm.
- Don't over-negate: negate the clearly wrong, not the uncertain. After launch, the search-terms pass (2 to 3 times target cost per conversion with none, or plainly wrong intent: negate; converting but not a keyword: add) takes over.

## 6. The responsive search ad

Limits enforced by the code: **3 to 15 headlines of 30 characters, all different (case ignored); 2 to 4 descriptions of 90; callouts up to 10, 25 characters each; keywords up to 80 characters, up to 200.** Write 15 and 4. Not available through Adgence: display paths, pinning, sitelinks, structured snippets (set them in Google Ads; say so).

Every headline must read well next to any other. The mix (Corey's spec, extended):
- 3 to 4 **keyword** headlines (the ad group's main term, so the ad matches the search);
- 3 **benefit or use** headlines in the customer's words (copy-and-scripts §1);
- 2 **proof** headlines, only proof the user owns;
- 1 to 2 **price or offer** headlines (only true, current offers);
- 2 **CTA** headlines;
- 1 to 2 **counter-position** headlines: the thing the leader doesn't say.

Descriptions: product and mechanism; offer and CTA; the objection the searcher has; the counter-position. Print the character count beside every line; recount before answering.

**Never in the user's ads:** a competitor's trademark, their claims or wording, "Voted #1", savings figures, health or results claims, anything the user hasn't confirmed (dose, shipping, migration, deposits). Mark each unconfirmed fact.

## 7. Budget math

- Clicks a day = daily budget ÷ CPC (Keyword Planner's top-of-page low to high; Similarweb's CPC as a first anchor, labelled).
- Conversions: MISSING until the user gives their site's conversion rate. Show "every 1% of conversion rate is N orders (or trials) a month".
- Hold back a share for the competitor or switcher campaign only when its rung makes sense; otherwise all of it goes to rung 2.
- Campaigns can spend up to twice the daily budget on a given day; Google balances it over the month.

## 8. Decide

| Decision | When |
|---|---|
| Fight on the head term | The category term has real volume and the leader's ads don't own the user's angle |
| Take the niche | The leader's ads speak to someone else (salon owners, "everybody"); the user's segment has its own words |
| Wait on comparison | The leader targets comparison shoppers with proof the user lacks |
| Skip their name | Their brand terms are contested, expensive, or typed by their customers rather than shoppers |
| Run a switchers campaign | The leader runs one (switch pages, "vs" ads) and the user can make switching easy |
| Don't launch Search yet | Keyword Planner shows too little demand, or no conversion action exists to judge it by |

## 9. The quality bar

Every claim about a competitor carries the ad link, its first-shown date and days. Every volume and CPC names its source (Similarweb or Keyword Planner) or says MISSING. Every negative has its reason. Every headline and description has its character count, checked. Nothing the user hasn't confirmed goes into an ad without a mark. The answer says plainly what wasn't tested: the create step and Google's review only happen on a connected account.
