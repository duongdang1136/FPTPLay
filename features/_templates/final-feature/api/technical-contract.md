# Legacy API Template

This file is kept only for backward compatibility with older FPTPlay final docs that already use split folders.

New FPTPlay final docs should not use this template by default.

Use the flat final template instead:

```text
features/_templates/final-feature/feature-functional-requirements.md
features/_templates/final-feature/feature-mockup.html
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

Use this legacy split API template only when:

- user explicitly asks for old `product/api/design` format, or
- maintaining an existing old feature folder where split files already exist and must stay split.
