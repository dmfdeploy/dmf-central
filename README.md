# dmf-central

[![CI](https://github.com/dmfdeploy/dmf-central/actions/workflows/ci.yml/badge.svg)](https://github.com/dmfdeploy/dmf-central/actions/workflows/ci.yml)

Placeholder roles and playbooks for the central-site services of the dmfdeploy platform.

Part of [dmfdeploy](https://github.com/dmfdeploy/dmfdeploy), an independent open prototype that explores the ideas of the EBU Dynamic Media Facility on commodity infrastructure. Status: scaffold, version 0.1.0, with no running code yet.

This repository is public so that the central-site part of the architecture has a visible place in the project. It holds the planned layout only. Nothing here deploys a service today. For the reasoning, see the [dmfdeploy platform plan](https://github.com/dmfdeploy/dmfdeploy/blob/main/docs/architecture/dmfdeploy-platform-plan.md).

## Services

| Service | Status | Notes |
|---|---|---|
| Authentik | STUB | Central identity provider that all clusters would federate to |
| OpenBao | STUB | Secrets backend, planned to move here from a separate deployment |
| Harbor / Zot | STUB | Container registry (choice deferred) |
| Central NetBox | STUB | Optional federation hub |
| Central Forgejo | STUB | Optional central Git repository hub |

## Dependencies

- [`dmf-env`](https://github.com/dmfdeploy/dmf-env): generic environment provisioning + bootstrap tooling

## Status

Scaffold only. The roles are stubs, and the central services in the table are planned work. The stubs mark where each service goes. They do not deploy anything yet.

## License

Apache License, Version 2.0. See [LICENSE](LICENSE).
Third-party components are listed in [NOTICE](NOTICE).

## For contributors

`AGENTS.md`, `CLAUDE.md` and `QWEN.md` hold instructions for AI coding agents that help maintain this repository. For the human workflow, see [CONTRIBUTING.md](CONTRIBUTING.md).
