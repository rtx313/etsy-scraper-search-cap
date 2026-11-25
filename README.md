# Etsy Scraper: What Etsy Actually Hands Over, How to Beat the Search Cap, and What You Should Pay (Buyer's Guide + Plan Picker)

Etsy's Open API requires an approved developer app, and even then it won't return search rank, sponsored placements, live cart counts, or the "bought in the last 24 hours" line. Those four signals are what most Etsy sellers and product researchers are actually chasing, and they exist only on the search page a shopper sees. So the real question isn't whether to use an Etsy scraper. It's which one, and what it charges you for.

ScraperAPI is the general-purpose route: you send it a URL, it handles the proxy layer, and you pay for the requests you use rather than a monthly seat. Its tiers and current monthly prices are listed on the pricing page. Below is how to work out which tier you need before you go look at the numbers, plus the field-level traps that decide whether your dataset is worth anything.

> **Quick pick:** Pulling Etsy search results at volume, or scraping Etsy alongside other sites? 👉 [Open ScraperAPI's pricing page and start on the smallest tier that covers a week of your real runs](https://www.scraperapi.com/?fp_ref=coupons). Under a few hundred listings a month, a row-billed marketplace actor will cost less.

## What an Etsy scraper can actually get you

The useful numbers live on the search results page, and the gap between a good tool and a cheap one usually comes down to whether it passes Etsy's own counters through untouched or rounds them on the way out.

**Cart counts.** Etsy shows shoppers a label like "In 20+ carts." The underlying integer, say 298, is what one well-built scraper returns as `inCartCount`. If a tool only ever gives you "20+", you can't rank a niche by demand, because everything above twenty collapses into one bucket.

**24-hour sales.** The `boughtLast24h` field is populated only when Etsy displays its "In demand. 7 people bought this in the last 24 hours" message. A null means Etsy showed no message, not that the listing sold zero. Reading null as zero will quietly push your best candidates to the bottom of the sheet.

**Shop-level sales.** Etsy publishes a shop's total sales counter but never a per-listing sales figure. The reliable way to get a daily number is to run the same shop or search every day and subtract yesterday's `shopSales` from today's. That gives you the shop's real day.

Which is exactly why per-listing sales numbers from subscription research tools are model output. Etsy doesn't publish them, so eRank, EverBee, Marmalead and similar tools estimate, and those estimates can miss in both directions and contradict each other on the same listing. If a number has to survive a budget meeting, use the shop counter and the cart count instead.

**Tags.** Verify this one before you buy. One scraper's documentation states plainly that Etsy no longer displays a listing's tags on the page, so they aren't in the output. Another advertises all 13 tags per listing with a full example array. Both can't be true at once, so whether tags come through depends on the implementation. If tag research is your main reason for buying, test it first on a handful of listings you can check by hand.

## The search cap: about 1,000 results, or about 250 pages

Etsy stops a keyword search at roughly a thousand results, and a scraper that just clicks "next page" stops there with it. One vendor works around this by splitting a broad keyword into price bands and running each band as its own search, which returns several thousand unique listings for a term as competitive as "necklace." Another describes the ceiling as closer to 250 pages and recommends narrower keywords plus price and ships-to filters instead.

The two accounts don't line up, which is your cue that the exact figure is tool-specific. What holds across both: paging alone hits a wall, and re-querying with narrower slices is how you get past it.

One setting matters if you track rank. Cross-query de-duplication, which saves each listing only once per run, is what you want when building a product list. Turn it off when tracking rank, or you lose each keyword's full ordered result list.

## Three ways to buy Etsy data

| Approach | What you get | How it bills | Published competitor rates | Watch out for |
| --- | --- | --- | --- | --- |
| Keyword-in scraper on a marketplace | Search rows plus optional detail fields, export to CSV, JSON or Sheets | Per 1,000 listings | Two Etsy scrapers on the Apify marketplace: $3.00 per 1,000 with full details falling to $2.10 on higher tiers, $1.00 to $0.70 list-only; and $8 per 1,000 with details, $2 per 1,000 search-only | Detail fields usually cost one extra request per listing |
| URL-in API | You pass a product or search URL, get parsed JSON or Markdown back | Per request | Not published in the documentation for the one I checked | You build pagination and price-band splitting yourself; that repo's sample response is dated 2022, so field names may have moved since |
| Monthly research dashboard | Charts, keyword tools, some modeled sales figures | Monthly seat | Varies by tool | You only see what the tool chooses to show, and exports are limited |

Across the market, Etsy scraping lands somewhere between $1 and $8 per 1,000 listings, and most of that spread is about whether detail fields are included. These are competitors' published rates and they change; confirm before you budget against them.

## Where a general-purpose API fits

A keyword-in scraper is purpose-built for Etsy and hands you Etsy's fields already mapped. A general-purpose API like ScraperAPI gives you the fetching layer and leaves the decisions to you: which URLs to hit, how to paginate, which fields to parse. More setup, more control, and the right shape if Etsy is one of several sites you monitor or if the data has to land directly in your own pipeline.

Because you pay per request, your bill scales with request count. Search pages cost roughly one request per page; detail data costs roughly one more request per listing, and that second part is what inflates invoices. Two habits keep it flat:

- Pull search rows first, shortlist, then fetch details only for the listings you'll actually analyse.
- Track rank daily with search-only runs and pull full details weekly.

### Sizing a tier before you look at the price list

| What you run | Shape of the load | Do this first |
| --- | --- | --- |
| One-off niche scan | A few thousand listings, once | Run 100 listings, note requests consumed, multiply |
| Daily rank tracking | Keywords × result pages × 30 days | Leave details off; they dominate the bill |
| Competitor shop monitoring | Same shop pages daily, diff `shopSales` | Confirm the fetcher returns the shop counter at all |
| Multi-country pricing | Same query repeated per `shipTo` country | Multiply request count by country count |

👉 [Compare ScraperAPI's tiers and their request allowances](https://www.scraperapi.com/?fp_ref=coupons) once you have that number. Pick the smallest tier that covers a week of your real workload rather than a hypothetical month.

Two things to test on a small run before committing to any tier: whether your target Etsy pages come back complete instead of as an anti-bot interstitial, and how many requests a single listing genuinely costs you. A hundred-URL run settles both before you've spent anything meaningful.

## Legal and data protection, briefly

Scraping public Etsy pages that anyone can see without signing in is the baseline every tool in this category claims, and it's a reasonable one. Two things sit outside it. Shop owner names and buyer names inside reviews are personal data, which may fall under GDPR and comparable laws, so scrape them only with a legitimate reason and a plan for storing them. Photos and descriptions are copyrighted and can't be republished just because you scraped them. Etsy's own terms apply to your use no matter which tool you pick, and none of these tools are affiliated with or endorsed by Etsy, Inc.

One more thing worth knowing: Etsy personalises results for signed-in users, rotates ad placements on every page load, and changes results by country. Scrapers query as an anonymous shopper in a specified `shipTo` country, so rank positions will differ slightly from what you see in your own browser. That's expected, not a bug to chase.

## Picking one

- Need exact cart counts, 24-hour sales messages, and raw rows you can pivot yourself → a keyword-in scraper with detail fields, or a general-purpose API if you also scrape other sites.
- Need a few hundred listings a month → the cheapest row-billed plan; a monthly API tier won't pay for itself.
- Need dashboards and never want to touch JSON → a research subscription, with every per-listing sales number treated as an estimate.
- Need one product page as JSON inside your own pipeline → a URL-in API.

Before you pay, confirm two things in writing: whether detail fields are billed as a separate request on top of the base rate, and what concurrency or throughput the cheapest tier allows. That cap decides whether a daily run finishes in minutes or blocks your morning.

👉 [Start with a small ScraperAPI run against your own keyword list](https://www.scraperapi.com/?fp_ref=coupons) and let the measured request count pick the tier for you.
