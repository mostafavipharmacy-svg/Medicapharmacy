# API contract for a production backend (proposal)

This is a planning document only. No backend is included and the client demo does not call these endpoints. Serve exclusively over HTTPS. Use JSON, explicit schema versions, request IDs, rate limits, audit logs and consistent RFC 7807-style errors.

## Authentication / profile
- `POST /v1/auth/register` — validated account creation; consent and age/jurisdiction policy as required.
- `POST /v1/auth/login` — secure session/token issuance, MFA policy, throttling and generic failure responses.
- `POST /v1/auth/logout`, `POST /v1/auth/refresh` — revoke/rotate sessions; secure, HttpOnly, SameSite cookies preferred for browser clients.
- `GET /v1/me`, `PATCH /v1/me` — authenticated profile with strict allow-list; never store passwords in app data.
- `DELETE /v1/me` — verified account/data deletion request with audit and retention rules.

## Licensed content
- `GET /v1/medicines?query=&locale=&country=&cursor=` — paginated, permission-filtered records.
- `GET /v1/medicines/{id}?locale=&country=` — record with `contentVersion`, `status`, `updatedAt`, `sources[]` (license/source ID), `reviewedAt`, and `reviewerRole` (not a fabricated name).
- `GET /v1/medicines/{id}/interactions?with=` — only if governed, validated clinical data exists; include evidence, severity, population, jurisdiction, version and source. Missing evidence must not imply safety.
- `GET /v1/travel-packs/{country}?version=` — signed/versioned and licensed offline pack; expiry and data scope explicit.
- `POST /v1/content/reports` — report inaccurate/outdated content with traceable ticket ID.

## Admin workflow
- `POST /v1/admin/records`, `PATCH /v1/admin/records/{id}` — role-gated drafts only, schema validation and source/license metadata mandatory.
- `POST /v1/admin/records/{id}/review` — independent authorized reviewer; record decision, evidence, timestamp, and audit event.
- `POST /v1/admin/records/{id}/publish` / `POST .../withdraw` — controlled release/rollback and cache invalidation.

## Requirements
Do not migrate this prototype's fictional records as clinical facts. Establish RBAC, MFA for privileged users, TLS, encryption at rest, key management, monitoring, backups, privacy/data-retention rules and incident response. Define content provenance/licensing, pharmacist review and clinical safety validation before enabling real interaction checking. Validate authorization on every request and avoid exposing sensitive user notes in logs. Return field-level validation errors without leaking internal stack traces.
