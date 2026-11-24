# The viral video method

How a senior social strategist reads organic outliers in a niche and turns them into posts and ads. Sources: Adgence's creative-strategy notes (§4 hooks, §5 formats, "views prove the hook and format, not that it will sell"), copy-and-scripts (hook layers, compliance), customer-research and product-research (organic signals, comment reading), ScrapeCreators' outlier-post-finder and comment-mining skills (median baseline, lift tiers, small-sample confidence), Motion's write-hooks skill (attention versus action, the 5-test hook bar), FastMoss's video-brief skill (organic versus ad-driven, reverse-engineer what sells), and three Adgence test runs (creatine gummies, cleaning paste, skin barrier; 23 Nov 2026), re-tested after the product fixes the same day. Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data can and can't say

`find_viral_videos` searches TikTok and Instagram for a keyword and returns up to 20 videos, most viewed first. When both platforms are asked for, TikTok gets up to 12 of the 20 and Instagram the rest. It costs 1 credit per 2 videos found, at most 3, and the same search is free for 72 hours (`refresh: true` fetches anew). `platform_counts` says how many each platform returned before the cut (40 TikTok and 8 Instagram for "creatine gummies").

What each field really is:

- **`views_per_follower` is views divided by followers** (followers floored at 1,000), not views against the creator's usual views; the tool now says so, and `beat_usual_reach_by` is the same number under its old name. It is wrong in both directions. In the tests: a skincare creator showed 3.4x but was really 51.4x her median; a trainer showed 1.3x but was 2.0x (2.2x on the re-test); an affiliate account showed 2,335.5x but was 5,347x; another showed 441.2x but was 55,146x (median 8 views); a skincare educator showed 36.2x but was 67.9x. Use it only to pick which creators to check. The real lift comes from `get_creator` (below).
- **Instagram rows now carry followers** when the creator's profile could be read: 8 of 8 in the skin barrier re-test, 1 of 8 in the creatine one. A row without followers has no ratio; say so rather than guess. `get_creator` on an Instagram account now gives `median_views` from reel plays (`views_note` says how many reels; photos have none), so Instagram lift is measured like TikTok's, on fewer posts. A creator who posts a reel every few months (12 reels over 497 days) gives a thin median: Medium confidence at best.
- **`opening_line` is the first sentence of the caption**, not what's said or shown. An affiliate video captioned "Ditching the powder in 2026" actually opens with the spoken line "What happens to your body after four weeks of creatine?". Hooks are only known after watching.
- **`engagement_percent`** is (likes + comments + shares + saves) / views on TikTok; Instagram has no saves. Compare it within a platform, never across.
- **`posted`** matters: views pile up for months. A 2-year-old video is an evergreen format, not this week's trend.
- **The search can drift off topic.** Instagram matched "creatine gummies" with 8 prank, art and comedy reels (0 of 8 about creatine, again on the re-test, where they ranked above every TikTok because they have the most views); "creatine" alone returned 20 of 20 on topic. TikTok "cleaning paste" returned mostly ASMR paste-play hobby videos (10 of 12), not people cleaning. Always check relevance before counting anything.

What no tool shows: reach source (For You, followers, boost), watch time, completion, saves on Instagram, or sales. Write MISSING for those.

**Views prove the hook and the format, not that it sells.** The proof that a format sells comes from somewhere else: the videos selling a TikTok Shop product (`get_tiktok_shop_product`, most viewed first), or the same format running as a paid ad for 30+ days (`find_competitor_ads`).

## 2. Measure the real outlier

