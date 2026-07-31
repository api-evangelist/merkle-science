---
name: Screen a VASP for counterparty due diligence
description: Look up a Virtual Asset Service Provider in Merkle Science KYBB and pull its off-chain KYC/AML, licensing, and regulatory-risk profile.
api: openapi/merkle-science-kybb-openapi.yml
operations:
- get-list-of-vasp-entities-1
- get-vasp-details
---

# Screen a VASP for counterparty due diligence

Use the Merkle Science KYBB API to find a Virtual Asset Service Provider (VASP) and
retrieve the off-chain data you need for counterparty due diligence.

## Auth
- Send every request over HTTPS with the header `x-api-key: <YOUR_KEY>`.
- Keys are issued directly by Merkle Science (contact support@merklescience.com).
- Sandbox host: `https://api.kybb.dev.merklescience.com/` — Production host: `https://api.kybb.app.merklescience.com/`.

## Steps

1. **Find the VASP** — call `get-list-of-vasp-entities-1`
   (`GET /api/v1/entity/`). Filter with query params such as `entity_name`,
   `entity_type`, `regulatory_permission_countries`, `restricted_countries`, or
   `supported_countries`. Page with `limit` and `offset`; the response carries
   `count`, `next`, `previous`, and `results[]`. Grab the `slug` of the matching entity.

2. **Pull the full profile** — call `get-vasp-details`
   (`GET /api/v1/entity/{slug}/`) with the slug from step 1. The response returns the
   entity's `kyc` posture, `regulatory_alerts`, `regulatory_permissions`,
   `legal_entities`, `assets`, `jurisdiction` restrictions, `media_links`, and `notes`.

3. **Assess risk** — evaluate `kyc.kyc_is_mandatory` / `kyc.conducts_aml`, any
   `regulatory_alerts`, `permission_status` on `regulatory_permissions`, and any
   `jurisdiction[].is_restricted` entries before onboarding the counterparty.

## Conventions & errors
- Pagination is offset/limit (see `conventions/merkle-science-conventions.yml`).
- Errors are a JSON `{ "detail": "..." }` envelope: `401 Invalid API Key.` (fix the
  `x-api-key` header) and `404 Not found.` (unknown slug). See
  `errors/merkle-science-problem-types.yml`.
