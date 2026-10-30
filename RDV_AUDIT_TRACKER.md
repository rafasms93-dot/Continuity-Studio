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

### Provisional conclusions

| Capability | Source | Provisional disposition | Status |
|---|---|---|---|
| Project Store | `server/store.ts` | ADAPT | SOURCE_MAPPED |
| Schema / migrations | `server/project-schema.ts` | ADAPT | SOURCE_MAPPED |
| Automatic Production Director | `server/automatic-production-director.ts` | ADOPT + ADAPT | SOURCE_MAPPED |
| Local API boundary | `server/runtime.ts` | ADAPT | SOURCE_MAPPED |

### Core finding

The production core is not implemented inside the React UI. Persistence, schema migration, orchestration and API routing are backend concerns. This makes the intended RDV approach technically plausible: preserve/replace the UI independently while reusing the audited production core.

## Audit queue

- [ ] `server/production-workflow.ts`
- [ ] `server/production-memory.ts`
- [ ] `server/manual-production-director.ts`
- [ ] `server/manual-production-state.ts`
- [ ] Movie DNA service/catalog
- [ ] StoryBrain
- [ ] FilmBibleService
- [ ] characters / references / assets
- [ ] FilmRuleEngine / continuity
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
