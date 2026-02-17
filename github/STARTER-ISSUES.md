# Starter GitHub Issues

Copy-paste these into GitHub Issues. Labels are suggested in brackets.

---

## Issue 1: Add Vitest unit tests for secure-id and WaiTag generation
**Labels:** `good first issue`, `testing`, `help wanted`

The `server/utils/secure-id.ts` module generates WaiTags, session IDs, and API keys using cryptographic randomness. We need unit tests to verify:

- WaiTag format matches `wai_<base36>_<random><hash>` pattern
- Session IDs are unique across 10,000 generations
- Generated IDs contain no PII or reversible information
- Edge cases: empty domain, very long domain, special characters

**Suggested setup:** Vitest with TypeScript support.

**Files to test:** `server/utils/secure-id.ts`

---

## Issue 2: Add Next.js App Router example
**Labels:** `good first issue`, `examples`, `help wanted`

Create an example showing how to integrate the Nylo client SDK into a Next.js 14+ App Router project:

- Load the SDK script in `layout.tsx` using `next/script`
- Track page views on route changes using `usePathname()`
- Show how to set up the server-side tracking API as a Next.js API route
- Include a README in the example directory

**Directory:** `examples/nextjs-app-router/`

---

## Issue 3: Add Cloudflare Workers ingestion example
**Labels:** `enhancement`, `examples`

Create a Cloudflare Workers script that implements the `/api/track` endpoint for edge-based event ingestion:

- Accept batch event payloads (both flat and compressed formats)
- Store events in Cloudflare D1 or KV
- Handle CORS headers
- Include `wrangler.toml` configuration

This would demonstrate that Nylo's server component isn't tied to Express/Node.js.

**Directory:** `examples/cloudflare-workers/`

---

## Issue 4: Add Playwright E2E tests for cross-domain clickthrough
**Labels:** `testing`, `enhancement`

Create end-to-end tests that verify the WTX-1 cross-domain token exchange:

- Set up two local domains (e.g., `localhost:3001` and `localhost:3002`)
- Navigate from domain A to domain B via a link
- Verify the `nylo_token` parameter is appended
- Verify the WaiTag is preserved on domain B
- Verify the token expires after 5 minutes

**Suggested setup:** Playwright with the demo server.

---

## Issue 5: Add TypeScript type definitions for the client SDK
**Labels:** `good first issue`, `enhancement`, `help wanted`

The client SDK (`src/nylo.js`) exposes a global `Nylo` object with methods like `track()`, `trackConversion()`, `getSession()`, `getMetrics()`, `getFeatures()`, `identify()`, `flush()`, and `destroy()`.

Create a TypeScript declaration file (`src/nylo.d.ts`) that types the full public API so TypeScript users get autocomplete and type safety.

**Files:** Create `src/nylo.d.ts`

---

## Issue 6: Document WaiTag privacy properties formally
**Labels:** `documentation`, `enhancement`

The README mentions four structural privacy guarantees (PII absence, non-reversibility, behavioral consistency, unilateral deletion). We need a standalone document that:

- Formally defines each property
- Provides a proof sketch or argument for each
- Maps properties to GDPR/CCPA requirements
- Discusses limitations and edge cases

**File:** `docs/PRIVACY-PROPERTIES.md`

---

## Issue 7: Add PostgreSQL storage adapter
**Labels:** `enhancement`, `examples`

Create a PostgreSQL storage adapter that implements the `NyloStorage` interface, similar to the SQLite reference adapter in `examples/storage-sqlite.js`:

- Use `pg` or `postgres` npm package
- Include a migration script to create tables
- Support connection pooling
- Include setup instructions

**Directory:** `examples/storage-postgres.js`

---

## Issue 8: Add event deduplication to batch ingestion
**Labels:** `enhancement`, `bug`

The current `/api/track` endpoint processes all events without checking for duplicates. If the SDK retries a failed batch (which it does with exponential backoff), the same events could be stored multiple times.

Implement deduplication using:
- A hash of `sessionId + eventType + timestamp` as a dedup key
- A short-lived in-memory or Redis-based dedup cache
- Configurable dedup window (default: 60 seconds)

**Files:** `server/api/tracking.ts`

---

## Issue 9: Add bundle size tracking to CI
**Labels:** `enhancement`, `devops`

Track the client SDK bundle size in CI to prevent regressions:

- Measure raw and gzip size of `src/nylo.js`
- Fail CI if size exceeds a threshold (e.g., 15KB raw)
- Report size in PR comments
- Use `bundlesize` or a custom script

**Files:** `.github/workflows/ci.yml`, `package.json`

---

## Issue 10: Add consent mode integration
**Labels:** `enhancement`, `privacy`

Add a consent-aware mode to the SDK that integrates with Consent Management Platforms (CMPs):

- Detect `__tcfapi` (TCF v2.0) or Google Consent Mode signals
- When analytics consent is denied, degrade to fully anonymous mode (no WaiTag, no identity persistence)
- When consent is granted, enable full tracking
- Provide a manual API: `Nylo.setConsent({ analytics: true/false })`

This is important for GDPR compliance in jurisdictions that require explicit consent even for pseudonymous tracking.

**Files:** `src/nylo.js`
