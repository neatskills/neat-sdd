# Spec Gate Skill

Verify implementation alignment against feature specifications using a three-layer gate: structural check, automated verification, and independent review.

## What It Does

Reads a feature doc's acceptance criteria and verifies implementation alignment:

1. **Layer 1: Structural Check** — mechanical validation of required structure, traceability, and file existence
2. **Layer 2: Automated Verification** — mode-specific checks (traceability depth + task specificity for design, build/test/coverage for execute)
3. **Layer 3: Independent Review** — fresh subagent performs semantic review with no prior session history

Produces a **PASS/FAIL verdict**: PASS if no FAILs across all layers (WARNs are acceptable), FAIL if any FAIL detected. Short-circuits: a FAIL in any layer skips remaining layers.

**Logging:** Provides inline progress output during execution (✓/⚠/✗ symbols) and saves detailed findings to gate log for audit trail.

## Skills

| Skill | What It Does |
|-------|-------------|
| `neat-sdd-gate` | Verify design + tasks or code against feature doc acceptance criteria |

## Modes

| Mode | Verifies | Against |
|------|----------|---------|
| `design` | Brainstorming spec (design + tasks) | Feature doc acceptance criteria |
| `execute` | Codebase (blast area files + git diff) | Feature doc acceptance criteria |

## When to Use

- **After brainstorming** — does the design + tasks cover the feature's acceptance criteria? (verifies spec structure and task traceability)
- **After implementation** — does the code satisfy acceptance criteria? (runs build/test/coverage checks + semantic review)
- **Standalone verification** — verify any agent or human implementation with independent review

**Prerequisites:** Feature doc with Acceptance Criteria (state: refined+) from `neat-sdd-refinement`

## Output

**Console:** Inline progress logging during execution (🔍 layer announcements, ✓/⚠/✗ check results)

**File:** Gate log appended to `docs/specs/<product>/features/<feature-name>-gates.md`

Each gate run includes:

- Check results table per layer (PASS/WARN/FAIL)
- **Detailed Findings** sections:
  - Layer 1: Files examined, criteria/task mappings, branch/diff stats
  - Layer 2: Depth analysis (design) or command results + coverage (execute)
  - Layer 3: Subagent prompt summary, parsing statistics
- Final verdict (PASS/FAIL) with blockers if failed

Example:

```text
docs/specs/<product>/features/
  feature-api-01-versioning.md        # feature doc
  feature-api-01-versioning-gates.md  # gate log (historical record)
```
