# 0001: Agent creates and manages listings

- **Status:** Draft
- **Features:** F-03 Listing management, F-04 Listing photos
- **Owner:** Product owner (to confirm)
- **Last updated:** 2026-11-04

## Summary

A verified estate agent can create a residential listing for sale or to rent, add photos, and
publish it. Afterwards they can edit the listing and move it through its life (under offer, sold or
let, withdrawn). This is the supply side of the platform: search, detail pages, enquiries and alerts
all depend on the listing data defined here.

## Context

- Both Property24 and Private Property are fed mainly by agents (see
  [competitor analysis](../research/competitor-analysis.md)). We launch agent-only and add private
  sellers in Phase 2 (F-23).
- The listing model must support the search filters planned for F-05. That includes SA-specific
  differentiators: backup power, water backup and fibre.
- Property24 showed a header price that disagreed with the price in the description. We store
  **one** price field and never ask agents to type it twice.

## User stories

- As an **agent**, I want to create a listing as a draft and finish it later, so that I can capture
  details on site and complete them at the office.
- As an **agent**, I want to upload, reorder and choose a cover photo, so that the listing looks its best.
- As an **agent**, I want to publish a listing once it's complete, so that buyers and tenants can find it.
- As an **agent**, I want to mark a listing as under offer, sold/let or withdrawn, so that buyers
  aren't shown properties that are no longer available.
- As an **agent**, I want to change the price, so that the platform can show a "Reduced" badge
  and keep a price history.
- As an **agency principal**, I want to see and manage all listings in my agency, so that I can
  cover for agents who are away or have left.

## Scope

**In scope**
- Residential listings: **for sale** and **to rent**
- Property types: House, Apartment/Flat, Townhouse, Cluster, Duplex, Simplex, Vacant land,
  Garden cottage/Flatlet, Farm (residential)
- Create, save as draft, edit, publish, change status, delete a draft
- Photo upload, reorder, cover photo, delete
- Recording price changes and status changes as listing events (for "New", "Reduced" and
  "Back on market" badges and future price history)
- Listings from all agents in an agency are visible to the agency principal

**Out of scope** (covered by later specs)
- Public search and listing detail pages (F-05, F-06)
- Enquiries and leads (F-07)
- Agent onboarding and FFC verification (F-02). This spec assumes a verified agent already exists.
- Commercial, industrial and development listings (F-26, F-27)
- Floor plans, videos and virtual tours
- Bulk import from CRM feeds (F-22)
- Paid promotion (F-24)
- On-show scheduling (F-25)

## Functional requirements

### FR-1: Listing fields

**Core**

| Field | Type | Required to publish | Rules |
|---|---|---|---|
| Listing type | Sale / Rent | Yes | Can't change after first publish |
| Property type | Enum (see scope) | Yes | |
| Title | Text | Yes | 10–80 characters |
| Description | Text | Yes | 50–5 000 characters; plain text with line breaks; no phone numbers, emails or URLs (blocked so contact goes through tracked enquiries) |
| Suburb | Reference to location (F-01) | Yes | Must be an existing suburb |
| Street address | Text | Yes | Stored for verification and map position; **hidden from the public by default** |
| Show street address publicly | Boolean | No | Default `false` |
| Map position | Point (lat/lng) | Yes | Geocoded from the address; agent can adjust the pin; must fall inside South Africa |
| Mandate type | Sole / Open / Unknown | No | Internal; not shown publicly |

**Price (sale)**

| Field | Type | Required | Rules |
|---|---|---|---|
| Asking price | Integer ZAR | Yes | R 10 000 – R 1 000 000 000 |
| Price on application (POA) | Boolean | No | If true, price is still stored (for search ranges) but not displayed. *See open question Q2.* |
| No transfer duty | Boolean | No | For VAT-inclusive new builds |

**Price (rent)**

| Field | Type | Required | Rules |
|---|---|---|---|
| Monthly rent | Integer ZAR | Yes | R 500 – R 1 000 000 |
| Deposit | Integer ZAR | No | |
| Available from | Date | No | Today or later |
| Lease term (months) | Integer | No | 1–60 |
| Furnished | No / Partially / Fully | No | |

