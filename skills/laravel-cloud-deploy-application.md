---
name: laravel-cloud-deploy-application
description: Create a Laravel Cloud application and environment, set environment variables and a domain, trigger a deployment and follow it to completion.
api: openapi/laravel-cloud-openapi.json
base_url: https://cloud.laravel.com/api
generated: '2026-07-19'
method: generated
source: openapi/laravel-cloud-openapi.json, conventions/laravel-conventions.yml
operations:
  - public.applications.index
  - public.applications.store
  - public.applications.environments.store
  - public.applications.environments.index
  - public.environments.show
  - public.environments.variables.store
  - public.environments.domains.store
  - public.environments.deployments.store
  - public.environments.deployments.index
  - public.deployments.show
---

# Deploy an application on Laravel Cloud

## Before you start

- Authenticate with `Authorization: Bearer <token>` using a token generated in the Laravel Cloud UI.
  See https://cloud.laravel.com/docs/api/authentication.
- Send `Accept: application/json` and `Content-Type: application/json`.
- **The Cloud API declares no scopes** — a token is all-or-nothing against its organization. Store it
  as tightly as you would a root credential.
- Paths are flat (`/applications`, `/environments/{environment}/...`); the organization is implied by
  the token, unlike Forge which nests everything under `/orgs/{organization}`.
- Success bodies are `application/vnd.api+json`; errors are `application/json` shaped
  `{ "message", "errors" }`.
- **No idempotency key exists.** Re-list before retrying any failed create.

## Steps

1. **Check what exists.** `public.applications.index` (`GET /applications`). Page with `page[size]` and
   `page[cursor]`; filter with `filter[...]`; expand with `include=`.

2. **Create the application.** `public.applications.store` (`POST /applications`).

3. **Create an environment.** `public.applications.environments.store`
   (`POST /applications/{application}/environments`). List them later with
   `public.applications.environments.index`.

4. **Set environment variables.** `public.environments.variables.store`
   (`POST /environments/{environment}/variables`). Deleting variables is a separate operation
   (`POST /environments/{environment}/variables/delete`), not a `DELETE`.

5. **Attach a domain.** `public.environments.domains.store`
   (`POST /environments/{environment}/domains`).

6. **Deploy.** `public.environments.deployments.store`
   (`POST /environments/{environment}/deployments`).

7. **Follow the deployment.** Poll `public.deployments.show` (`GET /deployments/{deployment}`), or list
   history with `public.environments.deployments.index`. Read overall environment state with
   `public.environments.show`.

## Operational notes

- `public.environments.start` and `public.environments.stop` control compute. Laravel Cloud supports
  scale-to-zero, so an idle environment may need to wake before it serves traffic.
- Rate limiting on the Laravel APIs defaults to 60 requests/minute per authenticated user, signalled by
  `X-RateLimit-Limit`, `X-RateLimit-Remaining` and `X-RateLimit-Reset`.
- **Avoid the `/databases` operations.** The five operations tagged *Databases (Legacy)* are flagged
  `deprecated: true` in the spec; use the database-cluster and database-schema resources instead.

## Error handling

| Status | Meaning |
|---|---|
| 401 | No valid token was provided. |
| 403 | Authorization error — the token cannot act on this resource. |
| 404 | Not found. |
| 422 | Validation error; `errors` maps each field to its messages. |
| 429 | Too many requests. |

## Related

- Conventions: `conventions/laravel-conventions.yml`
- Errors: `errors/laravel-problem-types.yml`
- Lifecycle and deprecations: `lifecycle/laravel-lifecycle.yml`
