# Abizor Check

GitHub Actions action for running Abizor Check on one pull request.

Abizor is the control layer that AI agents operate under for AI-made software
changes. The action installs Abizor from PyPI, reads the pull request diff,
runs your verification command, verifies the generated Change Record, publishes
one GitHub Check, and uploads the run artifacts.

Action repository:
https://github.com/Abizor/abizor-check

## Caller Permissions

```yaml
permissions:
  contents: read
  pull-requests: read
  checks: write
```

The action does not require write access to contents, pull requests, issues,
deployments, packages, OIDC, or repository administration.

## Usage

Use `Abizor/abizor-check@v1` for the public stable action ref:

```yaml
name: Abizor Check

on:
  pull_request:

permissions:
  contents: read
  pull-requests: read
  checks: write

jobs:
  abizor-check:
    runs-on: ubuntu-latest
    steps:
      - uses: Abizor/abizor-check@v1
        with:
          verification-command: bash .github/scripts/abizor-verify.sh
          github-token: ${{ github.token }}
```

The verification command is executed without a shell by Abizor. Put compound
shell logic such as install plus build plus test in a repository script, then
call that script from `verification-command`.

For production or private repositories that require an immutable action
reference, pin a full commit SHA from a validated run instead of the moving
`v1` major tag.

## Inputs

Required:

- `verification-command`: command run in the caller's GitHub Actions runner.
- `github-token`: token used for GitHub PR/check reads and Check publication.

Optional:

- `abizor-version`: exact PyPI package version, default `0.1.10`.
- `python-version`: Python version for `actions/setup-python`, default `3.12`.
- `intent`: run intent, default `verify this pull request before merge`.
- `diff-base`: explicit diff base, default `origin/$GITHUB_BASE_REF`.
- `publish-check`: create or update one Abizor GitHub Check, default `true`.
- `upload-artifacts`: upload generated run artifacts, default `true`.
- `artifact-prefix`: artifact name prefix, default `abizor`.

## Outputs

- `status`
- `event-log`
- `manifest`
- `change-record-json`
- `change-record-markdown`
- `summary`
- `comment-body`
- `verify-record-output`
- `check-report`
- `check-json`

Outputs are projections. Canonical truth remains the Abizor event log.

## Boundary

The action may install Abizor from PyPI, run the Abizor PR check path, verify
the generated record, publish one Abizor GitHub Check projection, and upload
run artifacts.

The action must not create pull requests, merge pull requests, deploy code,
post comments by default, require hosted Abizor access, install a GitHub App,
or send repository content, pull request data, diffs, test output, event logs,
or run artifacts to Abizor servers.