**Monthly costs**

| Field | Type | Required | Rules |
|---|---|---|---|
| Rates and taxes (per month) | Integer ZAR | No | |
| Levies (per month) | Integer ZAR | No | Prompted for Apartment, Townhouse, Cluster, Duplex, Simplex |

**Size and rooms**

| Field | Type | Required | Rules |
|---|---|---|---|
| Bedrooms | Decimal (0, 0.5, 1, 1.5 …) | Yes, except vacant land | 0–50; half values allowed (e.g. 2.5) |
| Bathrooms | Decimal (0.5 steps) | Yes, except vacant land | 0–50 |
| Garages | Integer | No | 0–50 |
| Covered/open parking bays | Integer | No | 0–100 |
| Floor size (m²) | Integer | No | 5–100 000 |
| Erf / land size (m²) | Integer | Yes for House, Vacant land, Farm | 10–100 000 000 |

**Features** (all optional, boolean unless stated)

| Group | Features |
|---|---|
| Living | Study, Lounges (int), Dining areas (int), Flatlet, Fireplace, Built-in braai, Aircon, Patio/Balcony |
| Outdoor | Garden, Pool, Pet friendly |
| Security | Security estate / complex, 24-hour security, Alarm, Electric fencing, Access control |
| **Backup power** | Solar panels, Inverter, Battery backup, Generator |
| **Water** | Borehole, Water tank(s) |
| **Connectivity** | Fibre available |

### FR-2: Photos

- Formats: JPEG, PNG, WebP, HEIC. Maximum 20 MB per file. Minimum 800 × 600 px.
- Maximum **40 photos** per listing. At least **3** are needed to publish.
- The first photo is the cover by default. The agent can choose a different cover and drag to reorder.
- Uploads go straight to object storage using a pre-signed URL. A background job then:
  - strips EXIF data (including GPS location)
  - corrects the orientation
  - creates WebP and AVIF versions at 320, 640, 1 024 and 1 600 px wide
- The photo shows as *processing* until its versions are ready. A failed photo shows an error and a retry button.
- An optional caption per photo, up to 120 characters.

### FR-3: Status lifecycle

```
          publish                mark under offer           mark sold / let
 Draft ──────────────► Active ─────────────────► Under offer ───────────────► Sold / Let
   │                    │  ▲                         │
   │ delete             │  └──── back on market ─────┘
   ▼                    │ withdraw
 (deleted)              ▼
                     Withdrawn ──── relist ────► Active
```

- **Draft:** not visible to the public. It can be incomplete, and it can be deleted permanently.
- **Publish:** allowed only when every field required to publish is valid and at least 3 photos have
  finished processing. On first publish, the system sets `first_published_at`.
- **Active** and **Under offer:** visible to the public (F-05/F-06), with Under offer shown as a badge.
- **Sold / Let:** a final status. Not shown in default search; kept for history and agent stats.
- **Withdrawn:** hidden from the public. It can be relisted, which returns it to Active.
- A published listing **can't be deleted**, only withdrawn. This keeps an audit trail.
- **Back on market** (Under offer → Active) records an event so search can show a "Back on market" badge.
- Active listings must be confirmed by the agent every **90 days**. Unconfirmed listings are
  withdrawn automatically after a 7-day email warning. *See open question Q4.*

### FR-4: Editing and listing events

- Agents can edit any field on Draft, Active, Under offer and Withdrawn listings.
  The listing type (sale or rent) can't change after first publish.
- Each published listing gets a public **listing number** that never changes. *See open question Q6.*
- These actions record a listing event (`created`, `published`, `price_changed`, `status_changed`,
  `relisted`), each with the time, who did it, and the old and new values:
  - every price change
  - every status change
- "Reduced" badge rule (applied in F-05): the price dropped within the last 30 days.
- "New" badge rule (applied in F-05): first published within the last 7 days.

### FR-5: Permissions

| Action | Listing agent | Other agent in the same agency | Agency principal | Platform admin |
|---|---|---|---|---|
| Create listing | Yes (for own agency) | — | Yes (and assign to any agent in agency) | No |
| View/edit/change status | Yes | No | Yes | Yes (for moderation) |
| Reassign to another agent | No | No | Yes | Yes |

