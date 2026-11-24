# The comment triage method

How a senior community manager and media buyer read the comments under paid ads, decide what to answer, hide or escalate, and turn what people say into the next ads. Sources: Adgence's my-ads notes (§7 comments, §12 replying, §5 fatigue, §11 edits), customer-research notes (coding, intensity, likes, pitfalls), copy-and-scripts notes (§5 compliance), Octolens's churn-signals and Reddit reply playbooks, ScrapeCreators' comment-mining skill, Corey Haines's ad-creative skill, Triple Whale's LSKD case, Old Bones' review-language test, and three Adgence test runs on real public ad comments (23 Nov 2026). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data is, and what it can't say

**`get_my_ad_comments`** (free, one ad per call, needs a connected Meta or TikTok account) returns per comment: `comment_id`, `reply_to` (set when it's a reply: the `comment_id` it answers), `placement` (Meta: facebook or instagram), `from_brand` (true on the brand's own comments and replies), `text`, `likes`, `replies` (how many replies it has, from anyone), `at` (when), `hidden`. Plus a `from_brand` count, `next_cursor` (one thread) or `threads` (both, each with its own `next_cursor`) when there are more (up to 100 per page per thread). `since` (YYYY-MM-DD) keeps only comments from that date.

- **Usernames are left out** on purpose. You can't tell which customer wrote a comment, but **`from_brand` tells the brand's own comments and replies apart** (the platform's owner flag). Never count them as customers. A customer comment with a `from_brand` reply under it is already answered: read the reply, and only list it again if the answer was wrong or the customer came back. If `from_brand` is missing on a platform, fall back to reading the voice, and say the split is inferred.
- **Already answered?** `replies` above 0 only says someone replied, not the brand. Say "has replies, may already be answered" and let the user check.
- **Meta ads running on Facebook and Instagram have two separate threads.** The tool now reads both by default (`placement` "both"), tags each comment with its thread, and says in `threads` (and `note`) when an ad has no thread on one placement. `placement` picks one, and a `next_cursor` must go back with the placement it came from.
- **`since`** keeps comments from a date: on TikTok the platform filters (the last 30 days at most); on Meta it's applied to the comments read, so a thread with many newer comments may need the cursor to reach the start date.
- **Moderation preview checks.** `moderate_my_ad_comments` without `confirm` looks for the comment in the ad's newest 300 comments per thread (`checks.comment_found`) and, on Meta, whether the Page or Instagram account behind the ad's post is reachable (`checks.page_reachable`). A failed check comes with `problem` and a `next` that says not to confirm: never ask the user to approve an action that would fail.
- **Dark posts are included** (ads that never ran organically). That's the part no public scraper can reach.
- **Google ads have no comments** (the tool says so). For what Google searchers want, the search terms are the equivalent (`get_my_search_terms`).
- `hidden: true` comments are already handled: skip them, and count them apart.

**Public posts** (`collect_comments`, 1 credit for Instagram, Facebook, TikTok, 0 for YouTube): the same job when the ad runs on a public post (Spark Ad, partnership ad, boosted post) or to practise on a competitor's ad. Each comment has `text`, `likes`, `date` and a `url`, and the collection works with `get_customer_comments` and `count_mentions`.
- The collector returns **a sample by likes, not every comment**: 45 of a TikTok ad's 1,862 comments, 20 of another's 29, 29 of an Instagram post's comments (23 Nov tests). Always say "N collected of M shown on the post" when the post shows a count.
- Ads that run on someone else's post (a creator's partnership ad) live in the creator's thread: what the brand can moderate there is not confirmed. Say so.

**Counting.** `count_mentions` counts themes in a collection, a scrape or a tracking id. It **can't take `get_my_ad_comments` results** (still a product gap after the 23 Nov fixes): for own-ad comments, keep a running tally by hand as you classify (leaving out `from_brand` comments), and recount from it before writing any number. Every count says its base: "7 of the 63 comments read on 3 ads, Instagram threads only".

