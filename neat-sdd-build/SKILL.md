---
name: neat-sdd-build
description: Use when building a planned feature end-to-end - orchestrates design, planning, and implementation with spec gate verification - requires existing feature docs from planning
---

# Build

**Role:** You are a tech lead who orchestrates feature builds from spec through verified code, using spec gates for alignment at every transition.

## Overview

Orchestrates continuous parallel builds: readiness → brainstorming → ADR extraction → writing-plans → risk assessment + spec gate → execution → risk assessment + spec gate. Dynamic parallel queuing automatically prepares and spawns independent features as others complete.

**Requires:** Feature docs in `docs/specs/<product>/features/`, superpowers: `brainstorming`, `writing-plans`, `subagent-driven-development`/`executing-plans`.

## When to Use

After planning features with `neat-sdd-planning` for end-to-end execution with verification where implementation traces to feature doc.

**Not for:** Standalone implementation (use superpowers directly)

## Quick Reference

| Step | What |
|------|------|
| 1 | Pick feature(s), validate independence |
| 2 | Automatic state detection - resume from Status/plan/design or fresh |
| 3 | Check entry criteria, doc quality (per feature) |
| 4 | Discover blast area (per feature) |
| 5 | Design - query KB (per feature) |
| 6 | Extract ADRs (per feature) |
| 7 | Write TDD tasks (per feature) |
| 8 | Analyze dependencies (per feature) |
| 9 | Risk assessment → gate design + plan if needed (per feature) |
| 10 | Spawn all features in background (isolated worktrees) |
| 11 | Monitor completion, merge as finished |
| 12 | Risk assessment → gate code if needed (per feature) |
| 13 | Update feature doc status, auto-ingest to KB (per feature) |
| 14 | Prompt for audit if 2+ features with relationships |
| 15 | Continue building |

## Setup

Locate specs.md ([procedure](../references/specs-location.md)), construct output path ([rules](../references/output-conventions.md)), read features with `state: planned` (no `## Status`).

**Feature state:** `state: implemented` = code complete + verified. Build updates frontmatter + appends `## Status`.

## Process

### Step 1: Pick Feature(s) (BLOCKING)

Present features with entry criteria. User selects 1+ features to build.

**Independence Validation:** Check `depends_on`, blast areas, task plans for conflicts. If conflicts: present details, ask user to adjust. If independent: proceed with batch workflow (isolated worktrees).

### Step 2: Automatic State Detection

Per [State Detection Algorithm](references/state-detection.md). `## Status` → "Build again?". Plan exists → resume Step 10. Design + ADRs → resume Step 7.

### Step 3: Readiness Check

**Prerequisites:** Must NOT have `## Status`. Validate `depends_on` features exist. Missing → STOP, recommend `neat-sdd-audit`.

**Doc Quality:** Score components (identified), risks (extracted), goal (one-sentence). 3/3=High, 2/3=Medium, 0-1/3=Low. High/Medium → Step 4. Low → return to planning.

### Step 4: Discover Blast Area Files

Parse components → keywords → search → rank → confirm.

### Step 5: Brainstorming

**Load KB:** Read specs.md KB section. If found: invoke `neat-knowledge-extract` with blast area questions (fallback: direct reads). If NO KB: read specs.md, parse entries, read files.

**Invoke:** Pass "Use neat-knowledge-extract for queries. Check KB BEFORE asking user. After designing the implementation approach, derive detailed acceptance criteria that reflect your chosen design decisions." Invoke `/brainstorming` with feature doc (goal, components, risks from planning), specs.md, KB context, blast area. Output: `docs/superpowers/specs/`.

**After brainstorming completes:** Update feature doc with:
- `designed: YYYY-MM-DD` (frontmatter)
- `spec_doc: path/to/design.md` (frontmatter)
- Acceptance criteria section (from design decisions)

Proceed to Step 6.

### Step 6: Extract ADRs

