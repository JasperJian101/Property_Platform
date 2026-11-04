# 0002: Native Expo app with shared packages, PWA first

- **Status:** Accepted
- **Date:** 2026-11-04

## Context

Users must be served well on both web and mobile. The options were a PWA, wrapping the Next.js site
(Capacitor/WebView), or a native React Native app.

## Decision

- **MVP:** the Next.js site is an installable PWA.
- **Phase 2:** a native React Native + Expo app that shares the API client, types, validation, data hooks,
  business logic and design tokens with the web app. Screens and navigation are built per platform.

## Alternatives considered

- **Capacitor/WebView wrapper:** cheapest, but Apple often rejects apps like this (guideline 4.2).
  It also has weaker performance on low-end Android phones and limited native features.
- **PWA only:** no app store presence and limited push notifications on iOS.

## Consequences

- Mobile screens are built twice (web and native), but most of the non-UI code is shared.
- Shared code must live in `packages/*` from day one, not inside `apps/web`.