- Only agents whose FFC is verified and in date can **publish**. Unverified agents can still create drafts.
- A listing can have one primary agent and up to one co-listing agent from the same agency.

## API contract (sketch)

All endpoints need an authenticated agent unless stated otherwise. Routes are versioned under `/api/v1`.

| Method | Path | Purpose |
|---|---|---|
| POST | `/api/v1/listings` | Create a draft (body: listing type, property type and any fields so far) |
| GET | `/api/v1/listings?status=&agentId=&page=` | List the caller's listings (or the agency's, for principals) |
| GET | `/api/v1/listings/{id}` | Get one listing, including the private fields |
| PATCH | `/api/v1/listings/{id}` | Update fields (partial update) |
| DELETE | `/api/v1/listings/{id}` | Delete a draft (409 if it has ever been published) |
| POST | `/api/v1/listings/{id}/publish` | Validate and publish (422 with a per-field list of errors if incomplete) |
| POST | `/api/v1/listings/{id}/status` | Change status (body: target status); 409 if the change isn't allowed |
| POST | `/api/v1/listings/{id}/confirm` | Confirm the listing is still available (resets the 90-day clock) |
| POST | `/api/v1/listings/{id}/photos` | Request a pre-signed upload URL; returns the photo id and URL |
| POST | `/api/v1/listings/{id}/photos/{photoId}/complete` | Mark an upload as finished; queues processing |
| PATCH | `/api/v1/listings/{id}/photos/order` | Set the photo order and cover photo |
| DELETE | `/api/v1/listings/{id}/photos/{photoId}` | Remove a photo |
| GET | `/api/v1/listings/{id}/events` | Listing event history |

Errors use RFC 9457 Problem Details. Validation errors are keyed by field name so the web form can
show them next to the right input.

## UI notes (agent back office, web)

- **My listings:** a table or card list with filters by status and agent, showing cover photo,
  title, suburb, price, status, days on market, and the date it next needs confirming.
- **Listing editor:** one page with sections (Basics, Location, Price and costs, Size and rooms,
  Features, Photos, Description).
  - Saves automatically as the agent works.
  - A "Ready to publish" checklist lists what's missing.
  - Mobile-friendly: agents often capture listings on site from their phones.
- **Location:** suburb autocomplete, then address autocomplete (Google Places), then a draggable map pin.
- **Photos:** drag-and-drop or camera upload, a progress bar for each photo, drag to reorder,
  and a star to choose the cover.
