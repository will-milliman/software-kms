# Notes

Append-only change log maintained by `/pull`.

- 2026-04-22 — PR #376 [AB#89807: Enhance persistent cookie creation with customizable expiration](https://github.com/milliman-lts/integrate.SaaS.foundations/pull/376) by blairforce1: Hardens `CreatePersistentCookie` (explicit expiry/now args, 4096-byte cap, input validation) and adds `IssuerServiceOptions.RememberMeCookieExpiration` so the remember-me lifetime is configurable; adds `HttpResponseExtensionsTests`. — affects: `kms/integrate.SaaS.foundations/features/dev/data-protection-and-ticket-store.md`
