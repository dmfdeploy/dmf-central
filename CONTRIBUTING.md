# Contributing to dmf-central

Central-services scaffolding for the dmfdeploy platform.

This repo is part of the **dmfdeploy platform**. GitHub is the canonical home and the
single source of truth: all changes land via **Pull Request** against `main`.

## Quick start

1. Read the platform overview in the [dmfdeploy/dmfdeploy](https://github.com/dmfdeploy/dmfdeploy) umbrella repo
   (`docs/architecture/dmfdeploy-platform-plan.md`) and apply any relevant ADRs from
   `docs/decisions/INDEX.md`.
2. Set up your machine and run the checks (see [Develop and test](#develop-and-test)).
3. Fork or branch, make your change on a topic branch, open a PR against `main`.
4. Ensure CI is green, your commits are **signed off** (see DCO below), and the
   PR body links its issue (see [What a pull request needs](#what-a-pull-request-needs)).

## Develop and test

This section is the one place for development and test commands. The README and
`AGENTS.md` point here. The commands match what CI runs in
`.github/workflows/ci.yml`. Where CI doesn't run a command, the text says so.

### What exists today

dmf-central is a scaffold. Every role under `roles/` holds a `TODO` comment and
nothing else. The playbooks print a message or include one of those empty
roles. Nothing here deploys a service, and no tests exist. The checks below
lint the YAML and confirm that each playbook parses. They can't tell you
whether a service would deploy, because none does yet.

### Prerequisites

- **Python 3 with `venv` and `pip`.** CI runs the `python3` that comes with
  Ubuntu 24.04 (the `runs-on` setting in `ci.yml`). The repository doesn't
  pin a version. `pip` installs the newest Ansible release that your Python supports.
- **Network access to Ansible Galaxy**, to install the collections.
- **Optional tools.** [gitleaks](https://github.com/gitleaks/gitleaks) powers
  the commit hook.

You don't need a cluster, a cloud account, secrets, or a container runtime.
No check in this repository uses one.

### Set up

Run these commands from the repository root:

```bash
python3 -m venv .venv
. .venv/bin/activate
python -m pip install ansible ansible-lint yamllint
ansible-galaxy collection install -r requirements.yml
```

CI installs the same packages. `ansible-galaxy` installs the collections
into `~/.ansible/collections`, outside the virtual environment, where other
projects can see them. It reports that there is nothing to do when the
`ansible` package or an earlier run already provides them.

### Run the checks

Run this block from the repository root, with the virtual environment active,
before you push. It takes about a minute. The parentheses run it in a subshell
with `set -e`, so it stops at the first failing command and exits non-zero.

```bash
(
  set -e
  yamllint .
  ansible-lint . -p
  for pb in playbooks/*.yml; do
    echo "=== syntax-check: $pb ==="
    ansible-playbook "$pb" --syntax-check
  done
  bin/check-review-comments.sh
  bin/check-review-comments.sh --self-test
)
```

| Command | What it does | Time |
| --- | --- | --- |
| `yamllint .` | Checks the YAML structure | A second or less |
| `ansible-lint . -p` | Lints the playbooks and roles with the `min` profile | About 10 seconds |
| Syntax check | Parses each playbook in `playbooks/` | A few seconds |
| `bin/check-review-comments.sh` | Rejects code comments that cite review history | A second or less |
| `bin/check-review-comments.sh --self-test` | Tests the parser of the check's allowlist | About half a minute |

The syntax-check loop covers only the files directly in `playbooks/`. CI uses
the same glob, so the playbooks in `playbooks/vertical-security/` get a
syntax check only if you run `ansible-playbook <file> --syntax-check` on them
yourself.

### What CI runs

The `ci` workflow in `.github/workflows/ci.yml` has these jobs:

- **`ansible`** installs `ansible`, `ansible-lint` and `yamllint`, then runs
  the `yamllint`, `ansible-lint` and syntax-check commands from the previous
  section. It also has a `helm lint`
  step for a `charts/` directory. That directory doesn't exist, so the step
  prints `SKIP: no charts found` and passes.
- **`review-comments`** runs `bin/check-review-comments.sh` and its
  `--self-test`.
- **`working-model`** confirms that the working-model block in `AGENTS.md`,
  `CLAUDE.md` and `QWEN.md` matches the umbrella template. It checks out the
  umbrella repo, so it runs in CI only.

The `guard` workflow scans for secrets and vulnerabilities, lints the workflow
files, and checks commit subjects and the issue link. The `DCO` workflow checks
sign-offs. Both run in CI only. The `automerge` workflow doesn't check
anything. It turns on auto-merge for the PR.

No check needs a live cluster, cloud credentials, or an image build. A local run
covers the `review-comments` job and the lint and syntax steps of the `ansible`
job. It leaves out the `helm lint` step, which has nothing to lint until a
`charts/` directory exists. If you add a chart, run `helm lint` on its directory
too. A local run also doesn't cover `working-model`, the `guard` checks, or the
sign-off check.

### Install the hooks

Run this once in each clone:

```bash
bin/install-hooks.sh
```

The script sets `core.hooksPath` to `.githooks/`. The `pre-commit` and
`pre-push` hooks scan your changes with gitleaks. They skip the scan with a
warning when gitleaks isn't installed. The `guard` workflow also runs gitleaks,
with a different scope: the hooks scan your staged changes and the commits you
push, and CI scans the checked-out tree.

### Pick a first task

Each task says whether you can finish and test it on your own machine.

| Task | Offline or live |
| --- | --- |
| Fix or clarify a README, such as `roles/openbao/README.md`. Run the checks. | Offline |
| Add `playbooks/vertical-security/` to the syntax-check loop in `ci.yml`. Run the new loop locally. CI's workflow lint checks the edit. | Offline |
| Implement a role, such as `roles/openbao`. Open an issue first, because `AGENTS.md` asks for a forcing function before anyone implements a stub. To prove the role works, you need a cluster to deploy it to. | Live |

## Evidence in proportion to the change

Show how you checked your change, and size that to the change.

- **A docs edit** needs the preceding checks and no more.
- **A change to a playbook, role, workflow or lint setting** needs the preceding
  checks, plus a line in the PR saying which ones you ran and what they
  reported. The repository has no behavior tests yet. If you add behavior, add
  a test that fails without it.
- **A fix for a class of defect** needs a search of the repository for other
  places with the same pattern. Paste the search and say which matches you
  fixed or left alone.

## Branch & PR model

- **GitHub Pull Requests only.** Direct push to `main` is blocked; force-push is
  banned; linear history is required.
- Topic branches: **`<handle>/<short-slug>`** (e.g. `jdoe/fix-probe-path`). One
  logical change per branch; rebase onto `main` rather than long-lived branches.
- A reviewer (per `CODEOWNERS`) and green required checks are needed to merge.

## What a pull request needs

CI checks each of these.

- **An issue link in the PR body.** Write a fully qualified reference to an issue
  in the [dmfdeploy/dmfdeploy](https://github.com/dmfdeploy/dmfdeploy/issues)
  backlog, such as `Refs dmfdeploy/dmfdeploy#912`. Use `Closes dmfdeploy/dmfdeploy#N`
  if the pull request finishes the issue. A bare `#N` doesn't count, because it
  points at this repo. If your change has no issue, such as a typo fix, a
  maintainer can add the `no-issue` label. Only maintainers can add labels, so
  if you open the pull request from a fork, ask in the PR. The check runs when
  you open the pull request, push a commit, reopen it, or edit the PR body
  (the `on:` trigger list in `guard.yml`). Adding a label doesn't run it, so
  after a maintainer adds `no-issue`, edit the PR body or push a commit to run
  the check again.
- **A DCO sign-off on every commit.** Use `git commit -s`. See the next section.
- **Conventional commit subjects.** Use `type(scope): summary`, with one
  optional scope. The scope may use only `a-z`, `0-9`, `.`, `_` and `-`. The
  types are `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `build`, `ci`,
  `perf`, `revert` and `style`. An `!` before the colon marks a breaking
  change. Other types, such as `wip`, are rejected, and so is a scope list such
  as `(api,ui)`.
- **Passing CI.** The `ci` workflow runs the checks in
  [Develop and test](#develop-and-test). The `guard` workflow adds secret and
  vulnerability scans.

Also leave agent session links out of commit messages and the PR body. The
`guard` workflow rejects one known session-link format. It can't recognize
every tool's links, so reviewers look for them too.

## Developer Certificate of Origin (DCO)

We use the [Developer Certificate of Origin](https://developercertificate.org/),
not a CLA. **Every commit must be signed off:**

```bash
git commit -s -m "fix: correct the probe path"
```

This appends a `Signed-off-by: Your Name <you@example.com>` trailer certifying you
have the right to submit the work under the project license. A **DCO check** runs
on every PR and fails if any commit is missing the trailer. Amend with
`git commit --amend -s` or rebase with `git rebase --signoff main` to fix. PRs are
**rebase-merged** by default so your signed-off commits land on `main` unchanged.

## Versioning & releases

This repo carries a `VERSION` file (single semver line). Per **ADR-0005**,
`VERSION` is the single source of truth — any release-tagged change must update it
in the same commit. **No VERSION bump → no release.** Release tags are `v<VERSION>`,
created by release automation, never by hand.

## Secrets & public-safety posture

**Secrets stay in OpenBao.** Never commit, track, or reference credentials, tokens,
keys, kubeconfigs, or Terraform state — not even with a "remove later" TODO. Use
**placeholder syntax** for any IPs, DNS names, or operator identity in code, docs,
PR descriptions, or issues (`<control-node-public-ip>`, `dmf.example.com`,
`<handle>`). A local pre-commit gitleaks hook runs on commit, and CI runs
secret-scanning + scrub gates on every PR — but redaction is your responsibility
first. If you need a secret, ask a maintainer — do not improvise a transport.

## Must / Must not

### MUST
- Open changes as **GitHub PRs against `main`** with **signed-off** commits.
- Use Conventional Commit messages and `<handle>/<short-slug>` topic branches.
- Update `VERSION` in the same commit as any release-tagged change.
- Use **placeholder syntax** for all IPs / DNS / operator identity in every artifact.

### MUST NOT
- Commit secrets, tokens, keys, kubeconfigs, or Terraform state.
- Push directly to `main`, force-push, or use `--no-verify` / `--no-gpg-sign`.
- Paste secrets, real IPs/DNS, or operator identity into issues, PRs, or CI logs.

## AI agent contract

Much of this platform is built by AI agents. Agents contribute the same way:
**PRs against `main`, signed off, CI green.** Additionally, agents must run cluster
mutation only via `bin/run-playbook.sh` (ADR-0010), must not use
`--no-verify`/`--force`/`--no-gpg-sign`, and must stop and ask before modifying a
sub-repo with uncommitted state.

## Reporting security issues

See [`SECURITY.md`](SECURITY.md). **Do not** open a public issue for a vulnerability.

## License & spec

Contributions are licensed under [Apache 2.0](LICENSE). The canonical governance
model is **ADR-0041 — DMF Release and Contribution Model** in the
[dmfdeploy/dmfdeploy](https://github.com/dmfdeploy/dmfdeploy)
umbrella repo (`docs/decisions/`).
