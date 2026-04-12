---
name: neat-sdd-audit
description: Use when multiple features are implemented and need cross-feature verification - checks dependency integration, blast area coordination, implementation gaps, and pattern consistency - requires implemented features
---

# neat-sdd Cross-Feature Audit

**Role:** You are a QA engineer who verifies cross-feature integration, coordination, and consistency after implementation.

**Usage:** `neat-sdd-audit <product>` or `neat-sdd-audit` (will prompt for product)

**Requires:** Multiple features with `state: implemented` in `docs/specs/<product>/features/`

**Not for:** Single feature verification (use `neat-sdd-gate`), documentation consistency during refinement (use `neat-sdd-refinement`)

## Overview

Verifies cross-feature integration: dependency integration, blast area coordination, implementation gaps, pattern consistency. Produces severity-ranked findings (ERROR/WARNING/INFO).

**Scope:** `neat-sdd-gate` checks single feature; `neat-sdd-audit` checks multi-feature integration.

## When to Use

Run AFTER implementation when features have `depends_on` or overlapping blast areas, batch completes, or before milestones.

**Don't use for:** Single feature (`neat-sdd-gate`), doc checks (`neat-sdd-refinement`), or pre-implementation.

## Quick Reference

| Step | What |
|------|------|
| 1 | Setup: locate specs, load features, determine which checks apply |
| 2 | Run applicable checks (1-4) |
| 3 | Present audit report with severity-ranked findings |
| 4 | Handle user choice: fix (recommend actions), accept (log rationale), done |
| 5 | Save audit report to `docs/specs/<product>/audit.md` |

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| ERROR | Integration broken, gaps, or conflicts | Should address before merge/release |
| WARNING | Inconsistencies or weak coordination | Review recommended |
| INFO | Verification passed or awareness only | No action needed |

All severities are advisory - user decides whether to address.

## Setup

1. **Locate specs.md** per [standard procedure](../references/specs-location.md). Read Outputs section for features path.
2. **Glob features:** Find all `feature-*.md` files. If none with `state: implemented` → **STOP:** "No implemented features found. Audit requires implemented features."
3. **Load feature data:** For each implemented feature, read:
   - Frontmatter: name, goal, state, depends_on
   - Blast Area: components, precision
   - Acceptance Criteria
4. **Determine applicable checks:** Based on feature data, decide which checks apply (see Checks section)
5. **Construct output path** per [output path rules](../references/output-conventions.md)

## Checks

Run all checks whose inputs exist. Skip others and note in report.

### Check 1: Dependency Integration Verification

**Inputs:** Features with `depends_on` in frontmatter (≥1 pair)

**Skip if:** No features with `depends_on` field

**Verifies:** Dependent features actually integrate with dependency features in code

**Algorithm:** Build dependency pairs, verify each pair (query KB for exports, grep dependent files for imports/usage, verify API patterns), classify findings.

| Severity | Condition |
|----------|-----------|
| ERROR | No imports or wrong API usage |
| WARNING | Weak coupling |
| INFO | Correct integration |

### Check 2: Blast Area Overlap Coordination

**Inputs:** Features with overlapping blast areas (≥1 overlap)

**Skip if:** No overlapping blast areas found

**Algorithm:** Build blast area map, find overlaps, query KB for approaches, detect conflicts vs coordination.

| Severity | Condition |
|----------|-----------|
| ERROR | Conflicting approaches |
| WARNING | Different modifications without coordination |
| INFO | Coordinated (referenced in dependencies/risks) |

### Check 3: Implementation Gap Detection

**Inputs:** Feature acceptance criteria, implementations (≥2 features)

**Skip if:** < 2 implemented features

**Algorithm:** Grep criteria for integration keywords, verify implied integrations exist in code via KB queries.

| Severity | Condition |
|----------|-----------|
| ERROR | Criteria requires integration but code has no connection |
| WARNING | Related features without integration |
| INFO | Integration points match spec |

### Check 4: Cross-Feature Pattern Consistency

**Inputs:** Multiple features in same domain (≥2 per domain)

**Skip if:** < 2 features per domain

**Algorithm:** Group by domain from blast area precision, query KB for pattern comparison, check intentionality if divergent.

| Severity | Condition |
|----------|-----------|
| WARNING | Patterns differ without rationale |
| INFO | Intentional or consistent patterns |

## Process

### Step 1: Run Applicable Checks

Execute checks 1-4 based on inputs available. Collect findings. Note skipped checks with reasons.

### Step 2: Present Audit Report (BLOCKING)

**Format:**

```markdown
# Audit Report: <product>
Date: YYYY-MM-DD HH:MM
Features Audited: N features (M implemented)

## Summary
- Errors: N
- Warnings: N
- Info: N
- Checks Skipped: [list with reasons]

## Check 1: Dependency Integration
[Table if run, or "SKIPPED: No features with depends_on"]

## Check 2: Blast Area Overlaps
[Table if run, or "SKIPPED: No overlapping blast areas"]

## Check 3: Implementation Gaps
[Table if run, or "SKIPPED: < 2 implemented features"]

## Check 4: Pattern Consistency
[Table if run, or "SKIPPED: < 2 features per domain"]

## Verdict
[PASS | FAIL] - [Summary of ERRORs if FAIL]
```

**Present to user:** Show findings table, summary, then ask: "Proceed? **Fix** | **Accept** | **Done**"

### Step 3: Handle User Choice

**Fix:** Recommend actions for ERRORs and WARNINGs:

| Finding Type | Recommended Action |
|--------------|-------------------|
| Dependency integration broken | Fix integration in dependent feature, re-run `neat-sdd-gate` |
| Blast area conflict | Coordinate features - may need refactoring, re-run `neat-sdd-gate` for affected features |
| Implementation gap | Implement missing integration, update acceptance criteria verification |
| Pattern inconsistency | Align patterns or document intentional divergence in Technical Decisions |

**Accept:** Ask which findings to accept. Get one-line rationale per finding. Log in report under each finding.

**Done:** Save report as-is.

### Step 4: Save Audit Report

Save to `docs/specs/<product>/audit.md` (overwrite each run). Register in specs.md Outputs per [standard format](../references/output-conventions.md):

```markdown
- Audit: docs/specs/<product>/audit.md
```

If entry exists, replace. If new, append to Outputs section.

## Red Flags - Signs You're Skipping Checks

These thoughts mean STOP - you're rationalizing away verification:

- "Individual gates passed, integration must be fine"
- "Tests pass, so features integrate correctly"
- "Time pressure means skip deep checks"
- "Tech lead reviewed, don't need to verify code"
- "Just check documentation consistency"
- "Quick sanity check is enough"
- "Developer is tired, rubber-stamp to help"

**All of these mean: Run the full checks. No shortcuts.**

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Checking only docs/tests | Verify implementation integration in code |
| Skipping code reading | Read implementations, don't infer from specs |
| Running before implementation | Requires `state: implemented` |
| Using for single feature | Use `neat-sdd-gate` instead |
| Inferring from proximity | Verify imports/usage explicitly |

## KB Registration

Register per [standard format](../references/output-conventions.md): `- Audit: docs/specs/<product>/audit.md`

## Output

`docs/specs/<product>/audit.md`
