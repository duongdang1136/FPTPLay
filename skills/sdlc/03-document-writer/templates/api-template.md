# API Template

Use for `api/API-<feature>.md` and `api/technical-contract.md` unless code-backed API docs specify a different implementation.

- **Lightweight docs:** may include pending decisions and proposed endpoints.
- **Final docs:** must match code-backed API docs when they exist. Do not invent endpoints, headers, token/cookie behavior, error codes, or rate limits.

```md
# Technical Contract — <Feature/Sub-feature Name>

> Project: <Project>
> Feature/Sub-feature: <Feature>
> Audience: BE, FE, QA
> API prefix: `/api/v1/...`
> Status: Implementation-ready contract
> Source-of-truth note: no dev-owned code-backed API doc exists under `features/api-docs/**` yet. Reconcile with backend implementation once published.

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Draft / Review-ready / Implementation-ready |
| Source of truth | Code-backed API doc / Backend implementation / Accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | BE / FE / QA |

## 1. Auth, Ownership & Envelope

All protected endpoints require `Authorization: Bearer <accessToken>` and verified email unless code-backed docs say otherwise.

Success envelope:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

FE must branch on `error_code`, not raw `msg`.

## 2. DTOs / Types

```ts
type ExampleDto = {
  id: string;
};
```

## 3. Endpoint Traceability

| Endpoint | Product requirement | Screen / Surface | Side effects |
|---|---|---|---|
| `POST /...` | F-001 | ... | ... |

## 4. Endpoints

### 4.1 `<METHOD> /path`

Purpose:

Auth / ownership:

Headers:

```text
Authorization: Bearer <accessToken>
Content-Type: application/json
```

Request:

```json
{}
```

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|

## 5. Error Code Matrix

| Code | Meaning | FE/Product behavior |
|---|---|---|
| `VALIDATION_ERROR` | Request is invalid. | Show field/global error. |
| `UNAUTHORIZED` | Token missing/expired. | Route to Login. |
| `FORBIDDEN` | User lacks permission. | Show safe access error. |
| `RATE_LIMITED` | Rate limit exceeded. | Disable action and show retry timer if available. |
| `SERVER_ERROR` | Unexpected server failure. | Allow retry / show fallback. |

## 6. Client State Contract

| API result / `error_code` | FE state | Required UI behavior |
|---|---|---|
| Success | success | Update page state from `data`. |
| `VALIDATION_ERROR` | form-error | Show mapped copy; preserve user input where safe. |
| `RATE_LIMITED` | cooldown | Disable submit; show retry time from response if provided. |

## 7. Side Effects & Persistence

| Operation | Server-side side effects | Persistence / schema impact |
|---|---|---|

## 8. Security / Privacy

- Do not return sensitive/internal fields unless explicitly required.
- Do not log raw private payloads, tokens, credentials, prompts, files, or secret URLs.
- Use safe errors for inaccessible private resources when appropriate.

## 9. Rate Limits / Config

Use this section only when the feature has quotas, rate limits, env vars, feature flags, or plan limits.

| Config / Limit | Value | Used by | QA note |
|---|---|---|---|

## 10. Observability / Audit — If applicable

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|

## 11. API Test Matrix

| ID | Scenario | Expected |
|---|---|---|
| API-001 | Success path | Returns success envelope with expected `data`. |
| API-002 | Invalid request | Returns error envelope and stable `error_code`. |
```
