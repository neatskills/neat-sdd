# Refinement Skill

Refines planned features into detailed specifications ready for implementation. Scans the `features/` directory for features with `state: planned`, presents them for selection, then derives detailed acceptance criteria, blast area, risks, and dependencies from the knowledge base.

**Workflow:** `planned` → `refined` → `implemented`

Requires at least one feature with `state: planned` — run `neat-sdd-planning` first to create features.

## Skills

| Skill | What It Does |
| --- | --- |
| `neat-sdd-refinement` | Refine planned features with acceptance criteria, risks, and dependencies |

## How It Works

**KB-Driven Derivation:** Uses knowledge base context to derive technical details:

- **L3 components** for blast area expansion
- **L6 risks** for risk identification
- **Domain knowledge** for high-precision component analysis

**Precision Levels:**

- **High:** Domain knowledge exists → comprehensive component query
- **Medium:** L3 analysis only → component list matching
- **Low:** Technical Decisions only → basic derivation

**Auto-Detects Dependencies:** Extracts component names from blast area, queries KB for infrastructure requirements, then cross-references features' "Components affected" sections to find providers. Presents detected dependencies with reasoning for user approval.

**Pattern-Based Criteria:** Derives acceptance criteria from feature type (real-time, auth, API) with testable conditions.

## Output

```text
docs/specs/<product>/features/
  feature-{goal}-{nn}-{slug}.md      # Updated with state: refined
  feature-{goal}-{nn}-{slug}.md
  ...
```

Each refined feature contains:

- Goal (one sentence: what's true when this feature is done)
- Blast Area (components affected, with precision level annotation)
- Acceptance Criteria (testable conditions defining "done")
- Risks (extracted from L6 findings, with mitigation strategies)
- Dependencies (auto-detected from component relationships, user-approved)

## Re-Refinement

When re-refining an existing feature, runs impact analysis — shows scope changes and downstream effects on other features, plans, and gate logs before overwriting. Recommends running `neat-sdd-audit` after approval.
