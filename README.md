# Leaf API Postman collection

Food and Agriculture developers use Leaf's API to access clean, standardized,
and aggregated Farm data from all major sources.

This [Postman](https://www.postman.com/) collection is intended to help you get
started with Leaf and explore our API. This is the same collection that we use
in all of our demos.

For more details on how to use Leaf's API, [check our full documentation](https://docs.withleaf.io/docs)

## Authentication (OAuth2)

Leaf authentication runs on **OAuth2 / OpenID Connect**. The auth server base
(realm included) is configured in `auth_url` (default `auth.withleaf.io/realms/leaf`).
The API now expects an **OAuth2 access token** as the `Authorization: Bearer`
header, replacing the legacy `POST /api/authenticate` (`id_token`) flow.

The product endpoints under `/services/...` are **unchanged** — only the token they
accept changed (OAuth2 access token instead of the old JWT).

### Quickstart

1. On Postman, import the Collection and the Environment.
1. Fill `leaf_email` and `leaf_pass` (and, for non‑prod, adjust `auth_url`,
   `client_id`, `leaf_api_url`).
1. Run **Quickstart → Get Access Token** (or **External API → Leaf Auth → Get
   Access Token**). It performs a Direct Grant (password) login and stores
   `access_token`, `refresh_token` and `id_token` in the environment.
1. Run any other request — they inherit the `access_token` Bearer automatically.
1. Use **Refresh Token** to renew the `access_token` without re-entering credentials.

### Login flow

- **Primary — Direct Grant (password):** `POST {{auth_url}}/protocol/openid-connect/token`
  with `grant_type=password`, `client_id`, `username`, `password`. Modeled in the
  collection and used for demos/testing.
- **Service integrations — Client Credentials:** for machine-to-machine access, use
  `grant_type=client_credentials` with a confidential client's `client_id`/`client_secret`.
- **Browser apps — Authorization Code + PKCE:** for real front-end apps, use Postman's
  native OAuth 2.0 authorization helper against the same `auth_url`.

## Collection structure

The collection is split by audience:

- **Quickstart** — minimal happy path (login + a few reads).
- **External API** — requests for Leaf customers/partners. Folder-level Bearer =
  `{{access_token}}`.
  - **Leaf Auth** — OAuth2 login + refresh (Direct Grant).
  - **Providers auth** — third‑party OAuth (John Deere, ClimateFieldView, CNHi,
    Raven, Stara, Trimble, Lindsay). Unchanged.
  - **Leaf User**, **Configurations**, **Field Boundary Management**, **Machine File
    Conversion**, **Field Operations**, **Crop Monitoring**, **Alerts**, **Weather**,
    **Irrigation**, **Leaf Connect**, **Leaf Link**, **Magic Link**, **Integrations**,
    **Usage Tracking**, **Beta** — the product API (`/services/...`), unchanged in
    contract.
  - **Member (`/me`)** — self-service org context for the logged-in user.
  - **Org Admin (`/org-admin`)** — organization administration (requires `ROLE_ORG_ADMIN`).
- **Internal (Leaf Admin)** — internal Leaf-only requests. Folder-level Bearer =
  `{{leaf_admin_access_token}}`.
  - **Leaf Admin Auth** — OAuth2 login for internal admins (confidential
    `internal_client_id`, user with `ROLE_LEAF_ADMIN`).
  - **Leaf Admin (`/leaf-admin`)** — legacy user import/sync and status.

The auth endpoints (`/me`, `/org-admin`, `/leaf-admin`) are served directly by the
auth server at `{{auth_url}}/...` (realm already included in `auth_url`), not by the
product gateway.

## Environment variables

Key variables (see the environment file for the full list):

| Variable | Purpose | Default |
|---|---|---|
| `leaf_api_url` | Product API gateway host | `api-v2.withleaf.io` |
| `auth_url` | OAuth2 server base (realm included) | `auth.withleaf.io/realms/leaf` |
| `client_id` | Public client for external login | `dashboard-ui-prd` |
| `internal_client_id` | Confidential client for Leaf Admin | `leaf-internal-prd` |
| `internal_client_secret` | Secret for the internal client | *(fill in)* |
| `leaf_email` / `leaf_pass` | External user credentials | *(fill in)* |
| `leaf_admin_email` / `leaf_admin_pass` | Leaf Admin credentials | *(fill in)* |
| `access_token` / `refresh_token` / `id_token` | External tokens | *(auto)* |
| `leaf_admin_access_token` / `leaf_admin_refresh_token` | Internal tokens | *(auto)* |
| `organization_id`, `organization_name`, `api_owner_id`, `role_id`, `org_user_id`, `username_to_import` | Path/query params for the auth endpoints | *(fill in)* |

> `leaf_token` is deprecated (legacy JWT). Use `access_token`.

## Contact

For any doubts or suggestions [feel free to contact our support email](mailto:help@withleaf.io)
And if you'd like to schedule a demo [please contact our commercial email](mailto:connect@withleaf.io)
