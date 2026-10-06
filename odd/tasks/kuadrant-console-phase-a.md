# Feature: kuadrant-console-phase-a

## Objective
Replace the broken `custom-rhcl-console` helmApps install with [gateway-smashes/kuadrant-console](https://github.com/gateway-smashes/kuadrant-console) so the Connectivity Link OpenShift Console plugin installs automatically with this workshop.

## Problem
`Everything-is-Code/custom-rhcl-console` returns 404. Workshop still points `connectivityLink.helmApps` at that source. Plugin enable Job waits for `kuadrant-console-plugin`, which does not match the new plugin name `kuadrant-console`.

## Why
Phase A of the gateway-smashes adoption: console plugin only (no developer portal).

## Scope
- Local Helm component under `examples/helm/components/kuadrant-console` (Service + Deployment TLS + ConsolePlugin + optional ConfigMap)
- Wire via `connectivityLink.apps` (sync-wave ~10)
- Remove/disable `custom-rhcl-console` helmApps entry
- Fix `enable-console-plugins.yaml` to enable `kuadrant-console`
- Update README, examples/helm/README.md, showroom module 11 / prerequisites refs
- Ignore `.codegraph/` in `.gitignore`

## Out of scope
- rhcl-developer-portal
- dns-prober sidecar/service
- ConsolePlugin proxy aliases for AI/MCP playgrounds

## Constraints
- Image: `quay.io/gateway-smashes/kuadrant-console:1.5.1`
- Namespace: `kuadrant-console`
- ConsolePlugin name: `kuadrant-console` (displayName: Connectivity Link)
- HTTPS :9001 with service-CA serving cert
- Artifacts in English; chat may be Spanish
- Delivery strategy: `ask-on-risk` (default)
- Route: delegated direct (writer trigger: 2+ non-trivial files)

## Acceptance criteria
- [x] Chart renders Service, Deployment (TLS mount), ConsolePlugin, ConfigMap
- [x] App-of-Apps creates Application for `kuadrant-console` when enabled
- [x] Old `custom-rhcl-console` helmApps entry removed or `enabled: false`
- [x] Plugin enabler includes `kuadrant-console`
- [x] Docs/showroom reference new namespace, plugin name, image, upstream URL
- [x] `helm template` smoke succeeds for the new chart

## Checks
- `helm template test examples/helm/components/kuadrant-console`
- Spot-check values.yaml apps entry + disabled/removed helmApps
- Grep for stale `custom-rhcl-console` install path (allow demo ns `kuadrant-console-demo`)

## Tasks
- [x] T1: Add local Helm chart `examples/helm/components/kuadrant-console`
- [x] T2: Wire `connectivityLink.apps` + remove/disable helmApps `custom-rhcl-console` + path values injection
- [x] T3: Fix console plugin enabler Job
- [x] T4: Update README / examples/helm/README / showroom module 11 + prerequisites
- [x] T5: Verify with helm template + grep; work-unit commit

## Progress
- Created: 2026-10-06
- Branch: `feat/kuadrant-console-phase-a`
- Completed: 2026-10-06 — T1–T5 via delegated writer
- Verification:
  - `helm template test examples/helm/components/kuadrant-console`: OK (ConsolePlugin/kuadrant-console, serving-cert annotation, `/var/serving-cert` mount)
  - `rg custom-rhcl-console` in README/helm/showroom install surfaces: only removal comment in `examples/helm/values.yaml`
