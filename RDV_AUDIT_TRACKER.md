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

RDV will not be a wholesale rename of Continuity Studio. The target is:

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

## CORE-AUDIT-001 — source mapped

- [x] Apache-2.0 root license inspected.
- [x] upstream/fork baseline SHA parity confirmed.
- [x] root `NOTICE` presence checked; none found at audited baseline.
- [x] `server/store.ts` mapped — ProjectStore / persistence boundary.
- [x] `server/project-schema.ts` mapped — schema version 18 + migration layer.
- [x] `server/automatic-production-director.ts` mapped — resumable automatic production state machine.
- [x] `server/runtime.ts` mapped — Express + Zod API boundary.

### CORE-AUDIT-001 conclusions

| Capability | Source | Provisional disposition | Status |
|---|---|---|---|
| Project Store | `server/store.ts` | ADAPT | SOURCE_MAPPED |
| Schema / migrations | `server/project-schema.ts` | ADAPT | SOURCE_MAPPED |
| Automatic Production Director | `server/automatic-production-director.ts` | PORT + ADAPT | TEST_REVIEWED |
| Local API boundary | `server/runtime.ts` | ADAPT | SOURCE_MAPPED |

The production core is not implemented inside the React UI. Persistence, schema migration, orchestration and API routing are backend concerns. This makes the intended RDV approach technically plausible: preserve/replace the UI independently while reusing the audited production core.

## CORE-AUDIT-002 — shared production model + creative-state chain

- [x] `server/production-workflow.ts`
- [x] `server/production-memory.ts`
- [x] `server/manual-production-director.ts`
- [x] `server/movie-dna-service.ts`
- [x] `server/story-brain.ts`
- [x] `server/film-bible.ts`
- [x] Manual Guided E2E test reviewed.
- [x] Automatic E2E test reviewed.
- [x] Production Memory tests reviewed.
- [x] Story tests reviewed.
- [x] Film Bible/character tests reviewed.

### CORE-AUDIT-002 conclusions

| Capability | Provisional disposition | Status |
|---|---|---|
| Production Workflow state | ADAPT | TEST_REVIEWED |
| Production Memory | ADOPT + ADAPT | TEST_REVIEWED |
| Manual Guided behavior | PORT + ADAPT | TEST_REVIEWED |
| Automatic Director behavior | PORT + ADAPT | TEST_REVIEWED |
| Movie DNA model/catalog | ADAPT | SOURCE_MAPPED |
| Story structured state/contracts | ADOPT + ADAPT | TEST_REVIEWED |
| Film Bible | ADOPT + ADAPT | TEST_REVIEWED |

Key architectural conclusion:

- Manual and Automatic are control policies over one shared persistent production model.
- RDV should preserve this behavior.
- RDV should not preserve the large director switch statements as its long-term extensibility mechanism.
- Preferred target is a `StageRegistry / CapabilityRegistry` so future camera, geometry, blocking, lighting, composition and motion specialists can enrich production without expanding a monolithic director.
- Movie DNA recommendation is currently heuristic/preset/keyword-driven; preserve the model and fallback, not the recommendation engine as final RDV intelligence.
- Production Memory is a strong base for the future richer RDV Scene Model.

### Test caveat

The major Manual and Automatic E2E suites are opt-in:

```text
CONTINUITY_MANUAL_E2E=1
CONTINUITY_E2E=1
```

The clean fork baseline had no combined commit status and no associated workflow run at audit time. RDV must explicitly execute these tests before marking parity VERIFIED.

## Audit queue

### Next: visual / continuity production core

- [ ] `server/reference-manager.ts`
- [ ] `server/asset-manifest.ts`
- [ ] `server/asset-maker.ts`
- [ ] `server/asset-storage.ts`
- [ ] `server/rule-engine.ts`
- [ ] `server/change-impact.ts`
- [ ] associated tests

### Then

- [ ] script / dialogue / shots / sequences
- [ ] prompt compiler / platform profiles
- [ ] Codex / brain routing
- [ ] generations / review / export
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

The first end-to-end implementation validation should be deliberately small:

```text
Stick Character Walk Test
```

Goal: prove project creation, persistence, one character, minimal asset/reference state, sequence planning, prompt compilation, result registration/review, continuity and restart/resume without the noise of a complex film project.
