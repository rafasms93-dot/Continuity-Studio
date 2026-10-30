# RDV Architecture Audit Tracker

This branch exists to audit Continuity Studio as the implementation reference for the next RFL Director Vision (RDV) architecture.

## Clean source baseline

```text
Upstream: momorzq-oss/Continuity-Studio
Fork: rafasms93-dot/Continuity-Studio
Upstream commit: 2f818cc4d14f84e7b31236aa4d8e307a8c5b952b
Fork baseline commit: 2f818cc4d14f84e7b31236aa4d8e307a8c5b952b
Release line: v1.1.0
Audit started: 2026-10-30
```

The fork `main` branch remains the clean upstream-derived source baseline. RDV audit notes belong on this branch until an implementation plan is approved.

## Product intent

```text
RDV identity + RDV UI
        ↓
RDV-owned API/capability vocabulary
        ↓
Audited/adapted Continuity production architecture
        ↓
Future RDV specialist services
(camera, geometry, blocking, lighting, continuity, etc.)
```

Complexity should increase internally while user interaction becomes simpler and more automatic.

## Decision vocabulary

`ADOPT` · `ADAPT` · `PORT` · `WRAP` · `KEEP_RDV` · `REJECT`

## CORE-AUDIT-001 — persistence / schema / automatic runtime / API

| Capability | Disposition | Status |
|---|---|---|
| Project Store | ADAPT | SOURCE_MAPPED |
| Schema / migrations | ADAPT | SOURCE_MAPPED |
| Automatic Production Director | PORT + ADAPT | TEST_REVIEWED |
| Local API boundary | ADAPT | SOURCE_MAPPED |

Core finding: production logic is not embedded in the React UI; the RDV UI can remain independent.

## CORE-AUDIT-002 — shared production model + creative-state chain

| Capability | Disposition | Status |
|---|---|---|
| Production Workflow state | ADAPT | TEST_REVIEWED |
| Production Memory | ADOPT + ADAPT | TEST_REVIEWED |
| Manual Guided behavior | PORT + ADAPT | TEST_REVIEWED |
| Automatic Director behavior | PORT + ADAPT | TEST_REVIEWED |
| Movie DNA model/catalog | ADAPT | SOURCE_MAPPED |
| Story structured state/contracts | ADOPT + ADAPT | TEST_REVIEWED |
| Film Bible | ADOPT + ADAPT | TEST_REVIEWED |

Key findings:

- Manual and Automatic are control policies over one shared persistent production model.
- Directors should become a `StageRegistry / CapabilityRegistry` in RDV rather than growing switch statements.
- Production Memory is a strong foundation for the future richer RDV Scene Model.
- Movie DNA structure is useful; its current heuristic recommendation is only a deterministic fallback.
- Manual/Automatic E2E suites are opt-in (`CONTINUITY_MANUAL_E2E=1`, `CONTINUITY_E2E=1`) and must be explicitly executed by RDV validation.

## CORE-AUDIT-003 — references / assets / rules / impact

| Capability | Disposition | Status |
|---|---|---|
| Reference Manager | ADOPT + ADAPT | TEST_REVIEWED |
| Canonical Asset Manifest | ADOPT + ADAPT | TEST_REVIEWED |
| Flat permanent asset storage | ADOPT | TEST_REVIEWED |
| Asset generation lifecycle | ADOPT + ADAPT | TEST_REVIEWED |
| Declarative rule catalog | ADOPT + ADAPT | TEST_REVIEWED |
| FilmRuleEngine monolith | PORT + ADAPT selectively | TEST_REVIEWED |
| Change Impact | ADOPT + ADAPT | TEST_REVIEWED |

Key findings:

- protected references, stable asset identity, explicit source versions, staged replacement and downstream impact cover much of the old RDV governance problem;
- semantic reference delete should become tombstone/archive in RDV when production history exists;
- successful generation must not imply automatic approval/lock merely because a file exists;
- declarative rules survive; the broad `FilmRuleEngine` class should not become the RDV monolith.

## CORE-AUDIT-004 — script / shots / sequence / Prompt State

- [x] `server/script-workflow.ts`
- [x] dialogue lock and shot model
- [x] formal sequence planning
- [x] `server/sequence-workspace.ts`
- [x] `server/platform-prompt-compiler.ts`
- [x] model/platform profiles
- [x] provider reference mapping/packages
- [x] script tests reviewed
- [x] Sequence Workspace tests reviewed
- [x] legacy compiler test reviewed

| Capability | Disposition | Status |
|---|---|---|
| Production Script State | ADOPT + ADAPT | TEST_REVIEWED |
| Exact Dialogue / locks | ADOPT + ADAPT | TEST_REVIEWED |
| Shot model | ADOPT + EXTEND | TEST_REVIEWED |
| Formal Sequence model | ADOPT + ADAPT | TEST_REVIEWED |
| Sequence Prompt State | ADOPT + ADAPT | TEST_REVIEWED |
| Normal + JSON renderers | ADOPT + ADAPT | TEST_REVIEWED |
| Prompt validation | ADOPT + EXTEND | TEST_REVIEWED |
| Provider reference packages | ADOPT | TEST_REVIEWED |
| Platform profile architecture | ADOPT + ADAPT | TEST_REVIEWED |
| Legacy PlatformPromptCompiler | SELECTIVE PORT / COMPATIBILITY | TEST_REVIEWED |
| Legacy generalized RDV Context Resolution | SUPERSEDE | DECIDED |
| RDV context fingerprint concept | KEEP SELECTIVELY | DECIDED |

Key decision:

`SequencePromptState` is the resolved execution context. It carries typed project/scene state, permanent references, provider profile, validation and source versions. Together with Script `sourceManifest` and Change Impact this removes the need for a second generalized Context Resolution authority.

Useful RDV fingerprint/provenance ideas may be attached to the typed Prompt/Scene state, but must not recreate a parallel source of truth.

## Audit queue

### Next: AI execution / final lifecycle

- [ ] `server/brains/*`
- [ ] `server/codex/*`
- [ ] `server/engine.ts`
- [ ] generated-video import/review
- [ ] generation attempt ledger
- [ ] export/verification
- [ ] recovery/diagnostics
- [ ] Electron / packaging

### Then

- [ ] RDV UI binding
- [ ] final port matrix
- [ ] Stick Character Walk Test implementation plan

## Implementation gate

Do not bulk-port a subsystem until all are known:

1. exact source files;
2. runtime role and dependencies;
3. relevant tests;
4. persistent state touched;
5. failure/recovery behavior;
6. license/provenance action;
7. final RDV disposition;
8. acceptance criteria.

## First functional validation

```text
Stick Character Walk Test
```

Goal: prove project creation, persistence, one character, minimal asset/reference state, sequence planning, prompt compilation, result registration/review, continuity and restart/resume without the noise of a complex film project.
