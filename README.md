# Laravel

Laravel is the company behind the Laravel PHP framework and a suite of commercial developer
infrastructure products: **Laravel Cloud** (a fully managed PaaS for Laravel and Symfony applications),
**Laravel Forge** (server provisioning and application deployment across DigitalOcean, AWS, Hetzner,
Vultr, Akamai/Linode and custom VPS), **Envoyer** (zero-downtime PHP deployments), **Vapor**
(serverless deployment on AWS Lambda), **Nightwatch** (application monitoring) and **Nova** (an
administration panel).

Website: https://laravel.com/ — Backed by: accel

## APIs

| API | Base URL | Spec |
|---|---|---|
| [Laravel Forge API](https://forge.laravel.com/docs/api-reference/introduction) | `https://forge.laravel.com/api` | OpenAPI 3.1 — 154 paths / 273 operations |
| [Laravel Forge API (legacy v1)](https://forge.laravel.com/api-documentation) | `https://forge.laravel.com/api/v1` | documented only — **discontinuation announced for 2026-07-31** |
| [Laravel Cloud API](https://cloud.laravel.com/docs/api/authentication) | `https://cloud.laravel.com/api` | OpenAPI 3.1 — 61 paths / 104 operations |
| [Envoyer API](https://envoyer.io/api-documentation) | `https://envoyer.io/api` | documented only |

Both machine-readable specs are published by Laravel at stable, unauthenticated URLs and linked from
each product's `llms.txt`:

- https://forge.laravel.com/api/docs.openapi
- https://cloud.laravel.com/api-docs/api.json

## House style

The current Forge API and the Cloud API share one design. Success responses use
`application/vnd.api+json` with `data` / `meta` / `links` envelopes, typed resource objects and a
`relationships` object; errors fall back to the plain Laravel envelope
`{ "message": ..., "errors": { field: [...] } }` on `application/json`.

- **Pagination** — cursor, via `page[size]` and `page[cursor]`; `meta.next_cursor` / `meta.prev_cursor`
- **Filtering** — `filter[<field>]`
- **Sorting** — `sort=<field>`, `-` prefix to reverse
- **Includes** — `include=<relationship>`, comma separated
- **Rate limiting** — 60 requests/minute per user, `X-RateLimit-*` headers
- **Idempotency** — not supported and not documented on any Laravel API

## Agent readiness

Laravel ships **Laravel Boost** (`laravel/boost`), a first-party MCP server run as
`php artisan boost:mcp` with nine documented tools, plus twelve published Agent Skills in the
`SKILL.md` format. Both target writing Laravel application code rather than consuming the Forge or
Cloud REST APIs.

## Artifacts in this repo

`openapi/` `overlays/` `authentication/` `scopes/` `conventions/` `errors/` `lifecycle/` `changelog/`
`cli/` `packages/` `mcp/` `skills/` `llms/` `well-known/` `security/` `conformance/` `data-model/`
`asyncapi/`
