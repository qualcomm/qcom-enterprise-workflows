# qcom-enterprise-workflows

Central home for GitHub Actions workflows that are distributed across the
Qualcomm GitHub enterprise as **required workflows** and enforced via enterprise
**rulesets**. These workflows run against *other* repositories in the enterprise;
there is nothing to copy into your own repository to adopt them.

> **GitHub Actions is disabled on this repository** so the central workflows do
> not self-trigger here. The files are consumed by other repos via enterprise
> rulesets — they are not meant to run against this repo.

## What's here

| Workflow | Purpose |
| --- | --- |
| [`.github/workflows/zizmor-scan.yml`](.github/workflows/zizmor-scan.yml) | GitHub Actions security scan ([zizmor](https://github.com/zizmorcore/zizmor)), enforced as a Code Scanning merge gate. See [`.github/zizmor.md`](.github/zizmor.md). |
| [`.github/workflows/qcom-preflight-checks-for-pkg.yml`](.github/workflows/qcom-preflight-checks-for-pkg.yml) | Preflight checks (license/copyright, dependency review, Semgrep, repolinter, commit email) for `pkg-*` repositories. |

Supporting files:

- [`.github/zizmor-enterprise-policy.yml`](.github/zizmor-enterprise-policy.yml) —
  the central zizmor policy (single source of truth). It lives outside
  `.github/workflows/` because GitHub treats every file under `workflows/` as a
  workflow, and this is a zizmor config, not a workflow.
- [`.github/zizmor.md`](.github/zizmor.md) — what the zizmor gate does, how to fix
  findings, and how exceptions work.

## For repositories subject to these checks

You don't need to add anything. The checks appear on your pull requests
automatically because they are enforced at the enterprise level. To understand a
specific result:

- **zizmor / security scan** — see [`.github/zizmor.md`](.github/zizmor.md).
- **preflight checks** — see the
  [qcom-reusable-workflows](https://github.com/qualcomm/qcom-reusable-workflows)
  repository, which provides the underlying reusable workflow.

## Branches

**main**: Primary development branch. Contributors should base submissions on this
branch and open pull requests against it.

## Maintaining these workflows

Changes here affect every repository in the enterprise that the corresponding
ruleset targets. Treat changes carefully:

- The zizmor policy is fetched by the scan workflow from a **pinned commit SHA**.
  After changing the policy, bump that pin via pull request. See the maintainer
  notes in [`.github/zizmor.md`](.github/zizmor.md).
- Renaming a workflow file changes the path the enterprise ruleset references —
  coordinate renames with the ruleset owner.
- See [AGENTS.md](AGENTS.md) for the design constraints and decisions behind these
  files.

Contributions are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md).

## Getting in contact

- [Report an Issue on GitHub](../../issues)
- [Open a Discussion on GitHub](../../discussions)

## License

`qcom-enterprise-workflows` is licensed under the
[BSD-3-Clause License](https://spdx.org/licenses/BSD-3-Clause.html). See
[LICENSE.txt](LICENSE.txt) for the full license text.
