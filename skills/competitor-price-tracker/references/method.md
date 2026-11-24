# The price and launch watch method

How a senior ecommerce merchandiser and pricing analyst watches competitors' stores: what each change means, which ones deserve a reaction, and what the reaction is. Sources: Adgence's product-research notes (§3 price analysis, §3b reading a Shopify store) and competitor-analysis notes (§4 positioning, §6 weekly tracking), the practitioner workflows in the research (Negodiuk's six-hourly competitor checker, Alikhanyan's "the top three keep shuffling", the r/shopify "slow at competitor research" cluster), Helium 10's monitors, the SKILL.md files of Motion's competitor-watch (baseline, delta, status per competitor), Nexscope's competitor-price-tracker and competitor-price-analysis, Boring Marketing's competitive-intel (WATCH mode), FastMoss's skills (principles, competitor batch) and Corey Haines's competitor-profiling, and real Adgence runs on Gymshark, Crave, Create Wellness and Lululemon (23 Nov 2026). Thresholds marked (H) are heuristics: use them, and say they are.

## 1. What the data is, and what it can't say

### A Shopify store (`get_store`, `track` kind store)

The tool reads what every Shopify store publishes: the shop (name, currency, country), the catalog (up to 10,000 products), the best-selling order when the theme renders it, and the apps and pixels on the home page. Every read is saved, so the next read says what changed.

| Field | What it really is | What it can't say |
|---|---|---|
| price | The **lowest variant price** of a product | The price most buyers pay. A cheaper new size, or a single-unit variant, moves it without any "price change" by the brand |
| compare-at and discount_pct | The compare-at price of the **cheapest discounted variant**, which may not be the variant behind `price` | The headline sale. Create's "Core" gummies: price $60, compare-at $120, discount 4% (a $115 bundle variant against $120) |
| on_sale share | Products with any discounted variant, of the shoppable products priced (after `left_out_of_figures`) | Whether the sale is new. Read it against the brand's own normal level |
| sold_out | No variant available, among the products left in the figures | Whether it's a real line. Old lines the store keeps touching stay in: Gymshark still showed 5,557 sold out of about 7,500 counted on 23 Nov, and its `on_sale` list for "leggings" was all 2020 to 2024 sold-out lines at 70% off, some titled "DNU" (do not use) |
| left_out_of_figures | Products the feed lists but shoppers likely don't see, left out of prices, on_sale, sold_out, types and launches: tagged hidden, test, wholesale, no-index or duplicate; penny lines; sold out and untouched for 180 days | That every one is hidden. Tags are the store's own: Create's #1 best seller is tagged "wholesale" and its 45-count (best seller #6) "landing page dupe", so both are out of its figures while shoppers buy them. Check the best-seller list against it |
| products_read / products_published | How many were read / the store's own count | When read is under published, the catalog was cut (Gymshark: 10,000 of 10,423). Changes in the unread part are invisible |
| launches_by_month | Products by **created** date, last 12 months | When they went on sale. Big brands create products months ahead: Gymshark's AW26 leggings were created in May and June and **published** in September and October, so its launch counts stop in June while it kept launching. Read the newest products' published dates (listed products show both) before calling a brand quiet |
| best_sellers | The store's own best-selling order, **only when the theme shows it** | Anything, when the theme hides it: write MISSING. Gymshark hides it; Crave and Create show it |
| tags | The store's own tags | Proof. But tags often name plans before they're visible: Gymshark had 3,486 products tagged `bfcm-retail-2026` |
| apps_and_pixels | Apps found on the home page | Apps used only at checkout. **Intelligems price tests** means two shoppers may see different prices: any price you read may be one arm of a test |
| changes since the last read | New, removed, repriced, back in stock, sold out, new in the top 10 best sellers, against the last saved read of that store in this workspace | Why. And against which read: say its date |

