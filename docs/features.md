# Feature Roadmap

Based on [research/competitor-analysis.md](research/competitor-analysis.md). Each feature gets an
ID (`F-nn`) and, before it is built, its own spec in [specs/](specs/).

**Strategy:** launch agent-supplied residential sale and rental listings first. Listings attract buyers,
and buyers attract more agents. Private sellers, paid features and the mobile app come once the core loop works.

## Phase 1: MVP (web + PWA)

| ID | Feature | Notes | Spec |
|---|---|---|---|
| F-01 | Location hierarchy | Province → city/region → suburb, slugs for SEO URLs, seeded reference data | — |
| F-02 | Agency and agent accounts | Agency profile, agents in an agency, FFC number captured, admin verifies; public agent/agency pages | — |
| F-03 | **Listing management** | Agent creates, edits, publishes and changes status of residential sale/rental listings | [0001](specs/0001-agent-manages-listings.md) |
| F-04 | Listing photos | Upload, reorder, cover photo, resized WebP/AVIF versions via a CDN | Part of 0001 |
| F-05 | Search and results | Buy/rent, location, price, beds, baths, type, parking, sizes, key features incl. **backup power/water and fibre**; sort; counts; pagination; badges (New, Reduced, Under Offer) | — |
| F-06 | Listing detail page | Gallery, key facts, **monthly costs (rates + levies + estimated bond)**, features, description, agent card, share, report | — |
| F-07 | Enquiries and leads | Enquiry form, WhatsApp click-to-chat, "show number" (all tracked); agent lead inbox; email notifications | — |
| F-08 | Buyer accounts | Email or phone OTP sign-in; saved listings | — |
| F-09 | Saved searches and alerts | Email alerts (instant or daily) for new and reduced listings | — |
| F-10 | Calculators | Bond repayment, affordability, transfer duty and bond costs | — |
| F-11 | SEO foundations | Server-rendered pages, suburb landing pages, sitemaps, schema.org structured data | — |
| F-12 | Admin back office | Verify agents, moderate reported listings, manage location data | — |
| F-13 | POPIA basics | Consent capture, privacy policy, cookie notice, account and data deletion | — |
| F-14 | Installable PWA | App manifest, offline fallback, data-light images | — |

## Phase 2: growth

| ID | Feature |
|---|---|
| F-20 | Native Expo app (map search, push alerts, saved listings, agent tools) |
| F-21 | Map search and draw-an-area search |
| F-22 | Listing feed import from agent CRMs (e.g. Entegral, Propctrl) |
| F-23 | Private seller and landlord listings (paid) |
| F-24 | Promoted/featured listings and agency subscriptions (Paystack, PayFast or Peach Payments) |
| F-25 | On-show (open house) scheduling and filter |
| F-26 | New developments (developer profiles, unit types) |
| F-27 | Commercial, industrial and farm listings |
| F-28 | Agent dashboard analytics (views, enquiries, conversion) |
| F-29 | Price history on listings |
| F-30 | Advice and news content (CMS) |

## Phase 3: differentiation and revenue

| ID | Feature |
|---|---|
| F-40 | Automated valuations, sold prices and area trends (data partner, e.g. Lightstone) |
| F-41 | Tenant screening for landlords and letting agents |
| F-42 | Auctions, repossessions and bank-assisted sales |
| F-43 | Agent ratings and reviews |
| F-44 | Home loan referral partners (e.g. ooba, BetterBond) |
| F-45 | Attorney and service-provider directory |
| F-46 | Neighbourhood guides (schools, transport, amenities) |
