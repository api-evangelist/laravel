---
name: laravel-forge-provision-and-deploy
description: Provision a Laravel Forge server, create a site on it, configure the deploy script and run a deployment, then poll the deployment to completion.
api: openapi/laravel-forge-openapi.json
base_url: https://forge.laravel.com/api
generated: '2026-07-19'
method: generated
source: openapi/laravel-forge-openapi.json, conventions/laravel-conventions.yml
operations:
  - organizations.index
  - organizations.servers.store
  - organizations.servers.show
  - organizations.servers.sites.store
  - organizations.servers.sites.deployments.script.update
  - organizations.servers.sites.deployments.store
  - organizations.servers.sites.deployments.index
  - organizations.servers.sites.deployments.show
---

# Provision a Forge server and deploy a site

## Before you start

- Authenticate with `Authorization: Bearer <token>`. Tokens are created at
  https://forge.laravel.com/profile/api with an explicit scope selection.
- Send `Accept: application/json` and `Content-Type: application/json` on every request.
- The scopes this flow needs are `server:create`, `site:create` and `site:manage-deploys`.
- Every resource path is scoped to an organization slug, so resolve the slug first.
- Success bodies are `application/vnd.api+json` with `data` / `meta` / `links`. Error bodies are plain
  `application/json` shaped `{ "message": ..., "errors": { field: [...] } }`.
- **There is no idempotency key on this API.** Do not blind-retry a failed `POST` — re-read the
  collection first to check whether the resource was already created.

## Steps

1. **Resolve the organization.** Call `organizations.index` (`GET /orgs`) and take the slug of the
   organization you are working in. Every subsequent path is `/orgs/{organization}/...`.

2. **Create the server.** Call `organizations.servers.store`
   (`POST /orgs/{organization}/servers`). Provisioning is asynchronous and returns `202 Accepted`.

3. **Wait for readiness — politely.** Poll `organizations.servers.show`
   (`GET /orgs/{organization}/servers/{server}`) until the server reports ready. Forge documents that
   provisioning takes roughly ten minutes and explicitly asks integrators to poll about **once every
   two minutes**, not tightly. Respect the 60 requests/minute limit and back off on `429` using
   `X-RateLimit-Reset`.

4. **Create the site.** Call `organizations.servers.sites.store`
   (`POST /orgs/{organization}/servers/{server}/sites`).

5. **Set the deploy script.** Call `organizations.servers.sites.deployments.script.update`
   (`PUT /orgs/{organization}/servers/{server}/sites/{site}/deployments/script`).

6. **Deploy.** Call `organizations.servers.sites.deployments.store`
   (`POST /orgs/{organization}/servers/{server}/sites/{site}/deployments`). Deployments are limited to
   ten minutes and fail automatically past that.

7. **Track the deployment.** List with `organizations.servers.sites.deployments.index` and read a
   single deployment with `organizations.servers.sites.deployments.show`. Use
   `include=` to pull related resources in one call, and `page[size]` / `page[cursor]` to page the
   deployment history.

## Error handling

| Status | Meaning | What to do |
|---|---|---|
| 401 | No valid token | Token missing, revoked or expired — reissue. |
| 403 | Authorization error | The token lacks the required scope, or the org/server is not yours. |
| 404 | Not found | Wrong organization slug, server id or site id. |
| 422 | Validation error | Read `errors` — it maps each failed field to its messages. Fix and resend. |
| 429 | Too many requests | Back off using `X-RateLimit-Reset`. Default limit is 60/min per user. |
| 503 | Maintenance | Forge is offline; retry later. |

## Related

- Conventions: `conventions/laravel-conventions.yml`
- Errors: `errors/laravel-problem-types.yml`
- Scopes: `scopes/laravel-scopes.yml`
