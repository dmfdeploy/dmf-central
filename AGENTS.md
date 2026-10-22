# AGENTS.md — dmf-central

This repo holds the deploy-once central services of the dmfdeploy platform:
Authentik (IdP), OpenBao (secrets), Harbor or Zot (registry), and optional
central NetBox and Forgejo. It is a scaffold: every `roles/*/tasks/main.yml`
is a TODO-only stub reserving a slot for Phase 0 step 5. Cross-repo context
lives in the [dmfdeploy umbrella repo](https://github.com/dmfdeploy/dmfdeploy);
scope is in its `docs/architecture/DMF Platform Plan.md` §7b, §7c and §8f.

## Boot steps

1. In the umbrella repo, run `git pull` and `bin/generate-status.sh`, then
   read `STATUS.digest.local.md`. Post shared notes to the pinned issue
   https://github.com/dmfdeploy/dmfdeploy/issues/618.
2. Read your tracking issue and the fitting ADRs in umbrella `docs/decisions/`.

## Build and test

CI (`.github/workflows/ci.yml`) runs these from the repo root:
```bash
ansible-galaxy collection install -r requirements.yml
yamllint . && ansible-lint . -p
for pb in playbooks/*.yml; do ansible-playbook "$pb" --syntax-check; done
```

## Rules that are easy to miss

- Do not implement the scaffold roles without a forcing function: a
  collaborator joining, a thesis-killer landing, or a decision to start Phase 0
  step 5. Do not tidy the stubs up; they keep the reservation.
- Before you retrieve secrets, run cluster operations or release dmf-cms, read
  section 0 of the matching skill in the umbrella `.agents/skills/`.
- Never commit secrets, tokens, keys, kubeconfigs or Terraform state. Use
  placeholders such as `dmf.example.com`.
- Land changes by pull request with `git commit -s`. Never use `--no-verify`,
  `--force` or `--no-gpg-sign`. See `CONTRIBUTING.md`.
- Ask before you modify a sub-repo that has uncommitted changes.

<!-- WORKING-MODEL-BLOCK-START — generated from umbrella docs/templates/working-model-block.md; do not edit copies, edit the template and run bin/check-working-model-sync.sh -->
## Working model (mandatory)

Canonical: [docs/WORKING-MODEL.md](https://github.com/dmfdeploy/dmfdeploy/blob/main/docs/WORKING-MODEL.md)
in the umbrella repo. The three rules that matter mid-task:

1. **Work starts at an issue** in the canonical backlog
   ([dmfdeploy/dmfdeploy issues](https://github.com/dmfdeploy/dmfdeploy/issues)):
   `component:*`/`workstream:*` labels **always**, plus a milestone **only if
   the work is scheduled** — unscheduled work gets `platform-debt` and no
   milestone (§2). Non-trivial work gets a plan doc in umbrella `docs/plans/`
   with `tracking_issue` frontmatter.
2. **The completing PR auto-closes its issue; you still flip the plan
   frontmatter by hand in that PR.** Reference umbrella issues **fully
   qualified** — `Closes dmfdeploy/dmfdeploy#N` (bare `#N` targets the wrong
   repo); the daily issue-close reconciler honors that ref, cross-repo
   included. Manual close is a fallback.
3. **Never invent a local backlog** (TODO files, ad-hoc trackers). Issues =
   liveness; plan frontmatter = design state; ADRs = decisions (RFC in
   Discussions first); STATUS.md = pointer file (operator notes live in pinned issue dmfdeploy/dmfdeploy#618);
   STATUS.local.md = live repo snapshot.
<!-- WORKING-MODEL-BLOCK-END -->
