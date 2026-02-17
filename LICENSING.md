# Nylo Licensing

Nylo uses a **dual-license model**: the core SDK is MIT-licensed (free for any use), while cross-domain identity features require a commercial license for production use.

## What is MIT (Free)

Everything needed for single-domain analytics:

- **Client SDK** (`src/nylo.js`) — event tracking, page views, clicks, forms, scroll depth, conversions, batching, retry logic, performance monitoring
- **Server integration** (`server/api/tracking.ts`) — batch event ingestion API
- **Input validation and security utilities** (`server/utils/`) — sanitization, XSS prevention
- **Examples** (`examples/`) — demo app, basic integration, server setup

You can use, modify, distribute, and sell products built with these components under the standard MIT License. See [LICENSE](LICENSE).

## What Requires a Commercial License

Cross-domain identity features covered by patent-pending claims:

| Feature | Files | Description |
|---------|-------|-------------|
| **WTX-1 Protocol** | `src/nylo.js` (CrossDomainIdentity module) | Cross-domain token exchange and verification |
| **WaiTag System** | `src/nylo.js` (Security.generateWaiTag), `server/api/waitag-tracking.ts` | Pseudonymous identifier generation and registration |
| **Cross-Domain Sync** | `server/api/tracking-sync.ts` | Identity synchronization across domains |
| **Encrypted Configuration** | `src/nylo.js` (parseEncryptedConfig) | AES-GCM encrypted feature toggles |
| **DNS Domain Verification** | `server/api/dns-verify.ts`, `server/utils/dns-verification.ts` | Domain ownership verification for cross-domain auth |

### When You Need a Commercial License

- Using cross-domain features in a product or service (paid or free)
- Internal business use processing more than 10,000 events/month
- Integrating cross-domain features into a proprietary analytics platform

### When You Don't

- Personal or non-commercial projects
- Academic research and education
- Evaluation and testing (non-production)
- Open-source projects with fewer than 10,000 monthly events
- Contributing back to Nylo

See [COMMERCIAL-LICENSE](COMMERCIAL-LICENSE) for full terms.

## Contact

For licensing questions, pricing, or enterprise inquiries:

**Email:** hello@waifind.com
