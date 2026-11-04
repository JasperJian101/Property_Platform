# Competitor Analysis: Property24 and Private Property

Snapshot taken on 2026-11-04 by reading the homepage, the Cape Town for-sale results page and one listing
detail page on each site. Pages were read as converted text, so features loaded by JavaScript
(maps, some filter panels, contact widgets) may be missing. "Not seen" means not seen in what we read,
not confirmed absent.

## Property24 (property24.com)

**Navigation**
- **Buy:** for sale, commercial, repossessions, auctions, on show, retirement, developments
- **Rent:** houses, flats, commercial, retirement, developments, letting agents, private rental listings,
  tenant screening (TenantPlus)
- **Sell:** find agents, private listing, free valuation, sold house prices, seller guide
- **Calculators:** bond, affordability, additional payment, bond and transfer costs
- **Advice:** news, guides, trends and statistics, find attorneys, agent ratings and reviews
- **Other:** mobile apps (iOS and Android), Agent Zone (agent back office)

**Search filters:** property type, price min/max, bedrooms, bathrooms, parking/garages,
floor size, erf size, pet friendly, garden, pool, flatlet, retirement, repossessed, on show,
security estate/cluster, auction, exclude under offer, exclude sold.

**Results page:**
- Sort: default, price ascending/descending, most recent, property type, size
- Counts per property type
- Suburb sub-links for SEO and browsing
- Numbered pagination
- Email alerts sign-up
- New-development cards mixed in with the listings
- Badges such as "No transfer duty"

**Listing detail page:**
- Photo gallery with full-screen view
- Price, beds, baths, parking, erf size (converts to hectares and acres)
- Listing number and date
- Pets allowed, kitchens, outbuildings, flatlets, public transport, internet type, generator
- Features list and description
- **Street address hidden by default** ("Contact agent for street address")
- Agent card: show number, **WhatsApp agent**, enquiry form
- Bond calculator modal showing repayment, once-off costs and required income
- Share (WhatsApp, Facebook, X, email), print, brochure download, report listing
- Saving favourites requires login

**Observed issue:** the price in the header (R 3 499 900) differed from the price in the description
(R 3 499 999). Agents type the price twice, so the two can disagree.

## Private Property (privateproperty.co.za)

**Navigation**
- **Buy:** all, on show, commercial, developments, bank-assisted sales and repossessions, farms
- **Rent:** all, commercial, farms, letting agents, private rental listings
- **Sell:** with an agent, or privately
- **Advice:** news, neighbourhood guides, lifestyle, agent advice, buy-to-let, flipping
- **Tools:** property alerts, bond and affordability calculators, app
- **Home loans:** referral partners (BetterBond, ooba)
- **For agents:** login, list as an agent, advertise your agency

**Results page:**
- Residential / commercial / farm / developments switcher
- Area list and "add nearby areas" suggestions
- Numbered pagination
- Instant property alerts
- **Listing badges:** Promoted, New, Reduced, Under Offer, Back on Market
- (Filter and sort controls not seen; probably loaded by JavaScript)

**Listing detail page:**
- Photo gallery
- Price with a bond-cost link
- Beds, baths, parking, land size, floor size
- **Rates and taxes amount**
- Listing number and date
- **"Only on Private Property" exclusive badge**
- Detailed features: lounges, dining areas, study, alarm, electric fencing, security post, pool,
  patio, fireplace, built-in braai, aircon
- Street address hidden by default

## What both sites have (the basics we must match)

1. Location hierarchy (province → city/region → suburb) with SEO-friendly URLs and suburb pages
2. Buy/rent search with price, beds, baths, property type, size and feature filters
3. Results with sorting, counts, pagination and email alerts
4. Detail page: gallery, key facts, features, description, agent card, enquiry, share
5. Calculators (bond, affordability)
6. Agency and agent profiles, an agent back office, private seller listings
7. Mobile apps
8. Advice and news content for SEO

## Opportunities to stand out

| Opportunity | Why it matters in SA |
|---|---|
| **Backup power and water filters** (solar, inverter, battery, generator, borehole, water tank) | Load-shedding and water outages are real buying criteria. Property24 only records "generator" as a field. |
| **Fibre availability** as a filter, not just a free-text feature | Remote work makes this a deal-breaker |
| **One price field, used everywhere** | Avoids the price mismatch seen on Property24 |
| **Monthly cost transparency**: rates + levies + estimated bond on every listing | Buyers compare monthly affordability, not just the price |
| **WhatsApp-first enquiries** with lead tracking for agents | WhatsApp is the dominant channel in SA |
| **Data-light pages** (small images, fast on 3G/4G, installable PWA) | Mobile data is expensive |
| **Verified agents** (PPRA FFC check shown as a badge) | Builds trust; both sites show agents without a visible verification badge |
| **Price history and "Reduced" badges** from our own listing events | Cheap to build once listings are versioned |
