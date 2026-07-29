# Leaf API Postman collection

Food and Agriculture developers use Leaf's API to access clean, standardized,
and aggregated Farm data from all major sources.

This [Postman](https://www.postman.com/) collection is intended to help you get
started with Leaf and explore our API. This is the same collection that we use
in all of our demos.

For more details on how to use Leaf's API, [check our full documentation](https://docs.withleaf.io/docs)

## Authentication (Keycloak / OAuth2)

Leaf authentication runs on **Keycloak (OpenID Connect)**, realm `leaf`, hosted at
`https://auth.withleaf.io`. The API now expects a **Keycloak access token** as the
`Authorization: Bearer` header. This replaces the legacy `POST /api/authenticate`
(`id_token`) flow.

The product endpoints under `/services/...` are **unchanged** — only the token they
accept changed (Keycloak access token instead of the old JWT).

### Quickstart

1. On Postman, import the Collection and the Environment.
1. Fill `leaf_email` and `leaf_pass` (and, for non‑prod, adjust `keycloak_url`,
   `keycloak_realm`, `keycloak_client_id`).
1. Run **Quickstart → Get Keycloak Token** (or **External API → Leaf Auth → Get
   Keycloak Token**). It performs a Direct Grant (password) login and stores
   `access_token`, `refresh_token` and `id_token` in the environment.
1. Run any other request — they inherit the `access_token` Bearer automatically.
1. Use **Refresh Token** to renew the `access_token` without re-entering credentials.

### Login flow

- **Primary — Direct Grant (password):** `POST {{keycloak_url}}/realms/{{keycloak_realm}}/protocol/openid-connect/token`
  with `grant_type=password`, `client_id`, `username`, `password`. Modeled in the
  collection and used for demos/testing.
- **Service integrations — Client Credentials:** for machine-to-machine access, use
  `grant_type=client_credentials` with a confidential client's `client_id`/`client_secret`.
- **Browser apps — Authorization Code + PKCE:** for real front-end apps, use Postman's
  native OAuth 2.0 authorization helper against the same realm.

## Collection structure

The collection is split by audience:

- **Quickstart** — minimal happy path (login + a few reads).
- **External API** — requests for Leaf customers/partners. Folder-level Bearer =
  `{{access_token}}`.
  - **Leaf Auth** — Keycloak login + refresh (Direct Grant).
  - **Providers auth** — third‑party OAuth (John Deere, ClimateFieldView, CNHi,
    Raven, Stara, Trimble, Lindsay). Unchanged.
  - **Leaf User**, **Configurations**, **Field Boundary Management**, **Machine File
    Conversion**, **Field Operations**, **Crop Monitoring**, **Alerts**, **Weather**,
    **Irrigation**, **Leaf Connect**, **Leaf Link**, **Magic Link**, **Integrations**,
    **Usage Tracking**, **Beta** — the product API (`/services/...`), unchanged in
    contract.
  - **Keycloak - Member (`/me`)** — self-service org context for the logged-in user.
  - **Keycloak - Org Admin (`/org-admin`)** — organization administration
    (requires `ROLE_ORG_ADMIN`).
- **Internal (Leaf Admin)** — internal Leaf-only requests. Folder-level Bearer =
  `{{leaf_admin_access_token}}`.
  - **Leaf Admin Auth** — Keycloak login for internal admins (confidential client
    `leaf-internal-prd`, user with `ROLE_LEAF_ADMIN`).
  - **Keycloak - Leaf Admin (`/leaf-admin`)** — legacy user import/sync and status.

The Keycloak endpoints (`/me`, `/org-admin`, `/leaf-admin`) are served directly by
Keycloak at `{{keycloak_url}}/realms/{{keycloak_realm}}/...`, not by the product gateway.

## Environment variables

Key variables (see the environment file for the full list):

| Variable | Purpose | Default |
|---|---|---|
| `leaf_api_url` | Product API gateway host | `api-v2.withleaf.io` |
| `keycloak_url` | Keycloak host | `auth.withleaf.io` |
| `keycloak_realm` | Keycloak realm | `leaf` |
| `keycloak_client_id` | Public client for external login | `dashboard-ui-prd` |
| `keycloak_internal_client_id` | Confidential client for Leaf Admin | `leaf-internal-prd` |
| `keycloak_internal_client_secret` | Secret for the internal client | *(fill in)* |
| `leaf_email` / `leaf_pass` | External user credentials | *(fill in)* |
| `leaf_admin_email` / `leaf_admin_pass` | Leaf Admin credentials | *(fill in)* |
| `access_token` / `refresh_token` / `id_token` | External tokens | *(auto)* |
| `leaf_admin_access_token` / `leaf_admin_refresh_token` | Internal tokens | *(auto)* |
| `organization_id`, `organization_name`, `api_owner_id`, `role_id`, `org_user_id`, `username_to_import` | Path/query params for Keycloak endpoints | *(fill in)* |

> `leaf_token` is deprecated (legacy JWT). Use `access_token`.

## Contact

For any doubts or suggestions [feel free to contact our support email](mailto:help@withleaf.io)
And if you'd like to schedule a demo [please contact our commercial email](mailto:connect@withleaf.io)
