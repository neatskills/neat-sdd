# neat-sdd-audit

Cross-cutting consistency audit for the SDD knowledge base — verifies planning, features, domain knowledge, and coverage align after pipeline changes.

## Purpose

After feature/plan/domain knowledge changes, audit the entire knowledge base for:

- Traceability gaps between planning and features
- Cross-feature consistency (entry criteria, dependencies, blast areas)
- Domain knowledge coverage and staleness
- Refinement log synchronization

Acts as a QA gate to catch cross-cutting issues before they compound.

## Usage

```text
neat-sdd-audit <product>
neat-sdd-audit          # will prompt for product
```

## Prerequisites

- specs.md with at least a Plan or Features KB entry
- Not for: Feature implementation (use `neat-sdd-gate`), analysis/planning/refinement (use respective skills)

## What It Checks

### 1. Planning → Features Traceability

- Uncovered actionable items (ERROR)
- Stale feature references (ERROR)
- Orphan features (WARNING)

### 2. Feature → Feature Consistency

- Broken entry criteria (ERROR)
- Circular dependencies (ERROR)
- Blast area overlaps (WARNING)

### 3. Domain Knowledge Coverage

- Low/medium precision blast areas (WARNING/INFO)
- Stale domain knowledge (WARNING)

### 4. Refinement Status Sync

- Unlogged additions/deletions (WARNING)
- Stale scope changes (WARNING)

## Workflow

1. **Read KB** — Load all entries from specs.md
2. **Run checks** — Execute all 4 checks (skip if inputs missing)
3. **Present report** — Findings table with severity (ERROR/WARNING/INFO)
4. **User decision** — Fix (get skill recommendations), Accept (log rationale), or Done
5. **Save report** — Write to `docs/specs/<product>/audit.md`, register in specs.md

## Output

- `docs/specs/<product>/audit.md` — audit report (overwritten each run)
- Registered in specs.md Outputs: `Audit: docs/specs/<product>/audit.md`

## Severity Levels

All findings are advisory — user decides whether to address:

- **ERROR** — Should address before proceeding
- **WARNING** — Review recommended
- **INFO** — Awareness only
