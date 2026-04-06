# Planning Skill

Clarifies ambiguous goals and decomposes them into discrete, refinable features.

## What It Does

**Core principle:** Clarify before decomposing. Detailed evaluation happens during refinement.

**Workflow:**

1. Query KB (via subagent) for context overview — tech stack, integrations, components, flows, constraints
2. Generate clarifying questions — query KB for factual answers, ask user for decisions
3. Decompose goal into user-centric capabilities (5-15 features)
4. Cross-check against architecture — identify affected components, type (incremental/transformative), risks
5. Present features with component mapping, iterate on feedback
6. Save feature files with `state: planned`, update specs.md KB

Queries knowledge base via subagent (analysis, domain knowledge) to inform decomposition without bloating context. Works on one product at a time.

## Skills

| Skill | What It Does |
| --- | --- |
| `neat-sdd-planning` | Break down goals into discrete feature areas |

## When to Use

- Starting with a high-level goal or objective that needs decomposition
- You have analysis but need to plan what to build
- Defining scope before refinement

**Not for:** Detailed feature specs (use `neat-sdd-refinement`) or immediate implementation (use `neat-sdd-build` with existing features)

## Output

```text
docs/specs/<product>/features/
  feature-{goal}-01-{slug}.md    # Each feature area with state: planned
  feature-{goal}-02-{slug}.md    # Numbering scoped per goal identifier
  ...
```

Examples:

```text
feature-auth-01-oauth-integration.md
feature-auth-02-token-refresh.md
feature-realtime-01-websocket-server.md
```

Each feature file contains:

- Name, state (planned), created date
- Brief description
- Components affected
- Type (incremental/transformative)
- High-level risks

Detailed acceptance criteria, dependencies, and blast area are added during refinement.