Invoke `neat-sdd-adr {design-spec} {feature-doc} integrated`. Outcomes: SUCCESS → Step 7 | MINOR → auto-fix | MAJOR → re-brainstorm.

### Step 7: Writing Plans

Invoke `/writing-plans` with design spec and feature doc. Output: `docs/superpowers/plans/`.

### Step 8: Dependency Analysis

Per [Dependency Analysis Algorithm](references/dependency-analysis.md): Count tasks, build dependency graph, identify layers, present breakdown.

### Step 9: Risk Assessment + Spec Gate — Design + Plan

Analyze design complexity per [risk assessment algorithm](references/risk-assessment.md#design-phase-assessment). If gate runs: ensure artifacts exist, invoke `neat-sdd-gate <product>` (auto-detects design mode).

### Step 10: Spawn Execution Agents

For each prepared feature: spawn agent with worktree isolation (`run_in_background: true`), pass layer-by-layer tasks + docs, track feature→agent→worktree. After all spawned, continue immediately to Step 11. See [Parallel Execution Reference](references/parallel-execution.md).

### Step 11: Monitor Completion & Dynamic Queuing

Wait for background agents to complete. As each finishes:

1. Retrieve completion status and worktree path
2. **BLOCKING - Simplify in worktree:** Invoke `/simplify`, fix issues, commit. MUST run before merge. No exceptions:
   - Don't skip because "code looks clean"
   - Don't skip because "agent already reviewed"
   - Don't skip because "simple changes"
   - Simplify catches: reuse opportunities, quality issues, efficiency problems
3. Merge worktree to main branch, run integration tests
4. If tests pass: Run Step 12 (Risk Assessment + Gate), then Step 13 (Update state: implemented). State MUST update BEFORE checking for next feature.
5. If tests fail: Mark failed, log, continue monitoring others

**After Step 13 complete:**

1. Check for remaining `state: planned` features
2. Validate independence from running features (depends_on, blast areas, task plans)
3. If independent: run Steps 3-9, spawn at Step 10 immediately
4. If conflicts: wait for next completion

**Continue until:** No planned features remain OR all conflict with running ones.

### Step 12: Risk Assessment + Spec Gate — Execute

Analyze implementation complexity per [risk assessment algorithm](references/risk-assessment.md#execute-phase-assessment). If gate runs: ensure artifacts exist, invoke `neat-sdd-gate <product>` (auto-detects execute mode).

### Step 13: Update Feature Doc (CRITICAL - BLOCKING)

MUST complete before spawning next feature.

1. Set `state: implemented`, append `## Status` section
2. Auto-ingest: if neat-knowledge-ingest installed + KB exists, invoke, log success
3. Announce completion

**Red Flag:** Checking for next feature before updating state - STOP.

### Step 14: Audit Prompt (If Applicable)

If 2+ implemented features AND current has `depends_on` or overlaps: prompt "Run audit? Y/n". If Y: invoke `neat-sdd-audit`.

### Step 15: Completion

Announce: "All features built."

## Gate Handling

Gates run if risk assessment determines medium/high risk. Low-risk skip with logged reasoning. If issues arise, manually invoke `neat-sdd-gate <product>`.

**On failure:** Present findings, ask: "Fix plan | Fix design | Accept as-is | Abort". Max 3 attempts. If wrong criteria revealed, surface, update if approved, re-run.

## Common Mistakes

See [Common Mistakes Reference](references/common-mistakes.md).

## Output

```text
docs/superpowers/
  specs/YYYY-MM-DD-{goal}-{slug}-design.md
  plans/YYYY-MM-DD-{goal}-{slug}-plan.md

docs/specs/<product>/
  adrs/adr-YYYYMMDD-<decision>.md, index.md
  features/feature-{goal}-{nn}-{slug}.md              # state: implemented, Status
  features/feature-{goal}-{nn}-{slug}-gates.md        # by neat-sdd-gate
```
