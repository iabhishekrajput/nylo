# Nylo as a Regulated-Industry Complement to Adobe Analytics

## The Problem Adobe Customers Face Today

Adobe Analytics relies on the Experience Cloud ID (ECID) service for cross-domain tracking. The ECID architecture depends on two cookies:

- **AMCV cookie** (first-party) — stores the ECID/MID for the current domain
- **demdex.net cookie** (third-party) — synchronizes the ECID across domains

When third-party cookies are blocked — which is already the default in Safari and Firefox, and imminent in Chrome — the demdex.net cookie stops working. The result:

| Adobe Capability | Status Without Third-Party Cookies |
|---|---|
| Cross-domain visitor stitching | Broken |
| Experience Cloud segment sharing across domains | Broken |
| Adobe Target cross-domain personalization | Broken |
| Audience Manager cross-domain sync | Broken |
| Single-domain tracking | Still works |

### Adobe's Current Workarounds

1. **`appendVisitorIDsTo()`** — Passes the ECID via URL search parameters. Limitations:
   - Only works across same second-level domains (e.g., `www.abc.com` → `shop.abc.com`)
   - Does NOT work across unrelated domains (`abc.com` → `xyz.com`)
   - Requires manual implementation on every cross-domain link
   - ECID is visible in server logs and browser history (privacy risk)

2. **Customer IDs (Authenticated Identity)** — Ties multiple ECIDs together via login. Limitations:
   - Requires the user to log in — impossible for anonymous visitors
   - Doesn't work for pre-authentication journeys
   - Adds friction that reduces conversion rates

3. **First-Party Device IDs (FPIDs)** — Custom device identifiers via Web SDK. Limitations:
   - Still scoped to a single domain
   - Safari ITP caps first-party cookies at 7 days
   - Doesn't solve cross-domain identity

4. **CNAME implementation** — Routes tracking through the customer's own domain. Limitations:
   - Safari ITP treats CNAME cookies identically to JavaScript-set cookies (7-day cap)
   - Doesn't solve cross-domain tracking
   - Requires Adobe Client Care involvement

**Bottom line:** Adobe has no production-ready solution for anonymous cross-domain identity preservation in a cookieless world.

---

## How Nylo Solves This

Nylo was built from the ground up for a world without third-party cookies. It doesn't retrofit workarounds — it was designed for this reality.

### Architecture Comparison

| Capability | Adobe ECID | Nylo WTX-1 |
|---|---|---|
| Cross-domain identity | Requires third-party cookie (demdex.net) | Hash-fragment token exchange (no cookies) |
| Unrelated domain support | No (same eTLD+1 only) | Yes (any domain with DNS verification) |
| Anonymous visitor tracking | Requires login for cross-domain | Works without login or PII |
| Safari ITP resilience | 7-day cookie cap, demdex blocked | Three-layer storage with graceful degradation |
| Token privacy | URL search params (sent to server, logged) | URL hash fragments (client-only, never sent to server) |
| Token expiry | No built-in expiry | Configurable expiry with server-side verification |
| Domain authorization | Organization-level (same Adobe Org) | Per-domain DNS TXT record verification |
| PII dependency | ECID is classified as personal data under GDPR | WaiTags contain no PII by design |
| Consent requirements | Requires consent as personal data processing | Stronger argument for legitimate interest |
| Bundle size | ~50KB+ (VisitorAPI.js + AppMeasurement.js) | ~12KB (zero dependencies) |

### How WTX-1 Works

1. User visits Domain A → Nylo generates a WaiTag (pseudonymous identifier from timestamp + crypto random + domain hash)
2. User clicks link to Domain B → Token is appended to the URL hash fragment (`#nylo_token=...`)
3. Domain B's Nylo SDK reads the hash (never sent to server), verifies the token with the Nylo backend
4. Identity is preserved across domains without cookies, login, or PII

### Production-Ready Security

