# Role layout — EBU DMF mapping

This repo deploys vertical-security artifacts to a federated central cluster.
Downstream sites (dmf-infra, dmf-media) consume these services.

Following the EBU DMF Reference Architecture V2.0 (2026-04-15):

| Role directory | EBU scope |
|---|---|
| `authentik/` | Vertical-security (central IdP) |
| `openbao/` | Vertical-security (central secret store) |
| `harbor/`, `zot/` | Vertical-security (registry — choice deferred) |
| `central-netbox/` | Layer 6 — App & UI (federation hub, optional) |
| `central-forgejo/` | Layer 6 — App & UI (central git hub, optional) |

The central cluster itself walks Layers 1–3 (provisioned via
`playbooks/300-central-base.yml` once the cluster is on real infrastructure).

See `dmfdeploy/docs/architecture/DMF EBU Mapping (2026-04-25).md` for the full canon.
