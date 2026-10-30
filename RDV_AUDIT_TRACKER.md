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

The fork `main` branch remains the clean upstream-derived source baseline. RDV audit notes live on this branch; implementation belongs in the RDV repository.

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
- Manual/Automatic E2E suites are opt-in and must be explicitly executed by RDV validation.

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

`SequencePromptState` is the resolved execution context. Script `sourceManifest` + Prompt State `sourceVersions` + Change Impact remove the need for a second generalized Context Resolution authority. Useful RDV fingerprint/provenance ideas may be attached to typed Scene/Prompt state without recreating parallel truth.

## CORE-AUDIT-005 — executors / generated video / export / desktop

| Capability | Disposition | Status |
|---|---|---|
| Brain Router concept | ADAPT | TEST_REVIEWED |
| Brain Provider abstraction | ADAPT to Capability Executor | DECIDED |
| Codex App Server integration | ADOPT + ADAPT | TEST_REVIEWED |
| Legacy Phase Engine | SELECTIVE PORT / SUPERSEDE | DECIDED |
| Generated-video ingest | ADOPT + HARDEN | SOURCE_MAPPED |
| Generation attempt history | ADOPT | SOURCE_MAPPED |
| Reject semantics | ADOPT | SOURCE_MAPPED |
| Sequence approval/lock | ADOPT + ADAPT | SOURCE_MAPPED |
| Approved END → continuity | ADOPT | SOURCE_MAPPED |
| Diagnostics | ADOPT + EXTEND | SOURCE_MAPPED |
| Raw project ZIP | ADAPT | SOURCE_MAPPED |
| RDV verified export/checksum | KEEP_RDV | DECIDED |
| Electron shell | ADOPT + ADAPT + HARDEN | SOURCE_MAPPED |

Key corrections for RDV:

- specialists/executors receive minimum typed context instead of whole-project context by default;
- generated video must pass technical media probe before review/approval;
- measured duration/codec/resolution must come from media, not planned sequence metadata;
- rejected attempts never advance canonical continuity;
- project export keeps RDV SHA-256/content-hash verification;
- local Electron API should use a per-session authorization secret in addition to loopback binding.

## Architecture audit result

The main production architecture audit is complete enough to begin implementation.

Target chain:

```text
RDV UI
  ↓
Authenticated Local API
  ↓
StageRegistry / CapabilityRegistry
  ↓
Typed Production State
  ↓
SequencePromptState
  ↓
Capability Executor Router
  ↓
Attempt + Evidence
  ↓
Review / Approval
  ↓
Continuity advancement
```

No executor, provider prompt, UI component or Codex thread owns project truth.

## Implementation plan

Canonical RDV implementation plan:

`docs/CONTINUITY_STUDIO_PORT_PLAN_V1.md`

First vertical slice:

```text
Stick Character Walk Test
```

Implementation order is intentionally incremental and non-destructive. The current RDV remains recoverable until the new chain proves parity.

## Remaining audit work

May proceed alongside narrow implementation when dependencies are isolated:

- dependency/license review for distributed runtime;
- exact RDV UI binding details;
- full import/migration strategy for old RDV projects;
- provider-profile freshness mechanism;
- future visual QA engines;
- specialist extraction from Studio RFL.

## Implementation gate

Every source-derived implementation PR must include:

1. exact source files + SHA;
2. provenance entry;
3. behavior/tests;
4. deliberate RDV differences;
5. persistent-state impact;
6. license/attribution action;
7. acceptance criteria.