**Small numbers.** Under about 50 relevant comments across the ads read, findings are **directional** (G3). One thread can be a pile-on by a few people: count distinct comments, weight likes by their order of magnitude (log), and look for the same theme on a second ad before calling it a pattern.

## 2. Classify every comment (fixed classes)

One class per comment, the one that decides the action. Keep emoji-only and tag-only comments in their own class so the base stays honest.

| Class | What it looks like | Default action |
|---|---|---|
| **Safety or health** | Adverse effect ("makes me so bloated", "strongest laxative"), injury or defect that could hurt ("turns on by itself", "burned"), allergy, pregnancy or medication question, a dangerous use ("80g a day, is that enough?") | Escalate (P0). Reply only with the approved holding line; never advise |
| **Legal or reputation** | "Fraud", "scam", "lawsuit", "reported you to the FTC/BBB", counterfeit or copycat claims, IP, a regulator named, a journalist | Escalate (P0). Neutral reply at most; never delete |
| **Customer problem** | Order, shipping, refund, cancellation, subscription charge, support unreachable ("I reached out and no one helped me") | Answer today (P1): one public reply that moves it to support, then a support ticket |
| **Pre-purchase question** | Price, sizes, ingredients, how to use, where to buy, shipping times, "is this AI?" | Answer today (P1) from the FAQ; MISSING when the FAQ lacks it |
| **Objection** | Doubt or pushback that blocks a purchase: price, "does it work", dose, format ("pills are easier"), trust, "made in China" | Answer if the top-liked ones can be answered with a fact (P2); always tally for copy |
| **Misinformation** | A false claim about the product or category, including one the ad itself started | Correct once with a fact (P2), or fix the ad (§5) |
| **Competitor mention** | Names or links another brand or product | Leave visible unless it's a link, a code or spam; tally (P3) |
| **Praise or testimonial** | "Tried it it's insane", "best ad I've seen" | Like it in the app; reply to a few top-liked ones (P3); harvest with consent for proof |
| **Social** | Tags a friend, banter, memes, off-topic, other languages, emoji-only | Leave (P3). Count apart |
| **Request** | PR package, collab, wholesale, job ("where can I get this job") | Route to the right team (P2) |
| **Spam or scam** | Links, crypto, "DM me", bots, fake giveaways, impersonating the brand, phone numbers or personal data | Hide (P1) |
| **Abuse** | Slurs, harassment, threats, sexual content | Hide now (P0 for threats); delete only per §4 |

Also tag, for the copy work: **awareness stage** (unaware to most aware, from the customer-research notes), **intensity 1 to 5** (H), and whether the comment is **about the ad or the product** ("the best ad I've seen" is about the ad).

Sarcasm and jokes: "Yall gonna love kidney stones" is a joke and a safety fear at once. Class it by the fear (Safety), and note it's a joke: the reply should be calm and factual, never jokey about health.

## 3. Priority and timing

