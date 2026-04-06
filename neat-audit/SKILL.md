---
name: neat-sdd-audit
description: Use when verifying cross-cutting consistency across the knowledge base after pipeline changes - checks features, domain knowledge, and coverage for gaps, staleness, and conflicts - requires existing specs.md
---

# Knowledge Base Audit

**Role:** You are a QA engineer who audits the entire SDD knowledge base for cross-cutting consistency — verifying that features, domain knowledge, and coverage align after changes.

**Usage:** `neat-sdd-audit <product>` or `neat-sdd-audit` (will prompt for product)

**Requires:** specs.md with Features KB entry.

**Not for:** Feature implementation (use `neat-sdd-gate`), analysis/planning/refinement (use respective skills).

## Overview

Cross-cutting consistency verification across the knowledge base. Runs two checks: feature→feature consistency and domain knowledge coverage. Produces severity-ranked findings (ERROR/WARNING/INFO) with skill recommendations for fixes. All findings are advisory — user decides whether to address.

## When to Use

- After feature/plan/domain knowledge changes
- When checking for stale references, gaps, or conflicts

## Quick Reference

| Step | What |
|------|------|
| 1 | Locate specs.md, construct output path, read all KB entries |
| 2 | Run checks 1-2 (consistency, coverage) |
| 3 | Present audit report with severity-ranked findings |
| 4 | Handle user choice: fix (recommend skills), accept (log rationale), or done |
| 5 | Save audit report to `docs/specs/<product>/audit.md` |

## Setup

1. **Locate specs.md** per [standard procedure](../references/specs-location.md). Read Outputs section. If no Features entry → **STOP:** "No features found. Run planning first to create features."
2. **Construct output path** per [output path rules](../references/output-conventions.md).
3. **Load KB:** Query KB per [knowledge query pattern](../references/output-access.md): "Load all KB entries (features, domain knowledge, analyses) for audit". Re-invoke for specific entries when checks need verification.

## Severity Levels

| Level | Action |
|-------|--------|
| ERROR | Should address before proceeding |
| WARNING | Review recommended |
| INFO | Awareness only |

All severities are advisory — user decides.

## Checks

Run all checks whose inputs exist. Skip others and note in report.

### 1. Feature → Feature Consistency

**Inputs:** feature docs

| Check | Severity | Condition |
|-------|----------|-----------|
| Broken dependencies | ERROR | Feature dependency (`depends_on`) references non-existent feature (deleted or renamed) |
| Circular dependencies | ERROR | Feature dependency chain forms cycle |
| Blast area overlap | WARNING | Features modify same components without cross-reference in dependencies or Risks |

Build dependency graph by:

1. Glob all `feature-*.md` files to get valid feature names
2. For each feature, parse `depends_on` frontmatter field
3. Validate all dependency references exist in feature list (check for missing nodes including renamed features)
4. Detect cycles in dependency chain

Compare blast areas for overlaps.

### 2. Domain Knowledge Coverage

**Inputs:** feature docs, domain knowledge, discoveries

| Check | Severity | Condition |
|-------|----------|-----------|
| Low precision blast area | WARNING | Blast area from Technical Decisions only (no analysis, no domain knowledge) |
| Medium precision blast area | INFO | Blast area from L3 only (no domain knowledge for domain) |
| Stale domain knowledge | WARNING | Domain knowledge references files/components not in latest analysis L3 |

Build coverage map (per `neat-sdd-refinement` Setup 5), determine domains, cross-reference domain knowledge. Verify Primary References against L3 via knowledge subagent.

**Example:** Feature shows "Precision: Low (Technical Decisions)" → audit flags as WARNING to run `neat-sdd-domains` for affected domain.

## Process

### Step 1: Run All Checks

Run checks 1-2. Collect findings. Note skipped checks (missing inputs).

### Step 2: Present Audit Report (BLOCKING)

**No findings:** "Audit for <product> — all clear. Checks run: [list]. Checks skipped: [list with reason]."

**Findings:** Table with # | Check | Severity | Finding, then summary "Errors: N | Warnings: N | Info: N | Checks skipped: [if any]", then "Proceed? **Fix** | **Accept** | **Done**"

### Step 3: Handle User Choice

**Fix:** Recommend skill per finding type (ERRORs and WARNINGs only):

| Finding | Action |
|---------|--------|
| Broken entry criteria / Circular dependencies / Blast area overlap | `neat-sdd-refinement` |
| Low/Medium precision / Stale domain knowledge | `neat-sdd-domains` |

**Accept:** Ask which findings. Get one-line rationale per finding. Log in report.

**Done:** Save as-is.

### Step 4: Save Audit Report

Save to `docs/specs/<product>/audit.md` (overwrite each run). Register in specs.md Outputs per [standard format](../references/output-conventions.md): `- Audit: docs/specs/<product>/audit.md` (replace previous entry).

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Partial KB reads | Read all KB entries |
| Treating findings as blocking | User decides — all advisory |
| Recommending actions for INFO | INFO awareness only |
| Auto-fixing | Present, wait for decision |
| Skipping checks when inputs missing | Skip specific checks, report what was skipped |
| Wrong skill recommendation | Use Step 3 table |
| Vague findings | Cite IDs, paths, values |
| Timestamped audit files | Use `audit.md`, overwrite |
