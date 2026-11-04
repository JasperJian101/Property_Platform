# 0001: ASP.NET Core (C#) backend

- **Status:** Accepted
- **Date:** 2026-11-04

## Context

We need a backend API for listings, agents, enquiries, search indexing, background jobs and
billing. The web and mobile frontends are TypeScript.

## Decision

Use ASP.NET Core on the current .NET LTS release with C#, running in Linux containers. Use:
- PostgreSQL + PostGIS through EF Core with Npgsql and NetTopologySuite (Dapper for the busiest read queries)
- Hangfire for background jobs
- SignalR for real-time features

The API publishes an OpenAPI spec, and a TypeScript client is generated from it for the frontends.

## Alternatives considered

- **NestJS (TypeScript):** lets the frontends and backend share code directly and is fast for an MVP.
  Not chosen because the product owner prefers .NET, and .NET gives better performance and a large SA hiring pool.
- **Laravel / Django:** viable, but have no advantage over the two options above for this team.

## Consequences

- Types aren't shared directly with the frontends. We rely on generating the client from OpenAPI,
  and CI must fail when the generated client is out of date.
- The team needs both C# and TypeScript skills.
- Strong performance and a mature built-in platform (auth, DI, background services).