**Lift = the video's views / the creator's median views** (`get_creator` → `recent.median_views`; `posts_read` gives the base, usually 10 recent TikToks or about 12 Instagram reels). Median, not mean, so one hit doesn't hide itself. If the outlier isn't among the posts read (it often isn't, being older), the median is the creator's usual now, which is what you want. Under 10 posts read is a small base: one step lower confidence (H). For a brand account, pinned posts are counted in the median (CeraVe: 2 pinned of 10), so glance at them.

| Lift (H) | Read |
|---|---|
| 5x+ | Huge outlier: something in this video, not the creator, did it |
| 2x to 5x | Strong outlier |
| 1.5x to 2x | Mild: on a big account this can still be the most useful video (it reached a proven audience) |
| Under 1.5x | Not an outlier: the creator's normal |

Then four checks before calling it a signal:

1. **Small-account lottery (H).** Median under about 1,000 views and a lift in the thousands = a numbers game, not a proven hook. The affiliate account in the creatine test posted the same caption 7 times in 5 days; one copy reached 5,000,000 views and one 962,100, while the other five got 189 to 113,700, against a median of 936. On the re-test a 451-follower account had 441,169 views on one blueberry gummy loop against a median of 8 views, 9 posts a week and 6 of 10 posts tagged TikTok Shop affiliate (`sponsored_breakdown`). Read it as "this format gets tested at volume", Low confidence, and look for the same format working on other accounts.
2. **Reach without resonance (H).** Engagement under about 1.5% on 200k+ views, especially from accounts under a few thousand followers, often means pushed reach (affiliate seeding, a paid boost, a For You test). In the creatine search, 4 of 12 TikToks were accounts under 2,200 followers with 0.55% to 1.29% engagement. Resonant educator videos sat at 4% to 11%.
3. **Who made it.** Tag each: educator or creator (own voice), brand-paid creator (`@brand`, `#brandpartner`, `#ad`, codes, "REKLAMA"), TikTok Shop affiliate (hard sell, "deal is live, click below"), a business (a cleaning service), hobby or ASMR, brand account. For a creator you checked, `get_creator` counts it for you: `sponsored_breakdown` (paid or ad tag, TikTok Shop affiliate, campaign tag), `campaign_tags` and `brands_tagged_in_sponsored` (a skincare creator with 2 of 10 posts paid by two brands is a UGC creator, still own voice). FastMoss's rule fits: organic hits teach the hook; paid or seeded hits may only teach the budget.
4. **Age.** For "this week's plan", weight videos from the last 90 days (H); older hits are formats, not trends. Fresh outliers (under 14 days, or older when their newest comments are from this week) can be ridden now with a reaction or reply.

**Confidence:** High = verified lift 2x+ against the median, resonant engagement, own voice, recent. Medium = verified lift but one of the checks fails, or a thin base (under 10 posts, or an Instagram creator with few reels). Low = only the tool's views-per-follower, or a lottery account.

## 3. Read why it worked (watch it)

Watch the 2 to 4 verified outliers with `break_down_ads` (1 credit each, free if watched in the last 30 days). For each, write:

- **Hook in three layers**, quoted: first frame, on-screen text, spoken line. Note when the caption and the spoken line differ.
- **Pacing, measured:** "8 cuts in 90 s, first cut at 7.9 s", "one take, 0 cuts in 41.1 s". Never estimate.
- **Format and mechanic** (Creative DNA labels: hook type, format, trigger, awareness).
- **Where the product appears**, if at all ("from frame 0", "at 12.5 s", "never: viewers ask in comments").
- **Why it likely works**, in one line, and what's borrowable (structure, device, pacing) versus not (their claims, face, footage, words).

Patterns from the tests worth knowing (observed, not rules):
- Both biggest verified outliers in the supplement and skincare searches were **green-screen reactions to a category scare**: "Creatine gummy scam" (2.0x a 665,100 median) and "How to undo the damage" over a toner-pad reaction clip (51.4x a 38,400 median).
- **Timeline before/after with no product** (9 s, 3 shots) hit 47.9x; the creator followed up the same day with a reply video offering the full routine (4.3x her median).
- **Problem-product-result loops** from a professional (a cleaner) hit 418x her median with no talking.
- Music-only videos return no transcript; that's normal, not a failure. But a video with word-by-word spoken-style captions and no transcript (the blueberry gummy loop on the re-test) had speech that wasn't transcribed: read the words off the frames and say so.
- **A product-flaw hook from an educator:** "good serum, but do you know it can break you out?" over a pasted pack shot, one 150-second take, hit 67.9x the creator's median; 20 of 65 comments said a barrier product broke them out. Honest "who this isn't for" content works in this category.

## 4. Read the comments (what the hook started)

`collect_comments` on the 1 or 2 verified outliers with the most comments (1 credit each; free if collected before). It returns the most-liked comments the source gives, not all of them: 52 of 513, 51 of 1,168 and 67 of 834 in the tests. `comments_on_page` is the M and `stopped` says why it ended ("the source returned no more pages"): say "N read of M". Then `count_mentions` (free) on 4 to 7 themes.

Sort comments into: questions (content ideas), objections (things to answer), debate (two sides: keeps the video alive), buying intent ("link?", "where do I buy"), and jokes. Commenters are viewers, not buyers.

- Likes on a comment are agreement. A dismissive top comment can be the real finding: "If you need a gummy to take your creatine, just don't take it" had 8,051 likes on the creatine educator video.
- **Comment-bait inflates counts.** "Comment YES for the link", "dm me MUSCLE", "You asked?" posts get comments from automation: one skincare reel had 16,884 comments on 1,795,460 views. Never read those counts as debate.
- `count_mentions` matches whole words, and treats curly and straight apostrophes alike now ("just don't take it" found the 8,051-like comment written with a curly apostrophe). A hyphenated term may miss the spaced form ("acne-prone" 0, "acne prone" 4): give both.

## 5. Check it sells (when the user sells a product)

For physical products, `find_tiktok_shop_products` (1) then `get_tiktok_shop_product` on the top relevant product (1) lists the videos actually selling it, most viewed first. Compare their topics with the organic outliers. In the cleaning test, organic search was ASMR and DIY recipes; the videos selling Scrub Daddy's PowerPaste (15,701 units) were about one problem, hard water stains (6 of 15 captions), often as "Replying to @" comment answers (4 of 15). That gap is the most useful line in the report: film for the problem that sells, borrow the device that gets views.

For a format you plan to run as an ad, `find_competitor_ads` on the niche (1) shows whether anyone keeps paying for it (30+ days live).

## 6. Decide

| Decision | When |
|---|---|
| **Remake the format** | Verified lift 2x+, resonant, own voice, and it works without their face, claims or brand |
| **Answer it** | The outlier attacks the category or names a fear your product can disprove (lab result, demo, side-by-side) |
| **Ride it this week** | Posted in the last 14 days and still moving: react, stitch or reply now |
| **Partner the creator** | Verified lift on your topic, own voice, posts about it often: `get_creator` with contact, or a Spark ad with their code |
| **Test it as an ad** | A hook that won organically and matches a selling angle: 3 hooks on one body, paused |
| **Ignore** | Off topic, lottery accounts, comment-bait, paid seeding, stale |

## 7. Turn it into hooks and posts

Hooks follow creative-strategy §4 and copy-and-scripts §2: three layers written separately (spoken ≤12 words, on-screen 3 to 8 words, a visual action in frame 1). Each hook names its source video and says whether it's for **attention** (views) or **action** (sales) (Motion). Before showing a hook, run Motion's 5 tests: real friction, a nameable trigger, a complete thought, a voice a person would use, personal stakes. Drop failures; don't patch them. Never "Introducing", "Discover", "5 reasons" openers, or a hook any competitor could post.

Compliance (copy-and-scripts §5): no health, weight, income or guaranteed-result claims, no before/after promises for supplements or skincare, no naming a competitor as harmful, paid creators disclose. If an outlier relies on a risky claim (bone health, "cheat code", "fixes everything"), say so and leave it out.

**Measure each post against the account's own median views**, the same lift idea, not raw views. When the brand note gives the brand's TikTok or Instagram handle, `get_creator` on it (1 credit) gives that median (tested on a brand account: 3,379 over its last 10 posts, so a winner is about 6,800 after 48 hours). A post at 2x the account's median is a winner worth repeating; one under 0.5x after 48 hours is a format to drop (H).

## 8. The quality bar

Every finding is evidenced (a video link, a verified lift, a count with its base), non-obvious, and tied to a decision. Cut: lists of viral videos with no "so what"; "short videos do well"; calling views sales; quoting the tool's views-per-follower as the creator's usual reach; hooks that could fit any brand; detail dumps. If a finding doesn't change what the user posts or tests, it goes to the evidence section or is cut.