- **Rate limiting** — 100 requests/IP/minute with `X-RateLimit` headers and 429 responses
- **Security headers** — HSTS, CSP, X-Content-Type-Options, X-Frame-Options, X-XSS-Protection, Referrer-Policy, Permissions-Policy
- **GDPR anonymous mode** — SDK degrades to fully anonymous tracking (no WaiTag, no storage) when consent is denied via `Nylo.setConsent({analytics: false})`
- **DNS domain verification** — Domain ownership proven via DNS TXT records before cross-domain identity is allowed
- **Client-side input sanitization** — HTML entity encoding and 1,000-character limits on tracked string fields

---

## The Regulated Industry Angle

Healthcare, financial services, and government organizations face the strictest constraints:

| Requirement | Adobe ECID | Nylo |
|---|---|---|
| No PII in analytics identifiers | ECID is personal data (GDPR Art. 4) | WaiTags contain no PII |
| No third-party cookie dependency | Depends on demdex.net | Zero cookie dependency |
| Consent-mode degradation | Limited (GA4-style consent mode) | Built-in anonymous mode with full API |
| Data residency control | Adobe-hosted (US/EU data centers) | Self-hosted (your infrastructure) |
| Audit trail | Adobe-controlled | Full source code, self-hosted logs |
| HIPAA-compatible analytics | Requires BAA with Adobe | Self-hosted, no PII, no BAA needed |

### Specific Use Cases

**Healthcare (HIPAA)**
- Track patient journeys across hospital website, patient portal, and appointment booking — without collecting PHI
- No Business Associate Agreement required because no PII is processed
- Self-hosted on the organization's own infrastructure

**Financial Services (GLBA/SOX)**
- Track customer journeys across banking, investment, and insurance domains
- No personally identifiable financial information in the analytics pipeline
- DNS-verified domain authorization prevents unauthorized cross-domain linking

**Government (FedRAMP/Section 508)**
- Cross-agency analytics without sharing PII across agency boundaries
- Self-hosted within government cloud (GovCloud, IL4/IL5)
- Zero dependency on third-party SaaS for identity resolution

---

## Integration Path with Adobe

Nylo isn't positioned as a replacement for Adobe Analytics — it's a **complement** that fills the cross-domain identity gap Adobe can't solve without cookies.

### Proposed Architecture

```
Adobe Analytics (single-domain tracking, reporting, segmentation)
        +
Nylo WTX-1 (cross-domain identity layer)
        =
Full cross-domain analytics without third-party cookies
```

### How It Would Work

1. Nylo SDK runs alongside Adobe's Web SDK or AppMeasurement
2. Nylo handles cross-domain identity via WTX-1 protocol
3. When identity is resolved, Nylo passes the unified WaiTag to Adobe as a Customer ID:
   ```javascript
   visitor.setCustomerIDs({
     "nylo_waitag": {
       "id": Nylo.getSession().waiTag,
       "authState": Visitor.AuthState.LOGGED_OUT
     }
   });
   ```
4. Adobe Analytics now has a cross-domain identifier without requiring login or third-party cookies
5. All Adobe reporting, segmentation, and Target personalization works across domains

### Value Proposition for Adobe

- **Solves a problem Adobe can't solve alone** — anonymous cross-domain identity without cookies
- **Doesn't compete with Adobe** — Nylo has no reporting, segmentation, or dashboards
- **Strengthens Adobe's position in regulated industries** — where cookie-based solutions are being rejected
- **Revenue opportunity** — Nylo's commercial license can be bundled or recommended by Adobe for enterprise accounts

---

## Licensing

- **Core tracking:** MIT License (free for personal and commercial use)
- **Cross-domain features:** Commercial license for production use, free for personal/academic/evaluation
- **Patent:** U.S. Non-Provisional Patent Application filed for cross-domain identity technology
- **Research:** Peer-reviewed paper submitted to *Decision Support Systems* (IF 6.8)

## Contact

**Email:** hello@waifind.com
