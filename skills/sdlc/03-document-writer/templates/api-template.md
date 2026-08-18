# Legacy API Template

This template is deprecated. FPTPlay no longer uses `api/technical-contract.md`.

## Default for FPTPlay final docs

Do not generate `api/technical-contract.md`. API/integration details belong inside the single source-of-truth spec:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/product/<feature-name>.md
```

Recommended section:

```md
## 9. API / Integration Contract
```

Default Pulse/FPTPlay API envelope is `{ status, error_code, msg, data }` unless code-backed API docs prove otherwise. FE/Product/Design should branch on `error_code`; raw `msg` is fallback only.

Do not invent endpoints, headers, token/cookie behavior, error codes, or rate limits. If code-backed API docs exist, reconcile the flat functional requirements file with those docs.

## When this legacy template is allowed

Use this split API template only when:

- user explicitly asks for old `product/api/design` format, or
- maintaining an existing old feature folder where split files already exist and must stay split.

For lightweight docs, continue using `features/lightweight/<...>/api/API-<feature>.md` when needed.
