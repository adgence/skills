# The winning-ads method

How a senior creative strategist reads a whole niche's live ads to find what has proven itself, and turns it into a decision. Sources: Adgence's competitor-analysis, creative-strategy and product-research notes; Motion's competitor-watch (survival cohorts, "frequency = conviction") and creative-strategist ("reading bets, not results", the insight bar); boringmarketer's AD RECON (over-bought and unbought territory, count × longevity); hyperfx's meta-ads-library ("aggregate before quoting": 5 of 10 advertisers is signal, one ad is noise); ScrapeCreators' ad-library-teardown (messaging clusters, swipe file layout); Ira Bodnar's "keep the ones running for over 60 days"; and three Adgence test runs on 23 Nov 2026 (magnesium glycinate US, dog supplements US, a German run). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What a niche search really is, and what it can't say

`find_competitor_ads` with a `niche` reads **one sample** of live Meta ads that match the keyword in one country: up to about 150 ads from two library searches (exact phrase and loose match), merged, then sorted longest-running first. The order the library returns them is not run time, so:

- **"Longest-running in the niche" means "longest-running in the ads this search read".** Say "of the 87 ads this search read in the US", never "the 10 longest-running ads in the niche".
- **Repeats are collapsed.** Ads from the same page with the same first 80 characters of copy count once, so a brand's copy family shows as one ad. A brand's per-search count (`ads_in_this_search`) is how many of its ads this search kept, never its size. Brands showing 1 ad here ran 90 to 2,175 live ads in the tests. Size comes only from `compare_competitor_ads` or `find_competitor_ads` on the brand (`live_ads_total`, Meta's own count).
- **Only live ads.** Stopped ads aren't visible, so "survivor share" counts survivors among ads still live; it can't give a kill rate.
- **Spend, results and targeting: MISSING,** always. Run time means someone keeps paying for the ad. It does not mean the ad is profitable (a 60-day ad can spend $10 a day), and small advertisers forget to pause.
- **Don't pass `min_days_live`** on the first search: it filters the ads before they are saved, so you lose the base you need for survivor share. The list is already longest-first; apply your 60-day cut while reading.
- **Format labels are the library's.** A "video" in the niche search came back as a catalog carousel when the brand was read directly (BetterWild, 23 Nov). Give format counts Low confidence until watched. `catalog` now marks only feed ads (template placeholders, or a dynamic ad with next to no copy); a dynamic-creative ad with its own copy carries `dynamic` instead (Herzina's 321-day reaction video, Double Wood's glycinate carousels): read those as creatives. A `catalog` ad can still be the brand's only long runner on a product page (Nature's Bounty, 224 days on its glycinate page): count it for survival, not for creative lessons.
- **Read the whole sample, then clean it yourself (`keep_all: true`).** The tool's off-topic check runs on AI when it can (`relevance_by: "ai"`) and otherwise falls back to words (`relevance_by: "words"`, with `relevance_note` and `unconfirmed_advertisers`). Since the 23 Nov fixes the word check treats a compound and its split form as one word ("Magnesiumglycinat", "Magnesium Glycinat"), reads the landing path (/produkte/magnesiumglycinat) and keeps an advertiser with the words only in its copy, listed as unconfirmed. It is still a word check: it can only see the copy, headline, page name and link the tool holds, while Meta's keyword search also matches text the tool doesn't get. Re-test, 23 Nov evening: in the US it kept 42 of 86 ads, 20 of them in the niche, and dropped 12 in-niche ads (SPNutrition's men's ad with 4 copies running, Nature's Bounty's Amazon ad, a Gold Seal persona ad, Mike Mutzel's magnesium carousels); in Germany it kept 23 of 52, 7 in the niche, and kept Sunday Natural's two glycinate-page ads, New Nordic and Pure Encapsulations' three glycinate carousels, but dropped all 4 Herzina ads (321 days, the market's longest-running magnesium ads: "8 Formen von Magnesium", no "Glycinat" in their copy) and Sunday Natural's forms ad. In both markets more than half of what it kept was outside the niche (migraine, ADHD and neuropathy advertorials, cortisol drinks, sleep gummies). The AI check would also drop the adjacent sellers you need for "both counts" (§2). So search with `keep_all: true` and apply §2 by hand; it costs the same credit.
- **English words abroad are marked.** In a non-English country, ads aimed at another market carry `other_market` (another country's site, or copy in another language) and the result an `other_market_note` with the count. Keep them out of the local base.

## 2. Clean the sample: who is really advertising

Classify every ad read into one of these, from its landing URL, page name and copy:

| Class | How to tell | Counts as |
|---|---|---|
| **In-niche seller** | Lands on a product, collection or advertorial page of a brand selling this product | The base for every measure |
| **Persona or partner page** | A person's name, "Dr.", "Tips", "Journal", "Research Institute", a creator, with a landing domain that belongs to a brand | Merge into the brand that owns the landing domain. It is that brand's ad, run through another face (whitelisting, creator partnership, persona page) |
| **Review or listicle site** | "top 5", "best X", "we tested 47", comparison pages, `/review`, `/listicle` | Keep as an advertiser (they buy the same buyers) and note whose product they push when the copy says so |
| **Adjacent** | The product only contains or mentions the term (a migraine roll-on in a magnesium search) | Out of the base; mention in one line if it teaches an angle |
| **Retailer or marketplace** | Amazon, Chewy, iHerb, Walmart, Vitamin Shoppe, Criteo redirects | Out of the base; one line ("Amazon listings are pushed by N brands") |
| **Comment bait** | Text-only questions from person pages with no landing ("where do y'all buy legit X??") | Out of the base; read them as **the niche's objections** (§7) |
| **Catalog** | `catalog` flag, or copy-less carousels | Out of creative lessons; count apart |
| **Off-topic** | Another category entirely | Drop |
| **Other market** | `other_market` set (another country's site, copy in another language) | Out of the local base; one line, and check the few that may sell locally |

**When the verdict depends on the cleaning, show both counts.** If counting adjacent sellers (a cortisol drink that contains the ingredient, a patch, a complex) would change "proven" or "thin", give both: "4 proven brands selling magnesium; 6 if you count the calm drinks".

**The advertiser is the landing domain, not the page.** In dog supplements, betterwild.com ran through BetterWild, a celebrity vet's page and an influencer's page; dogishuman.com through three pages; trycanislabs.com through an "animal hospital" and a "research institute". Group by domain before ranking anyone. Say how many pages each domain uses: it is a scale signal and a format choice (§6). A brand read (`compare_competitor_ads`, `find_competitor_ads` on a page) covers **one page**: a brand that advertises mostly through persona pages looks smaller and younger there than in the niche search (Herzina: 190 live ads and 245 days on its page, a 321-day ad on a persona page). Compare by page id. A domain for these brands resolves through the ads that land on its site (`matched_by: "landing domain"`): the lead page is then read whole, so `live_ads_total` is Meta's own count for it plus the ads found on the other pages, a floor for those (`landing_note`, `landing_pages`). trinkjello.com: 2,185 (2,147 on the TryJello page, 183 of them past 90 days, plus 13 persona pages). The other pages' share is still a floor: say so.

## 3. Read the niche: the measures

All on the cleaned base, each with its base stated.

| Measure | How | Read it as (H) |
|---|---|---|
| **Survivor share** | Share of in-niche ads live 30+, 60+ and 90+ days | 60+ days on 40% or more = a mature niche where things that work are left running. Under 15% at 60+ = new, churning or seasonal |
| **Proven advertisers** | Independent domains with at least one ad at 60+ days | 3 or more = **validated paid demand** (several sellers keep paying). 1 = one brand's bet. 0 = unproven |
| **Concentration** | Share of in-niche ads held by the top 3 domains in this search | Over 50% = a few players own the paid feed. Under 30% = fragmented, easier to enter |
| **Freshness** | Ads started in the last 14 days, by domain | Who is pushing now. A burst in late November is usually a sale (check the copy) |
| **Leaders' real size** | `compare_competitor_ads`: `live_ads_total`, `longest_days`, `scaling`, `new_this_week`, and `brand_read` (Meta's exact `running_30_days_plus` and `running_90_days_plus`). Also for the 1 or 2 sellers closest to the user's product | Tens of live ads = a testing operation; thousands = a creative machine (BetterWild 2,175). The 90+ day share of a brand's live ads is how much it keeps running (Herzina 26 of 190; Sunday Natural 121 of 612). Size separates a deliberate long runner from a forgotten one (§4), and a young in-niche ad from a big advertiser testing the niche |
| **A big brand's footprint in the niche** | `find_competitor_ads` on its page id with `product` = the niche word: `product_filter` matched of read, and `read_as` on each ad | How much of its account is this product (Sunday Natural: 3 of 148 ads read mention glycinat, of 612 live). Those ads tagged `most_seen` are among the brand's most-shown: a test it is putting money behind, not a side line |
| **Shelf price** (Founder, when the user gave a price) | `find_products`: range, quartiles, who owns each band and with how many reviews | Where the user's price sits, and whether the ad must justify it. Per pack, not per unit |
| **Format mix** | Video, static, carousel, text-only among long runners vs new ads | What survives vs what is being tried |
| **Landing mix** | Product page, advertorial or listicle, quiz, comparison, collection, home | Several advertorials = cold traffic at scale. Many review or "we tested" pages = the buyer doesn't trust brands yet |
| **Language** | Phrases repeated across advertisers, verbatim | The niche's proven words |

**Seasonal check:** if the run falls in a sale period (Black Friday to Cyber Monday, Christmas, January, Prime Day), most new ads are offers. Say so, and keep sale ads out of the formula.

## 4. Which ads count as winners

An ad's run time only means something next to who runs it.

| Status | Rule (H) | Confidence |
|---|---|---|
| **Proven, kept on purpose** | 90+ days, from a domain with 20+ live ads that is still launching (`new_this_week` > 0 or fresh ads in the search) | High |
| **Proven** | 60+ days, or 30+ days with 3+ copies (`same_creative_count`) | Medium, High when the advertiser is big and active |
| **Being scaled** | 3+ copies of one creative, or the same text on several ad ids or pages | High when stacked with 60+ days |
| **Possibly forgotten** | 90+ days from an advertiser with 1 to 3 live ads in all, or a trade body or retailer | Low: show it, don't build on it |
| **In test** | Under 30 days | A bet, not a result |
| **Evergreen brand or catalog** | Catalog carousels, retargeting offers, brand-awareness ads of mass brands | Ignore for creative lessons |

Copies count as one concept: `creative_id` when present, else the same text and image.

## 5. Pick what to watch

- **Check the library first** (`my_ad_library` with the niche word, free): ads already broken down in this workspace are reused, and ads watched in the last 30 days come back free.
- **Distinct concepts across distinct advertisers.** At most 2 per domain. Pick the strongest-signal ad of each recurring structure (§6), plus 1 newest bet from a leader if the Founder or Vertical user needs "what's next".
- **Check what the landing sells** before paying: a long runner from a brand whose product is adjacent (a horse injectable in a dog search) teaches less than a slightly younger in-niche ad.
- **Video first.** A static's lesson is mostly in its copy, which the search already gave you; watch a static only when the image carries the idea (a comparison, a label).
- `break_down_ads` takes **up to 10 per call; send 3 at a time when the videos are long.** Three long videos once overflowed the reply; it now shares 8 frame images across the ads still to write, keeps the first 40 cuts of a long cut list and clips transcripts to one budget, so the call fits. It can answer with `still_watching` and `credits_pending`: the rest finish in the background, are charged as each finishes, and come back free when you call again a minute later. `account` with `since` counts them.
- Write each one's Creative DNA (`save_ad_dna`, free): the three hook layers verbatim, the beat sheet, pacing measured (cuts, seconds, when the product appears), message, why it likely works, what's borrowable. Never re-save an ad that already has DNA.

## 6. Find the formula: convergence across advertisers

The insight a niche search gives that no single-brand teardown can: **what several independent advertisers converged on and kept paying for.**

- Tag every long runner (60+ days) with the fixed labels below, from copy for all of them and from the watch for the watched ones.
- **Niche-proven:** an element (structure, angle, hook template, landing type) in the long runners of **3+ independent domains** = High. 2 domains = Medium. 1 domain = that brand's choice, not the niche's.
- **Over-bought territory:** an angle almost every advertiser runs. Entering there means paying to be ignored unless you bring new proof (AD RECON).
- **Unbought territory:** pains, personas or awareness stages absent from every long runner. A hypothesis to check against customers, not a finding by itself.
- **Table stakes:** what every survivor shows (product in hand, a review line, a guarantee). Necessary, not differentiating.
- **Frequency = conviction** (Motion): rank angles and value propositions by how many long runners carry them.
- **Say when a converging brand sells something adjacent** (in the magnesium test, one of the 4 brands behind the forms argument pushes L-threonate, not glycinate). It still counts for the structure; say so for the claim.
- **Structure over claim.** Long runners can converge on a structure while disagreeing on the claim (dog supplements: "it's not the gut" and "it's the gut", both past 140 days, same "what you tried failed because of the wrong cause" structure). The structure is the finding.

Labels (Adgence's Creative DNA, so counts match the saved library):
- **Hook type:** problem, question, pov, shock_stat, before_after, testimonial, demo_first, unboxing, listicle, discovery, fake_text, green_screen, challenge, offer_first, other
- **Format:** ugc_talking_head, founder_story, product_demo, unboxing, before_after, slideshow, faceless_broll, skit, reaction, green_screen, voiceover_demo, static_image, carousel, motion_graphics, other
- **Awareness:** unaware, problem_aware, solution_aware, product_aware, most_aware
- **Angle** (report only): pain, myth-bust or "it's not X", mechanism or ingredient form, comparison, drug or old-way alternative, authority, social proof, identity or values, convenience, taste or experience, price
- **Face:** brand page, persona page, expert or "Dr." page, creator, celebrity, faceless
- **Landing type:** product page, advertorial, listicle or "we tested", comparison, quiz, collection, home, marketplace

Patterns seen in the tests (examples of what the method finds, not rules for other niches): in magnesium glycinate (US, 23 Nov 2026) the long runners of 4 domains answered "why didn't my magnesium work?" with the form or the label (oxide vs glycinate, fillers, dose); in dog supplements (US) 5 domains ran the same story-advertorial opener ("I'm going to say something nobody who owns a dog with … wants to hear") from persona pages, and 5 sold their chew as the alternative to a named prescription drug.

## 7. Read the niche's objections for free

Comment-bait posts and review-site hooks are written from what buyers ask. Count them: "where do I buy legit X, Amazon is full of fakes", "which form do I even look for", "anyone actually tried it?". They tell a founder what the first ad must answer, at no cost. For counted customer language, offer the customer-review-mining skill.

## 8. Risky to copy

Flag, never recommend: invented or AI-made doctors, vets and "research institutes" as faces; university or PubMed logos on screen; "we tested 47" claims with no test shown; named-competitor comparisons; disease or drug-replacement claims ("instead of Apoquel", "the vet said Rimadyl"); death and fear stories about pets or relatives; "Big Pharma scam" framing; AI presenters and AI product images with garbled labels. Several long runners in health niches use these: they survive in the library but put an ad account and a brand at risk (Meta's health and personal-attributes policies, FTC endorsement and health-claims rules). The structure is often borrowable without the claim: a real expert, a real label comparison without naming the brand, a real customer story.

## 9. Decide, then plan

| Decision | When |
|---|---|
| **Go: paid demand is proven** | 3+ independent domains keep 60+ day ads in the niche |
| **Test small first** | 1 or 2 proven domains, or a thin sample (§10) |
| **Use the niche formula, with your proof** | A structure is niche-proven (3+ domains) and fits the user's product without their claims |
| **Take the empty ground** | An angle, persona or stage no long runner uses, ideally one the comment bait asks about |
| **Avoid** | Over-bought angles without new proof; risky structures (§8) |
| **Watch** | A leader launching fast right now (`new_this_week`, fresh ads): track it |

Then **the first 3 ads**: for each, the hypothesis, the structure it borrows (with the reference ad's link and days live and how many domains use it), the angle, 3 hooks (visual, text, spoken), format and length, what the user must supply (their proof, label, customer story), and the success measure (purchases or CPA for a new concept; hook rate for a new hook; hold rate for a new body). Name each `Concept_Angle_Hook#_Format`. Mark a matrix built from copy only (Quick) as a draft.

## 10. Thin niches and other markets

- **Thin:** under 15 in-niche ads or under 5 independent domains after cleaning (H). First broaden once, from a sub-variant to what buyers search ("magnesium glycinate gummies" → "magnesium glycinate"), never to the parent category. In a non-English market, search the local word first; add the English word in that country only if the local read is thin, and clean it harder: it returns English-language advertisers selling from other countries (a US store in a German search, now marked `other_market`: 30 of 40 in the German test) and more off-topic ads. Merge the two reads of the same market; never merge another market. Still thin: say so plainly and read it as a finding (little paid competition, or little paid demand), and point to the demand checks (`find_products`, `find_viral_videos`, the product-demand-check skill). Don't fill the swipe file from another country without saying so.
- **Another country as reference:** the US or the home market of the leaders often shows what will arrive later. Read it as "what works there", labelled, and never mix its counts into the user's market.
- **Translate hooks,** keep the original in quotes next to the translation.

## 11. The insight quality bar

Every finding is evidenced (an ad link with days live, a count with its base), non-obvious, and tied to a decision. Cut: lists of ads with no "so what"; "they use UGC" (say the structure: one take, product in hand from 0 s, a myth-bust opener); run time called profit; advice that fits any niche; patterns from one advertiser presented as the niche's. If a finding wouldn't change what the user makes or decides, move it to the evidence or cut it.
