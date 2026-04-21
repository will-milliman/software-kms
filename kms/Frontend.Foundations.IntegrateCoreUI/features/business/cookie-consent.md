# Cookie Consent Banner

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A framework-free cookie-consent banner published as `@integrate-core-ui/cookie-consent` and deployed as a UMD script to Milliman's `legalassets` Azure blob, so any Milliman LTS web property can include it via a single `<script>` tag.

## Behavior

- On first visit, the banner appears until the user clicks Accept.
- On Accept, a `cookie_consent` cookie is set with a one-year expiry; the banner dismisses.
- The UMD bundle auto-initializes on `DOMContentLoaded` — the host page needs no JavaScript wiring.
- The UMD URL is case-sensitive (`cookieConsent.js`); the ESM entry is available for apps that prefer to import it (e.g., sandbox-01).

## Entry Points

- `packages/cookie-consent/src/index.ts:1` → `./cookie-consent`.
- `packages/cookie-consent/src/cookie-consent.ts:1` — exports `initCookieConsent()`.
- `packages/cookie-consent/vite.config.mts:54` — UMD footer that invokes `initCookieConsent()` on `DOMContentLoaded`.
- `pipelines/deploy.yml:157` — `Deploy_CookieConsent` stage (Azure File Copy to `legalassets/$web`).

## Key Dependencies

- None — no runtime or peer dependencies. Pure DOM manipulation.
- Azure Blob Storage (`legalassets` / `$web`) — hosting target.

## Related Features

- [ADO Deploy Pipeline (dev)](../dev/ado-deploy-pipeline.md)

## Change Log

- 2026-04-21: Seeded.
