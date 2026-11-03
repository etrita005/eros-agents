# Roadmap: EROS

## Overview

This milestone turns project intent into a bounded, traceable scope baseline. Phase 1 defines the long-term EROS system composition, area responsibilities, and the portability contract. Phase 2 separates Forge's implemented capabilities from future system scope and records explicit exclusions. Phase 3 establishes requirement-to-phase traceability with evidence status and completes a cross-document consistency review. No runtime, hardware, firmware, Forge implementation, or CI/CD delivery work is part of this roadmap.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: System Composition and Portability Boundary** - Define the long-term system composition, area responsibilities, and the portability contract. (completed 2026-11-04)
- [ ] **Phase 2: Implemented Scope and Explicit Exclusions** - Separate Forge's implemented capabilities from future system scope and record baseline exclusions.
- [ ] **Phase 3: Baseline Traceability and Consistency Review** - Map every requirement to a phase with evidence status and complete the cross-document review.

## Phase Details

### Phase 1: System Composition and Portability Boundary
**Goal:** Readers can see the long-term EROS system composition, the responsibility of each area, and the platform portability contract in the baseline documents.
**Depends on:** Nothing (first phase)
**Requirements:** SCOPE-01, SCOPE-04
**Scope baseline:** `.planning/SCOPE.md`
**Success Criteria** (what must be TRUE):
  1. Every long-term composition area of EROS is listed with exactly one stated responsibility, and no listed area lacks an assigned responsibility.
  2. The composition distinguishes EROS system areas from build and delivery infrastructure, so a reader can identify Forge's role without treating Forge as the whole operating system.
  3. The portability rule is recorded for platform-sensitive areas: common source and APIs where practical, platform-specific builds where required.
  4. The documents state explicitly that identical binaries are not guaranteed across platforms.
**Plans**: 1/1 plans complete
- [x] `01-01-PLAN.md` — Create the scope baseline and link it from PROJECT.md, REQUIREMENTS.md, and ROADMAP.md

### Phase 2: Implemented Scope and Explicit Exclusions
**Goal:** Readers can distinguish Forge's implemented capabilities from unimplemented EROS system scope, and the baseline's exclusions are explicit and reasoned.
**Mode:** mvp
**Depends on:** Phase 1
**Requirements:** SCOPE-02, SCOPE-03
**Success Criteria** (what must be TRUE):
  1. The baseline states which capabilities Forge implements today and identifies the authoritative Forge evidence behind each statement.
  2. The baseline states which EROS system areas have no implementation in this workspace, without presenting them as delivered.
  3. Every excluded area — runtime, BSP and hardware support, firmware, CI/CD, deployment, and target acceptance — appears in an explicit exclusion list with a reason.
  4. No excluded capability is described as delivered or accepted in the baseline documents.
**Plans**: TBD

### Phase 3: Baseline Traceability and Consistency Review
**Goal:** The baseline is fully traceable and internally consistent, with every unresolved item recorded and dispositioned.
**Mode:** mvp
**Depends on:** Phase 2
**Requirements:** TRACE-01, REVIEW-01
**Success Criteria** (what must be TRUE):
  1. Every v1 requirement maps to exactly one roadmap phase, and the traceability table shows no unmapped or duplicate requirement.
  2. Each traceability row records an evidence status that distinguishes documented scope from implemented capability and verified result.
  3. A recorded review covers PROJECT.md, REQUIREMENTS.md, and ROADMAP.md and lists every inconsistency found or states that none were found.
  4. Every unresolved item from the review is recorded with a disposition, and no item is left without a next action or deferral decision.
  5. The three documents agree on scope statements, requirement identifiers, and phase assignments after review, or every remaining disagreement is recorded as unresolved.
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. System Composition and Portability Boundary | 1/1 | Complete    | 2026-11-04 |
| 2. Implemented Scope and Explicit Exclusions | 0/TBD | Not started | - |
| 3. Baseline Traceability and Consistency Review | 0/TBD | Not started | - |
