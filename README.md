# Laravel

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
