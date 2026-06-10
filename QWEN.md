# QWEN.md — dmf-central

## DMF Platform context — read first

This repo is a component of the **DMF Platform**, an umbrella workspace
checked out alongside this repo. Operators set `$DMFDEPLOY_UMBRELLA` to its
local path. Cross-cutting state (status, decisions, plans, skills) lives
there, not here.

Before any non-trivial change in this repo:

```bash
cd "$DMFDEPLOY_UMBRELLA"
git fetch && git pull
bin/generate-status.sh --no-fetch    # refreshes STATUS.md
```

Then read in order:
1. `dmfdeploy/STATUS.md` — what's happening across all 6 repos right now
2. `dmfdeploy/QWEN.md` — full boot ritual + skills index + Qwen-specific rules
3. `dmfdeploy/docs/decisions/INDEX.md` — ADRs applicable to your task
4. The most recent file under `dmfdeploy/docs/handoffs/`

For cluster ops, secrets, or dmf-cms releases, also read §0 Secrets Discipline
of the relevant skill in `dmfdeploy/.claude/skills/`. Qwen doesn't have
Claude's `/skill-name` invocation — read the SKILL.md as documentation
and apply its sections like instructions.

If you change cross-repo state, update the `<!-- HUMAN-START -->` section of
`dmfdeploy/STATUS.md` before ending the session.

---

## Repo-specific notes

This repo is **scaffold-only** per the strategic review. All `roles/*/tasks/main.yml`
are TODO-only stubs reserving central-services slots (Authentik, OpenBao,
Harbor/Zot, optional central NetBox/Forgejo) for Phase 0 step 5.

Don't implement these without a concrete forcing function (collaborator joining,
thesis-killer landing, or an explicit decision to start Phase 0 step 5). The
scaffolds exist to keep the structural reservation; tidying them up is the
wrong direction. See TODOS.md "Scaffold roles awaiting implementation" for
context.
