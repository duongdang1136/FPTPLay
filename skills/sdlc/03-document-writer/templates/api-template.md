# Legacy API Template

This template is kept only for backward compatibility with older FPTPlay docs.

## Default for new FPTPlay final docs

Do not generate new final docs as `api/technical-contract.md` by default.

Use the flat final template instead:

```text
features/_templates/final-feature/feature-functional-requirements.md
```

In the flat final format, API/integration details belong inside:

```text
<feature-name>-functional-requirements.md
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
