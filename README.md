# dmf-central

Deploy-once central services for the DMF Platform.

## Services

| Service | Status | Notes |
|---|---|---|
| Authentik | STUB | Central IdP — all clusters federate here |
| OpenBao | STUB | Secrets backend (relocate from current external deploy) |
| Harbor / Zot | STUB | Container registry (choice deferred) |
| Central NetBox | STUB | Optional — federation hub |
| Central Forgejo | STUB | Optional — central git repo hub |

## Dependencies

- `dmf-env` — environment-specific inventory

## Status

Scaffold only. Central services deployment is Phase 0 step 5 of the DMF Platform Plan.

## License

Apache License, Version 2.0 — see [LICENSE](LICENSE).
Third-party components are listed in [NOTICE](NOTICE).
