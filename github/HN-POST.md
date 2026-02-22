# Hacker News Post Draft

## Title

Show HN: WTX-1 – An open protocol for cross-domain analytics without cookies or fingerprinting

## URL

https://github.com/nickolaev/nylo/blob/main/github/WTX-1-SPEC.md

## Text

Third-party cookies are dead. Safari killed them in 2017, Firefox in 2019, Chrome is following. If you run analytics across multiple domains (hospital system + pharmacy portal, bank + investment app, multi-brand retail), you've lost cross-domain visitor tracking.

The existing alternatives are problematic:
- Fingerprinting works but is ethically and legally toxic
- Login-based identity excludes anonymous visitors (most of them)
- Adobe ECID only works across subdomains of the same eTLD+1
- Privacy Sandbox is Chrome-only and advertising-focused

WTX-1 is an open protocol that solves this differently. It generates a pseudonymous identifier (WaiTag) using crypto.getRandomValues — no PII, no hardware signals, no cookies. When a user navigates cross-domain, the token transfers via URL hash fragment (never sent to the server, never logged). The destination domain verifies it server-side. DNS TXT records authorize which domains can participate.

Key design decisions:
- Hash fragments for token transport (never included in HTTP requests to the destination server, invisible to proxies and access logs; token is then verified via a dedicated API call)
- DNS-based domain authorization (no centralized registry)
- Full anonymous mode when consent is denied (identify() becomes a no-op, WaiTag is null)
- Zero-dependency client SDK, single file

The spec is at the link above. Reference implementation: https://github.com/nickolaev/nylo

Built for regulated industries (healthcare, finance, government) where fingerprinting is a non-starter and users don't always log in. But the protocol is general-purpose.

Would love feedback on:
1. Is hash fragment transport sufficient, or are there edge cases we're missing?
2. How do you read WaiTags under GDPR's "personal data" definition?
3. Are there other cross-domain identity approaches we should consider?

MIT licensed (core tracking). Cross-domain features have a commercial license for production.

---

## Posting Notes

- Post on a weekday, ideally Tuesday-Thursday between 8-10am ET for maximum visibility
- The "Show HN" prefix is important — it triggers HN's Show section which gets dedicated attention
- Keep the text concise — HN readers will click through to the spec
- The three questions at the end invite discussion, which HN's algorithm rewards
- Be ready to respond to comments quickly in the first hour — engagement velocity matters
- Potential objections to prepare for:
  - "This is just link decoration" — Emphasize the DNS authorization, server verification, and consent model that distinguish it from naive URL parameter passing
  - "WaiTags are still personal data under GDPR" — Acknowledge the debate, point to anonymous mode as the conservative fallback
  - "Why not just use first-party data?" — Explain that first-party data doesn't work across unrelated domains without PII exchange
  - "Hash fragments break SPAs with hash routing" — Point to the search parameter fallback