Products that aren't products (shipping protection, gift cards, donations) are left out of the counts, and so are the products in `left_out_of_figures` (Create, 23 Nov: 13 tagged hidden, test or duplicate and 1 penny line of 77; Gymshark: 2,473 tagged of 10,000). No manual stripping is needed for price bands any more. Two checks remain yours: a real best seller dropped by its tags (Create's, above), and old sold-out lines still counted because the store keeps touching them (Gymshark's, above), which inflate sold_out and fill `on_sale` lists. For a big catalog read the competing line with `products: newest` and `search`, not `on_sale`. `best_sellers` lists keep every product in the store's own order, left out or not.

When the store refuses a direct read, the catalog comes through a second source and the result says so in its note: prices, stock, types and tags are complete, but **no compare-at prices and no launch dates**. A store read one way one week and the other way the next shows every discount as "ended". Check the note before calling a sale over.

A store that isn't on Shopify, or hides its catalog, can't be read or tracked ("doesn't look like a Shopify store"; nothing charged). Lululemon is one. For those, watch the hero products through Google Shopping (`find_products`, section 7) and their promotions through their ads (`track` kind ads).

### A TikTok Shop (`get_tiktok_shop`, `track` kind tiktok_shop)

- Units sold are TikTok's **lifetime** counts. Sales value is units times the lowest price: a floor, never revenue.
- A read lists the shop's best sellers (about 20 a page, 2 pages). Its products outside that list aren't seen, so a "new product" is new **to the list**, and a product that drops out isn't necessarily delisted.
- The shop's own `units_sold` counts everything it ever sold, including products no longer on sale: Create Wellness showed 44,393 units for the shop and 7,158 across its 5 listed products.
- **Sales per day need two reads, in practice a day or more apart** (the tool compares after an hour, but TikTok's counts move slowly: Create Wellness's 44,393 units and every product count were identical 64 minutes later, so the diff said 0 sold). Zero since a short gap means "too soon", not "not selling". The first read is a baseline; the second gives units sold per product since the first (`since_last_check` in `get_tiktok_shop`, `selling_now` in `whats_new`). A product with almost no lifetime units next to established ones is usually new (Create's Sour Green Apple gummies: 4 units, beside 3,785 for the lead product): call it "new or not selling", not a launch date.

### Tracking (`track`, `whats_new`)

- A store check runs once at setup and then on its schedule (stores default weekly). Each check costs 1 credit; checks of the same item on the same day are charged once.
- `whats_new` compares each item's latest check with its previous one by default, or with the check at or before the day you pass as `since` (else the first check). Only checks from tracking onward count, plus a read up to a minute before `track`. Right after setup, a store says `status: "only one check so far: changes show from the next one"`, like the TikTok kind: that is "no second check yet", not "nothing changed".
- **Every `get_store` read of a tracked store is a check too.** On 23 Nov Gymshark's `whats_new` compared its first check (14:41) with a leggings read ten minutes later (14:51), not with anything a week old. So read `compared.from` and `compared.to` before you say "this week", and for a weekly roll-up pass `since` as the last roll-up's day.
- So the **first roll-up comes from `get_store`'s changes since the last read**, when the workspace read that store before (earlier today, last month). Read the store before you track it: it's the baseline you write today.
- `whats_new` is compact by default: counts, up to 10 changes per list, new products with title and price. `detail: "full"` lists up to 30 per list and adds compare-at and published dates to new products: ask it for a store with many changes (a sale starting) or when a launch matters.
- **Fresh numbers between checks:** `change_tracking` with `run_now` runs one check now at the normal cost (1 credit per store; a second check of the same item on the same day isn't charged again; one per item per 10 minutes), keeps the schedule, and shows in `whats_new` a minute or two later.
- Alerts (Slack, email, the in-app list) fire at most once a day per item, and for stores only on **new products or price changes**. Sell-outs, removals and best-seller moves never alert: they show only in `whats_new`.
- `whats_new` with a tracking id doesn't show or mark the workspace's alerts (`mark_read: true` to show them). Without an id it hands over every unread alert in the workspace and marks them read (`mark_read: false` keeps them unread). In a shared workspace, read one item at a time with its id, unless the user wants the whole inbox.
- Every `get_store` read is saved alongside the tracking checks. Reading a tracked store between checks is fine for `whats_new` (it compares by date), but the next scheduled check's alert then compares with your read, not with the last check. A `get_tiktok_shop` read with `products: newest` isn't saved and doesn't count as a check.
- After 3 failed checks in a row an item is paused, and the user is told. `my_tracking` shows `problem` and `paused_because`.
- Plans limit how many things are tracked at once (`my_tracking` gives limit and used).

## 2. Set the baseline: read each store once, properly

The first read decides what "a change" means later. For each competitor, note:

1. **Their normal discounting.** Share on sale and median discount. Gymshark: 77% of products on sale at a median 30% off (23 Nov, on the figures after `left_out_of_figures`; the first read that day, counting hidden lines, said 82% at 40%); Create: 68% but a median 4% (bundle variants, not sales). Compare a later read with a baseline on the same basis: the figures changed when hidden lines started being left out. A store that's always on sale has a real price equal to its sale price (product-research §3). Judge every later move against this, never against zero.
2. **Their price architecture.** Entry, core and premium (min, quartiles, max, which already leave out penny, hidden and duplicate products), with the hero product's price per unit where the title gives the count.
3. **Their launch rhythm.** Launches per month for the last year, and the gap since the last one, checked against the published dates of the newest products (created and published can be months apart). A launch after a long gap matters more than one in a busy month.
7. **Colour and size markdowns.** When the same product sits at different discounts by colour (Gymshark's Whitney flare leggings: 30 to 40% off in most colours, 50% in Espresso Brown), the deepest colour is the one not selling (inferred, Medium): clearance, not a price move.
4. **Their stack.** Subscriptions (Recharge, Skio): repeat-purchase model; price moves hit subscribers last. BNPL (Klarna, Afterpay): higher order values. Intelligems: they test prices, so one read isn't their price. Rebuy: upsells and bundles at cart.
5. **What's queued.** Tags naming a season or sale (`bfcm-…`, `black-friday`, `holiday`), and products created but priced at a gift or bundle level (Crave's Christmas Box, created 14 Nov, $95 against a $192 compare-at).
6. **Where they sell besides their store.** Their TikTok Shop and marketplaces (section 7). Channel price gaps are often the most useful finding: Create's 45-count gummies were $27.99 on its own store (from $34.99), $24.99 at Target and GNC, and $45.49 for a 45-count flavour on its TikTok Shop.

## 3. Read each change: the change ladder

| Change | What it usually means | Confidence | Default call |
|---|---|---|---|
| **Regular price cut** (price down, no compare-at added) | A repositioning or a cost change | High once it holds over 2 checks; Medium on 1 | React only where the product competes with yours (section 5) |
| **Sale started** (compare-at added, or the share on sale jumps 10+ points (H)) | A promotion; usually time-boxed | High | Hold your price; answer with value or timing, not a matching discount |
| **Compare-at added, price unchanged** | Sale framing on the same price (an anchor) | Medium | Ignore for pricing; note it for their ads' claims |
| **Sale ended** (compare-at removed, price back up) | Promotion over. Or a change in how the catalog was read: check the note | Medium | Their buyers just lost a reason to buy now: a window for your offer |
| **Price rise** | Cost pressure, or confidence in demand | High once held over 2 checks | An opening: hold your price and say so in ads where you overlap |
| **New product** | A launch; new to the catalog | High that it's listed; Low that it's selling yet | Read what it is: a new price point, flavour, size or bundle. Track it 2 checks before judging |
| **New product above their P75** | A premium move | Medium | Check whether yours has the proof to follow |
| **New bundle, box or gift set** | An order-value play, usually seasonal | High | Compare per-unit value with your best bundle |
| **Removed product** | Unpublished: discontinued, seasonal, or hidden for stock | Low on 1 check | Say "removed from the store", never "discontinued", until it stays gone 2 checks |
| **Hero sold out** | A supply gap | High that it's unavailable now | Their buyers need an alternative this week: the strongest move in the list |
| **Back in stock** | Supply restored | High | Expect their ads and email to push it |
| **New in their top 10 best sellers** | Demand shifting to that product (theme-dependent) | Medium | Read what it is; it's their next ad focus |
| **TikTok units per day up** on one product | It's selling right now (creators or a promo) | High that units moved; Low on why | Find the videos and creators behind it (next moves) |

**Noise, not news** (H): a price move under 3% (rounding, currency, a variant); one product of thousands; a change on a penny or duplicate product; "removed" counts above 20% of a catalog in one check (a partial read: re-check before reporting); many "price changes" where only compare-at vanished (the second read source; see §1).

**Size** (H): 5 to 10% on a hero product is a real move; 15%+ is strategic; a store-wide sale is any jump of 10+ points in share on sale; a launch month at 2x their 12-month average is a drop.

**Removed is not failed** (Motion's competitor-watch says the same of stopped ads): a product leaves the store because it sold through, the season ended, it was replaced by a new version, or it flopped. Look for clues before guessing: a seasonal name, a new product with a similar title the same week, a sale that ended.

**Observed versus inferred** (Corey Haines's competitor-profiling): the change is observed; what it means is inferred. Write the meaning with "likely" and a confidence, and never state a motive as fact ("they're going upmarket"). One read can't support "hasn't changed": only two dated reads can.

**Patterns over weeks** (Nexscope's competitor-price-tracker names the same three): once a store has 4+ weekly checks, describe its promotion pattern by **frequency** (how often a sale starts), **depth** (median discount during sales against its normal) and **timing** (paydays, month end, seasons). A rival whose promotions are getting deeper and more frequent is either clearing stock or losing sales (a struggling signal, per Boring Marketing's competitive-intel). Two rivals cutting the same product in alternate weeks is a **price war**: the call is almost always to stay out and compete on value.

**Resellers below the brand's price:** when `find_products` shows the same product cheaper at resellers than at the brand (Lululemon's Align 25" at $98 from Lululemon, $59 for some colours at Editorialist), the market price isn't the list price. For a brand the user represents, that's a MAP (minimum advertised price) issue to raise; for a rival, it's where their buyers already shop for less.

**Status per competitor** (from Motion's competitor-watch): **Quiet** (nothing or noise), **Promoting** (sale started or deepened), **Launching** (new products), **Repricing** (regular prices moved), **Supply trouble** (hero sold out). One word per store at the top of the roll-up.

## 4. Context that changes the reading

- **Season.** Late November to Christmas, most stores promote. A sale then is the norm; no sale is the news. Tags reveal the scope before it starts.
- **Their normal.** A 30 to 40% sale at a store that's 77% on sale all year is business as usual.
- **Their channels.** A price cut on their store may follow a cheaper price at a retailer or on TikTok Shop, or the reverse.
- **Their ads.** A sale nobody advertises is clearing stock; a sale in their new ads is a push. If the user tracks their ads too, line them up.
- **Price tests.** With Intelligems on the store, confirm a move on the next check before reacting.

## 5. Decide: what the user does about it

Every change that passes section 3 ends in one decision:

| Decision | When | What it looks like |
|---|---|---|
| **Hold** | Their sale is time-boxed, or you don't share buyers on that product | Keep your price; answer with value: proof, guarantee, cost per serving, bundle |
| **Counter on value** | They cut on a product that competes with yours | A bundle or free gift that keeps your unit price; a "price per serving" comparison |
| **Match selectively** | A permanent cut of 10%+ (H) on a direct rival to your hero, held 2 checks | Match on one product or one size as a test, measured on conversion rate and margin, for 2 weeks |
| **Exploit** | Their hero sold out, their price rose, their sale ended | Push your ads and email at their buyers now; raise budget on the overlapping product |
| **Learn** | A launch, flavour, format or bundle you don't have | Watch it 2 more checks; if it climbs their best sellers or TikTok units, consider it |
| **Ignore** | Noise, products you don't compete with | One line in the evidence, or nothing |

Rules a pricing lead follows: never match a sale by reflex (it trains your buyers to wait); compare per unit, never per pack; match only where you share buyers; every price test has a measure (conversion rate, average order value, margin) and an end date. Price elasticity and the user's margin are MISSING unless the user gives them: say what you'd need.

## 6. Where a moved price now sits

For a hero product whose price moved, `find_products` (Google Shopping, 1 credit) on its plain name gives the market around it: the median, and each listing's price against it. Read "their $44 sits at about 150% of the $29.99 median" only per comparable pack: titles rarely give counts, so say when sizes differ. `find_products` with `amazon` (2 credits) adds "bought in past month" where Amazon shows it.

## 6b. TikTok Shop: read the engine, not only the units

TikTok sales are content-driven: new videos and creators lead units by about a week (FastMoss's principles). When a product's units per day jump, open it (`get_tiktok_shop_product`) and look at the videos selling it and their post dates before calling it a trend; a jump with one viral video is fragile, a jump with many new creators is a push.

## 7. Competitors that can't be tracked as a store

- **Not on Shopify, or a hidden catalog:** `find_products` on 1 to 3 hero products, re-run weekly by a scheduled task (1 credit each): their own listing's price, and resellers marking it down (Lululemon's Align 25": $98 from Lululemon, $59 for some colours at Editorialist). Plus `track` kind ads for their promotions.
- **TikTok Shop sellers:** `track` kind tiktok_shop by the shop's link (a name alone often fails; `find_brand_creators` lists a brand's TikTok Shop sellers with links).
- **Amazon:** `find_products` with `amazon` for their listing, re-run weekly.

## 8. Choose how often

- **Weekly** (default): enough for most categories; catalogs move in drops and promotions, not hours.
- **Every 3 days:** during a sale season (mid-November to Christmas) or a competitor launch.
- **Daily:** only a direct price war on a few stores; costs 7x weekly.
- Cost per week = items x checks per week x 1 credit. Five stores weekly: 5 credits a week, about 21 a month.

## 9. The insight quality bar

The value is in the change, not the state (Motion): "Crave launched a $95 Christmas Box at 51% off its compare-at" is intelligence; "Crave has 2 products" is a fact sheet. Every line in a roll-up is a change, its size against the competitor's normal, what it likely means with confidence, and a decision. Cut: lists of every repriced variant; "they're running a sale" in November without its depth against their norm; "discontinued" after one check; revenue from TikTok units; any price compared per pack when sizes differ. If a change doesn't change what the user does, it goes to the evidence or nowhere. A quiet week is one line.
