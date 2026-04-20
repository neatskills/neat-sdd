---
name: neat-sdd-planning
description: Use when user has a high-level goal or objective that needs to be broken down into discrete features for refinement
---

# Planning

**Role:** You are a product owner who clarifies ambiguous goals and decomposes them into discrete, refinable features.

**Core principle:** Clarify before decomposing. Detailed evaluation happens in refinement.

## Overview

Decomposes goals into build-ready features via KB-guided clarification, architecture validation, and automated requirements derivation.

## When to Use

Use when goals need decomposition into features ready for build. Not for detailed design or implementation.

## Quick Reference

| Step | What |
|------|------|
| 1 | Query KB for context overview (structured index) |
| 2 | Generate clarifying questions → query KB for factual answers → ask user for decisions |
| 3 | Synthesize capabilities from goal (5-15 features) |
| 4 | Cross-check against architecture → identify components, type, risks |
| 5 | Present features, iterate on feedback |
| 6 | For each feature: derive goal statement, auto-detect dependencies, extract risks |
| 7 | Derive goal identifier, save as `feature-{goal}-{nn}-{slug}.md` with `state: planned`, update specs.md KB |

## Setup

1. **Locate specs.md** ([procedure](../references/specs-location.md))
2. **Construct output path** ([rules](../references/output-conventions.md))
3. If Plan entry exists in KB, ask "Update existing plan or start fresh?"

## Process

1. Query KB for overview (structured index) per [knowledge query pattern](../references/output-access.md)
2. Generate clarifying questions, query KB for factual answers, ask user for decisions
3. Synthesize capabilities from goal, cross-check against architecture
4. Present features with component mapping and risks, iterate on feedback
5. For each feature: derive goal statement, auto-detect dependencies, extract risks from KB
6. Save to `features/<slug>.md`, update specs.md KB

### Step 1: Load Context Overview

Query KB per [knowledge query pattern](../references/output-access.md):

Agent-driven discovery:

```markdown
Invoke: neat-knowledge-extract "What is the tech stack, integrations, architectural components, main workflows, and business logic?"
```

Agent evaluates keyword matches and decides loading depth (summary/sections/full based on ROI).

Parse JSON: Extract tech_stack, integrations, components, workflows, business_logic from returned documents.

If invoke fails (neat-knowledge not installed): Fall back to direct reads automatically, log "neat-knowledge not available, using direct reads"

Fallback: Read specs.md, parse KB entries, read analysis directly.

If KB minimal: Use goal only; factual questions become decision questions.

### Step 2: Clarify (Required Before Step 3)

**Generate questions:** List 2-5 questions about ambiguities (scope, users, integration, constraints, priorities). Categorize: **Factual** (KB) or **Decision** (user).

**Query KB for factual answers:** Query KB per [knowledge query pattern](../references/output-access.md) with explanations and citations. Example: "Does system support real-time?" → Query "What workflows and integration patterns support real-time operations?" Agent evaluates and loads relevant analysis sections + domain knowledge, synthesize with citations.

**Ask user decision questions:** Present only questions KB cannot answer (priorities, permissions, strategic choices).

### Step 3: Decompose and Cross-Check

**Synthesize capabilities:** Break goal into functional capabilities (user-centric, independently refinable, clear value). Aim for 5-15 features. Avoid technical layers or vague goals.

**Cross-check against architecture:** For each feature, identify components affected (from architectural components in KB), type (Incremental/Transformative), risks (blast radius, conflicts, ordering). Query KB for deeper understanding if needed with specific questions about component relationships or integration patterns.

### Step 4: Present and Iterate

Show: Name, brief description, components affected, type, risks. Iterate on feedback until approved.

### Step 5: Derive Requirements Per Feature

For each approved feature:

**Goal statement (automatable):** Derive one-sentence outcome from feature name and description. Pattern: "Users can {action} via {mechanism}" or "{System} enables {capability}".

**Auto-detect dependencies:** Parse components from all features in this planning session. For each feature, check if its components depend on infrastructure from other features:
- Query KB: "What infrastructure does {component} require?"
- Cross-reference: Which other features provide that infrastructure?
- Create depends_on list with feature identifiers

**Extract risks from KB:** Query KB with "What are known risks for {components}?" Extract relevant risks from analysis. If none found, use "None identified from KB."

### Step 6: Save and Update

**Derive goal identifier:** Extract 1-3 key terms from user's goal, create slug (lowercase, hyphens, max 20 chars).

**Examples:**

- "Implement OAuth authentication system" → `auth`
- "Add real-time collaborative editing" → `realtime-collab`
- "Build API v2 with GraphQL" → `api-v2`
- "Migrate to microservices architecture" → `microservices`

**Save feature docs:** Create `docs/specs/<product>/features/feature-{goal}-{nn}-{slug}.md` using format below. Use two-digit numbers (01, 02, etc.) scoped per goal identifier (each goal starts numbering at 01).

**Update specs.md Outputs:** Add Features entry per [standard format](../references/output-conventions.md):

```markdown
- Features: docs/specs/<product>/features/ (8 features)
```

**Recommend next step:** Suggest `neat-sdd-build` to design and implement features. Build will derive detailed acceptance criteria during design phase.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Skipping clarification | Always clarify before decomposing |
| Asking factual questions | Query KB for facts, ask user for decisions |
| Wrong granularity | 5-15 capabilities with user value |
| Skipping architecture cross-check | Must identify components, type, risks |
| Not deriving goal identifier | Derive from user's goal in Step 6 |
| Skipping requirements derivation | Must derive goal statements, dependencies, risks in Step 5 |

## Output

Save to `docs/specs/<product>/features/feature-{goal}-{nn}-{slug}.md` per [output path rules](../references/output-conventions.md).

**Format:**

```markdown
---
name: Feature Name
goal: goal-identifier
state: planned
created: YYYY-MM-DD
depends_on: [feature-id-1, feature-id-2]  # If dependencies detected
---

# Feature Name

Brief 1-2 sentence description.

## Goal

One-sentence outcome statement derived from feature description.

## Components Affected

**Components affected:** component-a, component-b

**Type:** Incremental | Transformative

**Cross-repo format (if applicable):** `[repo-name] component-name`

## Acceptance Criteria

(To be derived during design phase in Build)

## Risks

[Risks extracted from KB, or "None identified from KB"]
```

**Naming:**

- **Goal identifier:** 1-3 key terms from goal (lowercase, hyphens, max 20 chars)
- **Number:** Scoped per goal (01, 02, etc.)
- **Slug:** Feature name (lowercase, hyphens only, e.g., "Real-Time Editing!" → `realtime-editing`)

**Terminology standard:**

- **Section heading:** `## Components Affected` (always capitalized)
- **Field label:** `**Components affected:**` (bold with colon)
- **Inline reference:** "components" or "blast area" (lowercase)