- **Status actions:** clear buttons that only offer the changes allowed from the current status, with a
  confirmation for Sold/Let (it's final).
- Prices are displayed as `R 3 499 900`. Inputs accept spaces and commas and strip them.

## Acceptance criteria

**Drafts**
- **AC-1:** Given a verified agent, when they create a listing with only listing type and property type, then a Draft is saved and returned with an id.
- **AC-2:** Given a Draft, when the agent deletes it, then it is permanently removed.
- **AC-3:** Given a listing that has ever been published, when the agent tries to delete it, then the API returns 409 and the listing is unchanged.

**Publishing**
- **AC-4:** Given a Draft missing required fields or with fewer than 3 processed photos, when the agent publishes, then the API returns 422 listing every missing or invalid field, and the status stays Draft.
- **AC-5:** Given a complete Draft, when a verified agent publishes, then the status becomes Active, `first_published_at` is set, a listing number is assigned, and `created` and `published` events are recorded.
- **AC-6:** Given an agent whose FFC is unverified or expired, when they try to publish, then the API returns 403 with a reason, and they can still edit the draft.

**Validation**
- **AC-7:** Given a description containing a phone number, email address or URL, when the agent saves or publishes, then validation fails with a message explaining that contact goes through enquiries.
- **AC-8:** Given a map position outside South Africa, when saved, then validation fails.
- **AC-9:** Given a House listing, when the erf size is missing, then publishing fails; given Vacant land, bedrooms and bathrooms are not required.
- **AC-10:** Given a listing type that has been published, when the agent tries to change sale ↔ rent, then the API returns 422.

**Status changes**
- **AC-11:** Given an Active listing, when the agent changes status, then only the changes shown in FR-3 are allowed; any other returns 409.
- **AC-12:** Given an Under offer listing, when it is moved back to Active, then a `status_changed` event with `back_on_market = true` is recorded.
- **AC-13:** Given an Active listing, when the price is lowered, then a `price_changed` event with old and new values is recorded.

**Photos**
- **AC-14:** Given a photo upload, when processing completes, then the original's EXIF data (including GPS) is removed and WebP and AVIF versions exist at 320, 640, 1 024 and 1 600 px.
- **AC-15:** Given a listing with 40 photos, when the agent requests another upload URL, then the API returns 422.
- **AC-16:** Given a file that isn't JPEG/PNG/WebP/HEIC, is over 20 MB or is under 800 × 600 px, then the photo is rejected with a clear error.
- **AC-17:** Given a reorder request, when saved, then the photo order and cover are persisted and the first photo in the order is the cover unless another is starred.

**Permissions and privacy**
- **AC-18:** Given an agent in agency A, when they request a listing from agency B or another agent's listing in A, then the API returns 404.
- **AC-19:** Given an agency principal, when they list listings, then they see every listing in their agency, and they can reassign a listing to another agent in the agency.
- **AC-20:** Given a listing with "Show street address publicly" off, when its public data is produced (the shape used by F-05/F-06), then the street address and exact map position are excluded and the map position is rounded to roughly 200 m.

**90-day confirmation**
- **AC-21:** Given an Active listing not confirmed for 83 days, then the agent receives a warning email; after 90 days without confirmation, the listing becomes Withdrawn and a `status_changed` event records the reason `auto_expired`.

## Non-functional requirements

- **Performance:** listing editor API calls p95 < 300 ms (excluding photo upload). Processed photo
  versions ready within 60 s for 95% of uploads.
- **Security:**
  - All endpoints check that the caller belongs to the listing's agency (no guessable ids;
    ids are UUIDv7 internally, listing numbers are public).
  - Pre-signed upload URLs expire after 15 minutes and are limited to one object key and a maximum size.
- **Privacy (POPIA):**
  - Photo EXIF data is stripped.
  - Street addresses are private by default.
  - Agent personal contact details are not part of listing data; they come from the agent profile (F-02).
- **Accessibility:** the editor is fully usable with a keyboard; every input has a label; validation
  errors are announced to screen readers; meets WCAG 2.2 AA.
- **Observability:** publish, status-change and photo-processing failures are logged with the listing id
  (never with personal data) and counted in metrics.

## Dependencies

- **F-01 Location hierarchy:** suburbs must exist (seed data).
- **F-02 Agency and agent accounts:** agents, agencies and FFC verification status. Until F-02 is built,
  seed test agencies and agents directly.
- Object storage and CDN set up; a Google Maps Platform key for geocoding.

## Open questions

- [ ] **Q1.** Should published listings be reviewed by an admin before going live, or go live
  immediately and be moderated only when reported? *Suggestion: immediately, with reporting
  (F-12), for speed.*
- [ ] **Q2.** Is "Price on application" allowed? Property24 and Private Property both mostly show
  prices, and POA weakens search. *Suggestion: allow it for sale listings above R 10 million only.*
- [ ] **Q3.** Should agents be able to list in any province, or only near their agency's area?
- [ ] **Q4.** Is the 90-day confirmation rule (with automatic withdrawal) acceptable? Stale listings
  are a common complaint about portals.
- [ ] **Q5.** Should we capture the mandate type and expiry date, and should the expiry date
  withdraw a listing automatically?
- [ ] **Q6.** Listing number format: a prefix plus a number (e.g. `PP-1234567`)? What's the platform's name or prefix?
- [ ] **Q7.** Are the photo limits (minimum 3, maximum 40) right for the market?
- [ ] **Q8.** Should the rental fields include "Pets allowed" separately from "Pet friendly", plus
  utilities (water and electricity included, prepaid electricity)?
