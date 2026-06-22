# Legacy Product Template

This file is kept only for backward compatibility with older FPTPlay final docs that already use split folders.

New FPTPlay final docs should not use this template by default.

Use the flat final template instead:

```text
features/_templates/final-feature/feature-functional-requirements.md
features/_templates/final-feature/feature-mockup.html
```

Default final output:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-functional-requirements.md
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-mockup.html
```

`<feature-name>-functional-requirements.md` combines Product, UX, API/integration, state, error, analytics if relevant, and QA acceptance coverage.

Use this legacy split product template only when:

- user explicitly asks for old `product/api/design` format, or
- maintaining an existing old feature folder where split files already exist and must stay split.