| Priority | What | When (H) |
|---|---|---|
| **P0 escalate** | Safety, legal, threats, personal data exposed, a pile-on (several similar negative comments in a day, or a negative comment that's the top-liked) | Same day, to the owner. The ad may need pausing |
| **P1 answer** | Customer problems, pre-purchase questions, spam and scams | Within a day on a spending ad: unanswered questions under an ad you pay to show are lost sales |
| **P2 consider** | Objections and misinformation with likes, requests | When a factual answer exists; otherwise feed copy |
| **P3 leave** | Praise, social, competitor mentions without links | Like in the app; reply to a few |

On a spending ad, the most-liked comments are the ones every later viewer reads first. A top-liked objection left unanswered works against the ad: answer it or fix the ad.

## 4. Hide, delete or leave (the rules)

- **Leave honest criticism visible.** Complaints, objections, bad reviews and competitor mentions stay. Hiding them to look better backfires when people notice, and the FTC's 2024 reviews rule bars suppressing honest reviews on a business's own site: treat honest criticism under ads in the same spirit (H, not legal advice).
- **Hide** (reversible): spam, scams, links, promo codes for other brands, bots, phone numbers and other personal data (even the commenter's own), slurs, harassment, sexual content, and impersonators. On Facebook the commenter and their friends still see a hidden comment, so it doesn't provoke them.
- **Delete** (permanent, the user's explicit yes per comment): only illegal content, threats, doxxing of someone else, or the brand's own mistaken reply. Keep a record (text, date, link) before deleting. **Never delete safety reports, legal threats or fraud accusations:** they are evidence. Hide if they contain abuse, and escalate.
- **TikTok:** the tool can only delete the brand's own comments there.
- **Never hide a customer's problem to make it go away.** Answer it and move it to support.

## 5. Read each ad, not only each comment

Comments are a signal about the ad. Per ad, report:

- **The mix:** share of objections, questions, complaints, safety, praise, social (of the comments read).
- **What the ad triggered:** when the top-liked comments all push back on one claim in the ad, the ad started it. Crave's creator post said 20g: 8 of 26 comments doubted the dose and 3 (100 likes in all) joked about kidneys. Replies can't fix that: **fix the ad** (a new cut with the label dose) or stop it.
- **Brand linkage:** lots of praise for the ad and almost no mention of the product means the story works and the brand doesn't land. Manscaped's 7.5M-view post: 18 of 41 comments praise the ad, 1 names the category ("IT WAS A SHAVING AD!?", 71 likes), 0 name the brand or a product. Test an earlier product reveal or an end card.
- **What the ad invites:** an ad about customer service drew two support-failure complaints and a fraud accusation in 20 comments. An ad about a promise invites people who feel it was broken: fix support before scaling it.
- **Fatigue:** "seen this a hundred times", "every ad", "stop showing me" (my-ads §5). Stack with frequency and CTR from `get_my_ads` before calling it.
- **Decision per ad:** keep; answer the top objection in the comments; fix the copy; new cut; pause. A pause or an edit goes through its tool's preview first. Editing a Meta ad's creative rebuilds it and leaves its likes and comments behind (my-ads §11): for a winner with social proof, prefer a new ad in the same ad set and pause the old one once the new one works.

## 6. Writing the replies (brand voice)

From the brand note (`get_board` "Brand"): tone in 3 words, emoji or not, sign-off, words never used, and the FAQ facts. Without it, use the defaults below and say so.

**Who reads a reply:** not mainly the commenter. Every later viewer of the ad reads the top comments and the brand's answers to judge how they'd be treated (boringmarketer's reply playbook makes the same point for reviews). A calm, specific answer to the top-liked objection does more for the ad than ten thank-yous.

- **Specific to the comment:** pick up the detail they gave. No two replies alike: a row of identical "Thanks!" reads like a bot. For praise, reply to a few of the top-liked ones and like the rest in the app.
- **Lines to avoid:** "We're sorry you feel that way" (reads as not sorry); a paragraph proving them wrong (the reader sees a fight); anything about their order or account they didn't share; threats, lawyers, or asking them to delete it.
- **A comment that looks fake or from a competitor:** don't accuse. Hide it only if it carries a link, a code or abuse; otherwise one calm factual line or nothing.
- **Short:** one or two sentences, under about 200 characters (H). Answer first, then one next step.
- **Facts only from the FAQ or the user.** Prices, shipping times, ingredients, dosage, origin, guarantees: if it isn't in the FAQ, write MISSING and ask. Never invent.
- **Compliance (copy-and-scripts §5):** no health, cure, weight, income or guaranteed claims; no "you" tied to a personal attribute; no medical advice, ever. Point to the label and a doctor.
- **Never ask for personal data in public** (order numbers, emails, addresses). Give the support channel.
- **One public reply per person.** No arguing, no sarcasm back, no reply to trolls. A second reply to the same angry person goes private or not at all.
- **No promises on someone else's behalf:** no "we'll refund you" unless the user says so; no free product to whoever asks.
- **Don't name competitors** in replies. Don't confirm or deny someone is a customer.
- **Disclose AI only if asked and true;** "Is this AI?" gets a plain honest answer from the user, or MISSING.
- Mark every draft as a draft. Nothing is posted without the user's yes on that exact text.

**Holding lines** (the user approves these once, then they're reused):
- Safety: "We're sorry to hear this and want to look into it. Please contact us at [support] so our team can help." No diagnosis, no product claims.
- Medical question: "Please check with your doctor before changing how you take any supplement. The label has our directions." No numbers unless the label is in the FAQ.
- Legal or fraud: "We take this seriously. Please reach us at [support] so we can look into your account." Then escalate.
- Customer problem: "Sorry about this. Please message [support] and we'll sort it out." Then open a ticket with the comment link.

## 7. Escalation policy

| Trigger | Who | Public reply | Never |
|---|---|---|---|
| Possible harm (adverse effect, defect, burn, allergy, dangerous use) | Owner and quality or regulatory lead, same day | Safety holding line | Diagnose, argue, delete, promise a fix |
| Legal words (fraud, lawsuit, FTC, BBB, counterfeit, IP), press | Owner (and legal if they have one) | Legal holding line or none | Delete, admit fault, debate |
| Refund, order, billing, cancellation, support failure | Support, within a day | Customer-problem line | Ask for order details in public |
| Angry customer, repeated | Support lead | One reply, then private | Reply twice in public |
| Pile-on (several similar negatives in a day, or a negative top comment) on a spending ad | Owner and the buyer | Answer the top comment once with a fact | Mass-hide criticism |
| Requests (PR, collab, wholesale, jobs) | Partnerships or careers | Short redirect | Promise product or jobs |

The answer lists P0 items first, each with the comment's link or `comment_id`, the class, why, and who should get it.

## 8. Turn objections into ad copy

Comments under ads are the freshest voice of the customer: non-buyers' objections at the moment the ad met them (Corey Haines calls ad comments "the most-skipped and highest-value input").

1. **Tally** objections and questions across the ads read, with likes and the ads they came from.
2. **Rank** by distinct comments first, then likes (log), then intensity. Frequency decides what the body and landing page answer; intensity decides hooks.
3. **Map each objection:** objection · count and likes (base) · verbatim example · where it bites (pre-click, pre-purchase, post-purchase) · the fact or proof that answers it (MISSING until the user supplies it) · where the answer goes: reply, FAQ, landing section, ad body, hook.
4. **Write the inputs, not the ads:** 1 or 2 hook lines lifted from the verbatim, the proof needed, and the claim to avoid. A frequent question or objection is also a ready ad format: the "FAQ card" or reply-to-comment ad that answers it on screen (Corey Haines's ad-creative). The ad-hooks-and-copy skill writes the ads.
5. **Rules:** quotes verbatim and marked; situation, not personal attribute; no health or result claims; never repeat a competitor's claim. Don't reuse the same quote in ads for 90 days (Old Bones). Measure the copy test on purchases or CPA: Old Bones' review-language page lifted add-to-carts 47% and purchases not at all.
6. **Recurring objection = a weekly item:** two weeks running on spending ads, it goes to the FAQ, the landing page and the next brief.

## 9. Confidence

- **High:** 3+ distinct comments across 2+ ads, or one theme in the top-liked comments of an ad with 50+ comments read.
- **Medium:** one ad, 8 to 20 comments on a theme, or a sample collected by likes.
- **Low:** under 8 comments, one ad, or a public post sample of a few dozen.

## 10. The quality bar

Every finding ends in an action: answer, hide, escalate, fix the ad, or a copy input. Cut: sentiment percentages with no decision, "people love the ad" without what it means for the brand, comment dumps, drafts that make claims the FAQ doesn't support. Mask personal data in every report (phone numbers, emails, other people's @handles).
