# ScraperAPI Ultra Premium: 30 Credits per Request, 75 With Rendering, and How to Check the Bill Before You Commit

`ultra_premium=true` costs **30 API credits per successful request**, counted against your monthly allowance. Add `render=true` and the same request costs **75**. It is available on paid plans only, and requests carrying the flag are cached by default, which matters a great deal if you are scraping prices or stock levels.

The rest of this page is the bill behind those two numbers: what the parameter switches on compared with `premium=true`, how many ultra_premium requests each plan's credits actually cover, how to price one URL before you send fifty thousand of them, and the three documented limits that catch people who buy first and read the docs afterward.

> **Quick rule:** standard pool (1 credit) for normal pages, `premium=true` (10 credits) for sites that need residential or mobile IPs, `ultra_premium=true` (30 credits) only for targets ScraperAPI itself describes as "really tough." 👉 [See current ScraperAPI plans and credit allowances](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## What you'd be running it on: current plans

| Plan | Credits/month | Concurrent threads | Geotargeting | Price (monthly / annual) | Link |
| --- | --- | --- | --- | --- | --- |
| Hobby | 100,000 | 20 | US & EU | $49 / $44.10 | [ Choose Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | US & EU | $149 / $134.10 | [ Choose Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | 3,000,000 | 100 | Country-level | $299 / $269.10 | [ Choose Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | Country-level | $475 / $427.50 | [ Choose Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | Country-level | $975 / $877.50 | [ Choose Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | Country-level | $1,975 / $1,777.50 | [ Choose Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Country-level | Custom | [ Request an Enterprise quote](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

Annual billing is 10% below monthly on every self-serve tier. Credits reset at each renewal and don't roll over. Pay-as-you-go is available from Scaling upward, so Hobby, Startup and Business accounts get a 429 error when credits run out instead of an overage charge. The free tier is 1,000 credits a month with 5 threads, and the 7-day trial gives 5,000 credits without a card. Neither is a paid plan, so by the docs' wording neither can run `ultra_premium`.

## What `ultra_premium=true` actually switches on

There are three proxy tiers behind the same endpoint. The standard pool draws on "millions of proxies from over a dozen ISPs" and, in the docs' words, "should be sufficient for the vast majority of scraping jobs." Below that sits a private internal pool of residential and mobile IPs, open to all paid users and reached with `premium=true`. Above it is "a higher premium level that can be used on really tough target websites," reached with `ultra_premium=true`.

| What you send | Credits per successful request | Paid plan required | Notes |
| --- | --- | --- | --- |
| Nothing extra | 1 | No | Standard pool |
| `premium=true` | 10 | Yes | Residential/mobile pool |
| `premium=true` + `render=true` | 25 | Yes | Rendered through premium proxies |
| `ultra_premium=true` | 30 | Yes | Cached by default |
| `ultra_premium=true` + `render=true` | 75 | Yes | Cached by default |

Two details in that table are easy to skim past. `premium=true` plus rendering is **25** credits, not 20, because the docs price the combination at "25 times the normal rate" rather than adding two surcharges. And `ultra_premium` with rendering is **75**, which is 2.5 times the 30-credit rate and 75 times what an unflagged plain page costs. Rendering is the expensive half of the upgrade, not the proxy tier.

## How far each plan's credits actually go

Divide the allowance by the per-request cost and the numbers get uncomfortable fast.

| Plan | Credits/month | ultra_premium (30) | ultra_premium + render (75) |
| --- | --- | --- | --- |
| Hobby | 100,000 | ~3,333 | ~1,333 |
| Startup | 1,000,000 | ~33,333 | ~13,333 |
| Business | 3,000,000 | ~100,000 | ~40,000 |
| Scaling | 5,000,000 | ~166,666 | ~66,666 |
| Professional | 10,500,000 | ~350,000 | ~140,000 |
| Advanced | 21,500,000 | ~716,666 | ~286,666 |

Treat every figure as a ceiling. The docs also list domain-level costs (normal pages 1 credit, Amazon/Walmart/eBay 5, Google and Bing 25, LinkedIn 30) and bot-protection bypass costs (10 credits for Cloudflare, Cloudflare Turnstile, DataDome, PerimeterX/HUMAN and Akamai). How those combine with parameter costs isn't spelled out anywhere I could verify, so don't add them up by hand and assume you've got the total. A rendered ultra_premium run at 50,000 requests a month needs 3.75 million credits, which puts you on Scaling; the same volume on Hobby would be a rounding error against a 100,000 allowance.

👉 [Pick the plan whose credit ceiling matches your monthly volume](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## Price one URL before you commit a month of credits

The docs give two ways to see what a request will cost.

The `sa-credit-cost` response header comes back on every request and reports the credit cost of that specific request. Run one real target, read the header, and you know your true per-request number including whatever surcharges applied.

The `/account/urlcost` endpoint on the API host accepts all ScraperAPI parameters, which the docs describe as "making it easy to understand the exact cost before applying them to your requests." Feed it your target URL with `ultra_premium=true&render=true` and whatever else your job needs, and budget against the answer rather than against the marketing page.

This is worth ten minutes if you're scraping a category. One hard domain at 75 credits a request can eat a Hobby plan in an afternoon, and the header tells you that before the invoice does.

## Three limits that aren't in the pricing table

**Every ultra_premium request is cached by default.** The docs state that requests with the parameter "are cached (by default) to enhance performance and efficiency," and the caching page confirms the feature applies only to requests that include `ultra_premium=true` and is on by default. For a one-off pull from a hostile site, this is fine. For price monitoring, inventory checks or anything where the current page is the point, a cached reply is a stale answer that still costs 30 credits. Caching is described as on by default and the docs point elsewhere for the details, so confirm how it behaves on your target before you build a recurring job on top of it.

**Your custom headers get discarded.** `keep_headers=true` costs nothing extra, but it doesn't survive `ultra_premium`. ScraperAPI's own n8n integration documents `keepHeaders` as "not available with ultraPremium" and states that custom headers are "Discarded when ultraPremium is enabled." If your target needs an Authorization header, an API key or a specific Referer, ultra_premium strips it and you get failures that look exactly like blocking. Debug the configuration before you blame the proxy pool.

**Discounted accounts pay a higher credit rate for ultra_premium.** The docs are explicit: accounts on custom deals with discounted pricing "will have a higher credit cost per request for our Ultra Premium domains to meet our minimum pricing of $3 per one thousand requests without rendering and $7 with rendered pages." Your negotiated discount doesn't travel to the top tier. The credits get adjusted so the effective price never drops below those floors. If you're on a custom contract and heavy on hard targets, get the ultra_premium credit rate confirmed in writing before you scale a job.

👉 [Compare plans before you commit to a monthly credit allowance](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## When ultra_premium is the wrong call

**Start at 1 credit.** The docs say the standard pool covers the vast majority of scraping jobs. Send the request without flags first and look at what comes back. Plenty of "hard" sites aren't.

**Sites that only need residential IPs should stop at `premium=true`.** Ten credits, or 25 with rendering, is a third of the ultra_premium rate. If the block is IP reputation rather than something more aggressive, the middle tier does the job.

**If rendering is your problem, fix rendering.** `render=true` alone is 10 credits. Turning on ultra_premium to solve a JavaScript problem costs three times as much for a proxy tier you didn't need.

**Escalate one tier at a time.** Premium first, ultra_premium only on the specific domains where premium still fails. The gap between 10 and 30 credits, and between 25 and 75 with rendering, is the whole budget conversation.

## FAQ

### Can I use ultra_premium on the free plan?

No. The docs mark it as available with paid plans only, and the same asterisk appears in the credit cost table. The free tier and the 7-day trial aren't where you'll test it, which is worth planning around: budget one paid month to benchmark your hard targets rather than assuming the free credits will tell you.

### What does ultra_premium cost with JavaScript rendering?

75 credits per successful request, against 30 without rendering. The combination is priced as a bundle, the same way `premium=true` plus rendering lands at 25 rather than 20.

### Why do I keep getting the same response for a page that changes?

The default cache on ultra_premium requests. It's on unless something changes it, and it applies only to requests carrying that parameter, so the same URL without `ultra_premium=true` won't behave the same way.

### How do I know the cost before sending the request?

Send it once and read the `sa-credit-cost` response header, or query the `/account/urlcost` endpoint with your full parameter set. Both report actual credit cost, including surcharges the pricing tables describe separately.

## The decision, in one pass

Normal page: no flags, 1 credit. Site needs residential or mobile IPs: `premium=true`, 10 credits, 25 with rendering. Site is one ScraperAPI calls "really tough" and premium already failed: `ultra_premium=true`, 30 credits, 75 with rendering, paid plan only, cached by default.

Before you pay, confirm two things. Which allowance covers your monthly volume at 30 or 75 credits a request, since Hobby tops out around 1,333 rendered ultra_premium calls. And whether your targets need rendering at all, because that single flag is what turns a 30-credit request into a 75-credit one.

👉 [Start with the plan that matches your volume](https://www.scraperapi.com/pricing/?fp_ref=coupons)
