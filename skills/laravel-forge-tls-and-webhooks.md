---
name: laravel-forge-tls-and-webhooks
description: Issue and activate an SSL certificate for a Laravel Forge site domain, and subscribe an endpoint to Forge deployment webhooks.
api: openapi/laravel-forge-openapi.json
base_url: https://forge.laravel.com/api
generated: '2026-07-19'
method: generated
source: openapi/laravel-forge-openapi.json, asyncapi/laravel-webhooks.yml
operations:
  - organizations.index
  - organizations.servers.sites.index
  - organizations.servers.sites.domains.certificates.store
  - organizations.servers.sites.domains.certificates.index
  - organizations.servers.sites.domains.certificates.active
  - organizations.servers.sites.domains.certificates.actions.store
  - organizations.servers.sites.webhooks.store
  - organizations.servers.sites.webhooks.index
---

# Secure a Forge site and wire up deployment webhooks

## Before you start

- Bearer token auth, `Accept: application/json`, `Content-Type: application/json`.
- Required scopes for this flow include `site:meta` and `site:manage-deploys`.
- Resolve the organization slug first — all paths are `/orgs/{organization}/...`.
- Certificate issuance is asynchronous; expect `202 Accepted` and poll.

## Part 1 — TLS

1. **Find the site.** `organizations.servers.sites.index`
   (`GET /orgs/{organization}/servers/{server}/sites`). Filter with `filter[name]=` and sort with
   `sort=`; page with `page[size]` / `page[cursor]`.

2. **Request a certificate for a domain.** `organizations.servers.sites.domains.certificates.store`
   (`POST /orgs/{organization}/servers/{server}/sites/{site}/domains/{domainRecord}/certificates`).

3. **List certificates on that domain.** `organizations.servers.sites.domains.certificates.index`.
   Forge supports multiple certificates per domain with one active at a time, so use this to stage a
   replacement before switching over.

4. **Check what is live.** `organizations.servers.sites.domains.certificates.active`
   (`GET .../certificates/active`).

5. **Activate or otherwise act on a certificate.**
   `organizations.servers.sites.domains.certificates.actions.store`
   (`POST .../certificates/{certificate}/actions`).

## Part 2 — Deployment webhooks

6. **Subscribe an endpoint.** `organizations.servers.sites.webhooks.store`
   (`POST /orgs/{organization}/servers/{server}/sites/{site}/webhooks`).

7. **Verify the subscription.** `organizations.servers.sites.webhooks.index`.

8. **Handle the delivery.** After every deployment — successful or failed — Forge sends an HTTP `POST`
   with this documented payload:

   ```json
   {
     "status": "success",
     "server": { "id": 123, "name": "my-awesome-server" },
     "site": { "id": 456, "name": "my-awesome-site.dev" },
     "commit_hash": "382b0f5185773fa0f67a8ed8056c7759",
     "commit_url": "https://github.com/johndoe/my-awesome-site/commit/382b0f5185773fa0f67a8ed8056c7759",
     "commit_author": "John Doe",
     "commit_message": "deploying!"
   }
   ```

   **Laravel documents no signing or HMAC verification scheme for this webhook.** Treat the payload as
   untrusted: use it as a trigger, then re-read the deployment through the API before acting on it, and
   restrict the receiving endpoint by network or a shared secret in the URL you register.

## Error handling

Same envelope as the rest of the API — `{ "message", "errors" }` on `application/json`. `403` means a
missing scope, `422` returns per-field validation messages, `429` means you exceeded 60 requests/minute.

## Related

- Webhook catalog: `asyncapi/laravel-webhooks.yml`
- Conventions: `conventions/laravel-conventions.yml`
