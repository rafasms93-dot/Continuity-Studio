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

- [x] `server/reference-manager.ts`
- [x] `server/asset-manifest.ts`
- [x] `server/asset-maker.ts`
- [x] `server/asset-storage.ts`
- [x] `server/rule-engine.ts`
- [x] `server/rule-catalog.ts`
- [x] `server/change-impact.ts`
- [x] reference lifecycle tests reviewed
- [x] asset manifest tests reviewed
- [x] flat storage tests reviewed
- [x] generation lifecycle tests reviewed
- [x] rule-engine tests reviewed
- [x] change-impact tests reviewed

| Capability | Disposition | Status |
|---|---|---|
| Reference Manager | ADOPT + ADAPT | TEST_REVIEWED |
| Canonical Asset Manifest | ADOPT + ADAPT | TEST_REVIEWED |
| Flat permanent asset storage | ADOPT | TEST_REVIEWED |
| Asset generation lifecycle | ADOPT + ADAPT | TEST_REVIEWED |
| Declarative rule catalog | ADOPT + ADAPT | TEST_REVIEWED |
| FilmRuleEngine monolith | PORT + ADAPT selectively | TEST_REVIEWED |
| Change Impact | ADOPT + ADAPT | TEST_REVIEWED |
| Legacy generalized RDV Context Resolution | LIKELY_SUPERSEDE | PENDING_PROMPT_AUDIT |

Key findings:

- protected references, stable asset identity, explicit source versions, staged replacement and downstream impact already cover much of the old RDV governance problem;
- semantic reference delete should become tombstone/archive in RDV when production history exists;
- successful generation must not imply automatic approval/lock merely because a file exists;
- rule data is valuable, but the broad `FilmRuleEngine` class should not be copied as a new monolith;
- final Context Resolution decision is deferred until Sequence Workspace / Prompt State audit.

## Audit queue

### Next: executable scene / prompt construction

- [ ] `server/script-workflow.ts`
- [ ] shot planning / dialogue lock
- [ ] sequence planning
- [ ] `server/sequence-workspace.ts`
- [ ] `server/platform-prompt-compiler.ts`
- [ ] model/platform profiles
- [ ] reference mapping / packages
- [ ] associated tests

### Then

- [ ] Codex / brain routing
- [ ] generations / video review / export
- [ ] Electron / packaging
- [ ] RDV UI binding

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
