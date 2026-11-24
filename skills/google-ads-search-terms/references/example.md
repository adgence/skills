# Worked example: search terms cleanup on DEMO data (23 Nov 2026)

**Read this first.** This is NOT a real account. It is the skill run on Adgence's demo file `apps/web/public/demo/myads_search_terms.json` ("Example Supplements Co.", illustrative numbers made for the product demo), through the skill's pasted-export path, because no Google Ads account was connected when the skill was built. The $30 target is an example input. Use it for the **shape** of the answer only: the verdict first, the four queues with match type, level, reason and risk, the odds of zero by luck, what a file can't show, and facts / inferences / missing. Never reuse its numbers. A run on a real connected account would add coverage, matched keywords and ad groups, existing negatives at every level, Keyword Planner sizes and a stage-two preview from `add_my_keywords`; that run has not happened yet.

---


*Search terms, 24 Oct to 22 Nov 2026, Example Supplements Co. (demo), from a pasted export: 8 searches, $1,082.00, 919 clicks, 50 conversions. Your target: $30 per conversion.*

**Before the numbers, three things this file can't tell me** (MISSING until your account is connected): how much of your Search spend these 8 searches cover (Google hides rare searches), which keyword and ad group each one matched, and which negatives you already have. The window also ends on 22 Nov; conversions from the last 3 days may still be arriving, so the newest days look worse than they are.

## 1. The verdict

- **$146.90 of $1,082.00 (13.6%) went to three searches that won't buy:** "side effects" research, a free-sample hunt and a recipe search. Block them as phrases.
- **One search earns its own keyword:** "best creatine gummies for women", 14 conversions at $16.54, about half your target.
- **Wait on "are creatine gummies a scam":** $44.10 and no sale yet, but 37 clicks is too few to call it (a normal search here would show zero sales by luck about 1 time in 8).

## 2. Negate (copy-ready; from a file I can't add them for you)

Your account converts 5.44% of clicks (50 of 919), so zero sales starts to mean something at about 55 clicks (3 ÷ 5.44%).

| # | Negative | Match | Where | Blocks | Cost | Clicks | Conv. | Reason | Risk |
|---|---|---|---|---|---|---|---|---|---|
| 1 | "side effects", "side effect" | Phrase | Shared negative list on all non-brand Search campaigns | creatine gummies side effects | $86.30 | 74 | 0 | Spend rule: 2.9x target, 74 clicks (over 55), about 4 sales expected at your rate, 1.6% chance of zero by luck | Low: research intent, but the numbers say no |
| 2 | "free sample", "free samples" | Phrase | Same shared list | creatine gummies free sample | $38.90 | 41 | 0 | Intent: free seeker. Not the single word "free", which would block "free shipping" searches | None |
| 3 | "recipe", "recipes" | Phrase | Same shared list | creatine gummies recipe | $21.70 | 29 | 0 | Intent: do it yourself | None, unless you ever sell a recipe or kit |

Plurals are listed because negatives don't catch close variants. In Google Ads: Tools, Shared library, Negative keyword lists, new list, paste, then apply it to the campaigns. If a problem belongs to one ad group only, connect the account and I can add it there myself after you approve the preview.

## 3. Add

| # | Keyword | Match | Ad group | Cost | Conv. | CPA vs $30 |
|---|---|---|---|---|---|---|
| 1 | [best creatine gummies for women] | Exact | The one that wins it now (MISSING from this file) | $231.60 | 14 | $16.54, 45% under |

If it's already a keyword, skip it. Search volume and bid range: MISSING without the account (Keyword Planner runs through it).

## 4. Watch

| Search | Cost | Conv. | CPA | What would move it |
|---|---|---|---|---|
| are creatine gummies a scam | $44.10 | 0 | n/a | Negate if it reaches $60 (2x target) with no sale. Meanwhile it's a trust question: does your ad or page answer it (reviews, guarantee)? (inferred) |
| buy creatine gummies near me | $63.80 | 2 | $31.90 | 6% over target on 2 sales: keep. "Near me" converts for you even online |
| creatine gummies vs powder | $97.40 | 3 | $32.47 | 8% over target: keep; add as a keyword only if it drops under $30 |

**Leave:** "creatine gummies" (31 conversions, $16.07): working.

## 5. What it would mean (inferred)

If the three blocked searches would have brought no sales, the same 30 days would have cost $935.10 for 50 conversions: $18.70 each instead of $21.64. In practice Google spends that budget on other searches, so read it as money freed, not money saved.

## 6. The evidence

- 8 searches read, all of the file. Too few for word-level (n-gram) analysis; at 100+ searches I'd also total cost per word.
- Account CVR 50 ÷ 919 = 5.44%; CPA $1,082 ÷ 50 = $21.64. Zero-sale searches together: $191.00 (17.7%); three negated, one watched.
- Chance of zero by luck = (1 − 5.44%)^clicks: 74 clicks 1.6%, 41 clicks 10.1%, 37 clicks 12.6%, 29 clicks 19.7%. Rows 2 and 3 are blocked on intent, not on these odds.
- **Facts:** the 8 rows above, 24 Oct to 22 Nov 2026, and the arithmetic on them.
- **Inferences:** the CPA if the blocked searches had brought nothing ($18.70, medium confidence: budget moves to other searches); "scam" searchers want reassurance (low confidence, from the words only).
- **Missing:** how many clicks and how much spend Google hid; matched keywords and ad groups; existing negatives (any of these three may already be blocked); which conversion actions count; Keyword Planner volumes; Performance Max. All need the account, and the first could change the waste share either way.

**Nothing has been added.** Connect Google Ads at https://adgence.ai/account/ad-accounts and I'll check these against what you already block and add the ad-group ones after you approve each preview.

**One question:** are these conversions sales, or leads that still have to close? If they're leads, I'd want your close rate before trusting the $16 searches.

**Next:**
- Connect Google Ads and rerun live: free.
- What competitors show on Google for these searches (their Search, YouTube and Shopping ads by domain): 1 credit each.
- Run this every 2 weeks as a scheduled task that only proposes: free.
