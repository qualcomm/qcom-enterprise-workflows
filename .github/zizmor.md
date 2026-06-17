# zizmor security gate

This document explains the enterprise GitHub Actions security scan powered by
[zizmor](https://github.com/zizmorcore/zizmor) and how it affects pull requests.

`zizmor` statically analyzes GitHub Actions workflows for common security issues
(dangerous triggers, template injection, credential/permission mistakes, unpinned
actions, and more). It runs centrally as an **enterprise required workflow** and
is enforced as a merge gate via a **Code Scanning** ruleset — there is no file to
add to your repository.

## What you'll see on a pull request

- A **GitHub Actions Security Scan (zizmor)** check appears on PRs automatically.
- Findings are reported to the repository's **Security → Code scanning** tab.
- The merge gate blocks a PR only when there is an **open high-severity** alert.
  Lower-severity findings are advisory (visible, non-blocking).

### Advisory (annotations) mode

In some contexts the scan cannot upload results to code scanning (for example,
code scanning is not available on the repository, or the run has a read-only
token). When that happens the workflow **degrades gracefully**: it runs in
annotations mode and writes a job summary instead of uploading SARIF. Advisory
runs surface findings inline for visibility but do not arm the merge gate. The
job summary on the run explains why a particular run was advisory.

## Fixing a finding

1. Open the **GitHub Actions Security Scan (zizmor)** check (or the Code scanning alert) and read the rule
   description and the flagged location.
2. Apply the recommended remediation to your workflow (for example, scope
   `permissions:`, avoid interpolating untrusted input into `run:` blocks, or pin
   an action).
3. Push the fix — the scan re-runs and the alert closes when resolved.

zizmor's rule documentation has remediation guidance for every audit:
<https://docs.zizmor.sh/audits/>.

## Currently blocking rules

The following are treated as **blocking** (high severity):

- `dangerous-triggers` — e.g. `pull_request_target` / `workflow_run` used unsafely.
- `template-injection` — untrusted `${{ ... }}` expansion in `run:` and similar.
- `github-env` — unsafe writes to `GITHUB_ENV` / `GITHUB_PATH`.

`unpinned-uses` (action pinning) is currently **advisory** and will become
blocking in a later phase once migration tooling and communications are in place.
All other zizmor audits run at their default severity.

## Exceptions

If a finding is a verified false positive or an accepted risk, the supported
escape hatch is an inline comment on the offending line:

```yaml
- uses: some/action@v1 # zizmor: ignore[rule-name]
```

Use these sparingly and with justification — ignores of protected rules are
reviewed. Dismissing code-scanning alerts is restricted to Security Managers.

## Policy configuration

The single source of truth for the policy is
[`.github/zizmor-enterprise-policy.yml`](zizmor-enterprise-policy.yml) in this
repository. It lists only the **deviations** from zizmor's built-in defaults. The
scan workflow fetches this config from a pinned commit and passes it to zizmor
explicitly, so a repository-local `zizmor.yml` cannot weaken the policy.

## For maintainers

- The policy is fetched by [`.github/workflows/zizmor-scan.yml`](workflows/zizmor-scan.yml)
  from this repo at an **immutable commit SHA** (`ZIZMOR_CONFIG_REF`). Never point
  it at a branch or tag.
- To change the policy: edit `.github/zizmor-enterprise-policy.yml`, merge the
  change, then bump `ZIZMOR_CONFIG_REF` to the new commit SHA via PR.
- Pinned versions: `zizmor-action` and `actions/checkout` are pinned by SHA in the
  scan workflow; bump them deliberately.
