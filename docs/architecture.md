# Architecture & Tech Stack

A property listings platform for the South African market (sale and rental), delivered on web and mobile.

## Decisions

| Layer | Choice |
|---|---|
| Web | Next.js (React, TypeScript), SSR/ISR for SEO, PWA |
| Mobile | React Native + Expo (iOS & Android) |
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
  ui/                  # shared UI primitives / design tokens
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

1. **MVP:** responsive Next.js web app + API: listings, search, agent dashboard, enquiries (WhatsApp/email), saved searches.
2. **Phase 2:** Expo app (push alerts, map search), CRM listing feeds, paid featured listings.
3. **Phase 3:** valuations/market data, rentals and tenant screening, developments.
