# Legacy Product Template

This template is kept only for backward compatibility with older FPTPlay docs.

## Default for new FPTPlay final docs

Do not generate new final docs as `product/functional-specification.md` by default.

Use the flat final template instead:

```text
features/_templates/final-feature/feature-functional-requirements.md
```

Default final output:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-functional-requirements.md
```

`<feature-name>-functional-requirements.md` combines Product, UX, API/integration, state, error, analytics if relevant, and QA acceptance coverage.

## When this legacy template is allowed

Use this split product template only when:

- user explicitly asks for old `product/api/design` format, or
- maintaining an existing old feature folder where split files already exist and must stay split.

For lightweight docs, continue using `features/lightweight/<...>/product/SRS-<feature>.md` when needed.
