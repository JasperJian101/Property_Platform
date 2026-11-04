# Architecture & Tech Stack

A property listings platform for the South African market (sale and rental), delivered on web and mobile.

## Decisions

| Layer | Choice |
|---|---|
| Web | Next.js (React, TypeScript), SSR/ISR for SEO, installable PWA |
| Mobile | React Native + Expo (iOS & Android), native app sharing code with web, not a wrapper |
| Frontend monorepo | Turborepo + pnpm |
| Backend API | ASP.NET Core (.NET LTS, C#), Minimal APIs or controllers, OpenAPI |
| Data access | EF Core + Npgsql + NetTopologySuite; Dapper for hot read paths |
| Database | PostgreSQL + PostGIS |
| Search | Typesense or Meilisearch (OpenSearch at scale) |
| Background jobs | Hangfire (image processing, CRM feed imports, saved-search alerts) |
| Real-time | SignalR (enquiry chat, dashboard notifications) |
| Cache / rate limiting | Redis |
| Images | S3-compatible object storage + CDN, served as resized WebP/AVIF |
| Auth | ASP.NET Core Identity or external provider (JWT/OIDC), with phone OTP |
| Hosting | AWS af-south-1 (Cape Town) or Azure South Africa North (Johannesburg), Linux containers |

### Frontend ↔ backend contract

The .NET API publishes an OpenAPI spec. A TypeScript client is generated from it
(openapi-typescript / NSwag / Kiota) into `packages/api-client`, shared by the web and
mobile apps. Regenerate the client in CI when the spec changes, and fail the build if it drifts.

## Mobile approach

The mobile app is a **separate native app built with Expo**. It is not a WebView or
Capacitor wrapper around the Next.js site, and it is not a full rewrite either.

### Shared vs platform-specific

| Part | Shared? |
|---|---|
| API client (generated from the .NET OpenAPI spec) | Shared (`packages/api-client`) |
| TypeScript domain types (Listing, Agent, Suburb…) | Shared |
| Validation schemas (e.g. Zod) | Shared |
| Data fetching / caching hooks (TanStack Query) | Shared |
| Business logic (bond & transfer duty calculators, ZAR formatting, search filters) | Shared (`packages/core`) |
| Design tokens (colours, spacing, typography) | Shared (`packages/ui`) |
| Screens and UI components | Platform-specific |
| Navigation | Platform-specific (Next.js App Router / Expo Router) |

Libraries such as Tamagui, NativeWind or React Native Web may be used to share some
UI primitives, but screens are designed separately for web and native.

### Why not wrap the web app

- Apple frequently rejects "website in a wrapper" apps (App Store guideline 4.2).
- Wrapped sites feel like a website: weaker scrolling of image-heavy listings, map interactions and gestures.
- Push notifications, offline saved listings, camera uploads and biometric login are limited or awkward.
- Performance on lower-end Android devices, common in South Africa, is noticeably worse.

### Rollout

1. **MVP:** ship the Next.js site as an installable PWA so mobile users get a good experience from day one.
2. **Phase 2:** build the Expo app on top of the shared packages, focusing on what native does better:
   map search, push alerts for saved searches, saved listings, and agent tools (photo uploads, lead notifications).

## South Africa–specific integrations

- **Payments:** Paystack, PayFast or Peach Payments (cards, EFT, instant EFT)
- **Messaging:** WhatsApp Business API for enquiries and alerts; SMS (Clickatell/BulkSMS) for OTP; email via SES/Resend
- **Maps/geocoding:** Google Maps Platform (Mapbox as a cheaper tile option)
- **Listing feeds:** import from agent CRMs (e.g. Entegral, Propctrl, Base)
- **Compliance:** POPIA (consent, retention, deletion); PPRA Fidelity Fund Certificate (FFC) checks for agents
- **Domain:** ZAR, rates & levies, sectional title vs freehold, province → city → suburb hierarchy, bond and transfer duty calculators

## Proposed repository layout

```
apps/
  web/                 # Next.js
  mobile/              # Expo
packages/
  api-client/          # generated TypeScript client from the API's OpenAPI spec
  core/                # shared types, validation, data hooks, calculators, formatting
  ui/                  # shared design tokens and UI primitives
  config/              # shared tsconfig, eslint
backend/
  PropertyPlatform.sln
  src/
    PropertyPlatform.Api/            # ASP.NET Core host, endpoints, auth
    PropertyPlatform.Application/    # use cases, validation
    PropertyPlatform.Domain/         # entities: Agency, Agent, Listing, Lead, Suburb...
    PropertyPlatform.Infrastructure/ # EF Core, PostGIS, search, storage, messaging
    PropertyPlatform.Worker/         # Hangfire jobs
  tests/
docker-compose.yml     # postgres/postgis, redis, typesense for local dev
```

## Phasing

1. **MVP:** responsive Next.js web app (installable PWA) + API: listings, search, agent dashboard, enquiries (WhatsApp/email), saved searches.
2. **Phase 2:** native Expo app on the shared packages (push alerts, map search, saved listings, agent tools), CRM listing feeds, paid featured listings.
3. **Phase 3:** valuations/market data, rentals and tenant screening, developments.
