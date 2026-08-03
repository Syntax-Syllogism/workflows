# Syntax & Syllogism shared workflows

Reusable GitHub Actions workflows for Syntax & Syllogism tool repositories.

This repository is **public on purpose**. GitHub does not allow a reusable
workflow stored in a private repository to be called from a public one, and our
tool repositories publish their public mirrors as public repositories. The
workflow definitions here contain no secrets — every credential is supplied by
the caller through `secrets: inherit`.

The tooling that consumes these workflows lives in the private
`Syntax-Syllogism/tooling` repository.

## `docs-publish.yml`

Builds a tool's `docs-site/` and publishes the result to a subdirectory of the
documentation site repository.

```yaml
jobs:
  docs:
    uses: Syntax-Syllogism/workflows/.github/workflows/docs-publish.yml@workflows/v1
    with:
      tool_slug: my-tool
      public_repo: Syntax-Syllogism/my-tool
      site_repo: Syntax-Syllogism/Syntax-Syllogism.github.io
    secrets: inherit
```

| Input | Required | Meaning |
| --- | --- | --- |
| `tool_slug` | yes | Destination subpath — the site serves `<tool_slug>/docs`. |
| `public_repo` | yes | `owner/name` of the public tool repository. |
| `site_repo` | yes | `owner/name` of the documentation site repository. |

Required secret: `SITE_PAGES_TOKEN`, a token allowed to push to `site_repo`.
It is set as an organization secret, so `secrets: inherit` is enough.

The job is guarded by `github.repository == inputs.public_repo`, so the same
caller workflow is a no-op when it runs on a private mirror.

## Versioning

Consumers pin the moving major tag, never `master`:

```yaml
uses: Syntax-Syllogism/workflows/.github/workflows/docs-publish.yml@workflows/v1
```

Re-point `workflows/v1` for backward-compatible changes. Cut `workflows/v2`
for any change to the inputs or secrets contract.
