---
name: Embed Zenlytic for an external user
description: Mint a short-lived signed URL that renders Zenlytic content (dashboard, Explore, or Zoë chat) for an external end user, with row/column access scoped by user attributes.
api: openapi/zenlytic-embed-openapi.yml
operations: [createSignedEmbedUrl]
source: https://docs.zenlytic.com/embedding/signed_embedding
---

# Embed Zenlytic for an external user

Use this when a host application needs to show governed Zenlytic content to an
external user who does **not** have a Zenlytic login.

## Auth

- Endpoint: `POST https://api.zenlytic.com/api/v1/embed/signed_url`
- Authenticate with HTTP Basic: `Authorization: Basic <base64(client_id:client_secret)>`.
- `client_id` / `client_secret` are provisioned by your Zenlytic representative.
  Keep the secret server-side; never mint signed URLs from the browser.

## Steps

1. Call `createSignedEmbedUrl` with the required fields:
   - `external_user_id` — your stable identifier for the end user.
   - `target_url` — the full Zenlytic URL to embed (e.g. `https://app.zenlytic.com/chat`).
2. Scope access with optional fields:
   - `user_attributes` — drive row/column-level access grants.
   - `role_name` — one of `embed`, `embed_with_sql`, `embedded_with_scheduling`.
   - `extra_prompt_context`, `chat_header_message`, `chat_welcome_message`,
     `chat_initial_prompts`, `first_name`, `last_name` to tailor the Zoë chat surface.
3. Read `signed_url` from the response and load it in an iframe. Honor
   `expires_in` (seconds) — re-mint before expiry rather than caching long-lived URLs.

## Rules

- One documented endpoint; there is no list/pagination or idempotency-key
  contract (see `conventions/zenlytic-conventions.yml`).
- A `401` means missing/invalid client credentials.
- Prefer minting a fresh signed URL per session and per user; carry permissions
  in `user_attributes`, not in the target URL.
