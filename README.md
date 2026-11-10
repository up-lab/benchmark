# CI runner benchmark — up-lab fork

This is the `up-lab/benchmark` fork of
[`ci-router/benchmark`](https://github.com/ci-router/benchmark). It exists to
prepare one bounded, native Ubicloud GitHub Actions runner sample at the same
4-vCPU/16-GB, Ubuntu 24.04 baseline as the free GitHub-hosted route.

## Provenance

- **Source:** upstream `ci-router/benchmark`, commit
  `71098db26f7ed6b6cc2c3e0275d80491399969c2` (the fork's `main`).
- **Runs:** a fork result must link to a public run in `up-lab/benchmark`.
  Upstream `ci-router/benchmark` runs are historical upstream measurements, not
  fork measurements, and must not be presented as this fork's results.
- **Results:** the collector and publisher use the workflow's
  `GITHUB_REPOSITORY`; they validate it as `owner/name` and write to that
  repository's `results` branch. Nothing is hardcoded to `ci-router/benchmark`.
- **Fork results today:** none. No Ubicloud job has run from this fork, so there
  is no fork measurement, comparison or ranking to report.

## Status

**Prepared, not executed.** The fork enables exactly two routes:

| Route | Label | Size | Image | State |
| --- | --- | --- | --- | --- |
| `github-hosted-4vcpu` | `ubuntu-24.04` | 4 vCPU / 16 GB | Ubuntu 24.04 | enabled, free baseline |
| `ubicloud-hosted-4vcpu` | `ubicloud-standard-4-ubuntu-2404` | 4 vCPU / 16 GB | Ubuntu 24.04 | enabled, paid, not yet run |

Depot, Boat, Tenki and every other route are disabled in this fork to prevent
accidental cross-organization access or inference. The upstream Depot and Boat
pilots and their historical runs are not fork measurements.

The Ubicloud GitHub App `167560338` is installed for all repositories and is
unsuspended; the fork has admin push. That does **not** prove first-job
admission. Only a normal job can establish repository/account admission, and
that job is still pending. No subscription or plan purchase is authorized, and
no ruleset bypass is used.

`premiumCpu` and `transparentCache` remain `null` (unknown) in `routes.json`.
Do not force account settings to make them known. Ubicloud's published standard
4-vCPU rate is `$0.0025/minute` and premium is `$0.004/minute`; 15 minutes
therefore estimates `$0.0375` or `$0.06` of machine time. These are
informational estimates, not an observed charge or a hard billing cutoff. The
actual CPU fingerprint from the job is required before interpreting any result.

## Run only with confirmed budget headroom

As of 2026-11-10, do not dispatch while the recorded holds exhaust the allocation. The owner's external allocation is currently held by one
Depot and one Boat refusal, the charge is unknown, and there is no extra
headroom until accounting is reconciled or the UTC day rolls over. Confirmed external headroom is required before dispatch under the standing
owner authorization.

After headroom is confirmed, and after this change is merged to the fork's `main`, the single
intended command is:

```sh
gh workflow run benchmark.yml \
  --repo up-lab/benchmark \
  --ref main \
  -f route=ubicloud-hosted-4vcpu \
  -f workload=node-typescript \
  -f repeats=1
```

This selects one explicit paid route, one workload and one repeat. The job has a
15-minute timeout, one matrix cell, no fallback and global serialization. The
default manual dispatch remains the free GitHub baseline; enabling Ubicloud does
not enroll it in the schedule.

The existing six-hour cron is guarded to the upstream
`ci-router/benchmark` repository on `main`, so a fork scheduled plan is skipped.
This fork does not add a paid cron. Manual dispatch is the only intended path.

## What is saved

When a run is on the fork's default branch, the `collect` job appends one row
per `run <route> <workload> <repeat>` job to the fork's `results` branch under
`results/<suite>/`, and saves the raw jobs-API response and opted-in logs under
`raw/`. It never pushes to the default branch. The GitHub-hosted baseline opts
in to log archiving; the Ubicloud route does not.

## Checks

Node 22 is required; there are no packages.

```sh
node --test
```

The suite pins suite `0.2.0`, Node `22.23.4`, TypeScript commit
`1e4744d68260a7cb91b62b12edc3f6a2187faaf1`, and all actions by full commit SHA.

## Safety

The workflow starts only from `workflow_dispatch` or the schedule. It has no
`pull_request` trigger, no secrets in benchmark jobs, no caches and no
third-party actions. Benchmark jobs use `permissions: {}`. Only the
GitHub-hosted `collect` job can write, only on the default branch, and only to
the `results` branch. There is no pull-request trigger. Native provider jobs do not pass through
the CI Router admission path; this is an external provider benchmark.

## Licence

- Code, workflows, workload definitions and tests: [Apache License 2.0](LICENSE).
- Results and raw data on the `results` branch: [CC-BY-4.0](LICENSE-RESULTS),
  attributed to "ci-router benchmark".
