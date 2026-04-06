# ADR Skill

Create or extract architectural decisions as formal ADRs (Architecture Decision Records) in MADR format.

## What It Does

**Two modes for creating ADRs:**

**Standalone Mode** (conversational):

- Ask questions to gather decision context
- Generate ADR through guided dialogue
- No design spec required
- Use when documenting decisions independently

**Extraction Mode** (automated):

- Read design spec's "Key Decisions" section
- Extract decisions with context, alternatives, and consequences
- Called automatically by neat-sdd-build pipeline
- May produce zero ADRs if no architecturally significant decisions found (valid outcome)

Both modes output to the same location with shared numbering and identical MADR format.

## Usage

- Standalone: `neat-adr`
- Extraction: `neat-adr <design-spec-path> <feature-doc-path> <mode>`

## When to Use

**Standalone Mode:**

- `/neat-adr` - Start conversational ADR creation
- Document architectural decisions without a design spec
- Retrospectively record past decisions
- Explore trade-offs before committing

**Extraction Mode:**

- Called automatically by neat-sdd-build after brainstorming
- Can also be invoked manually for existing design specs
- Backfill ADRs from historical design docs

## Output

Both modes output to the same location:

```text
docs/specs/<product>/adrs/
  adr-20260330-database-choice.md  # Date-based ADR files
  adr-20260330-api-design.md       # Same day, different slug
  adr-20260331-caching-strategy.md
  index.md                         # Sorted by date descending
```

**File naming:** `adr-{YYYYMMDD}-{slug}.md` format (date-based)
**Numbering:** Date-based (`YYYYMMDD`), eliminates collision/gap issues (see [utilities.md](references/utilities.md#1-adr-numbering))
**Registration:** Both modes register in specs.md Outputs

**Benefits:** Zero collision issues, self-documenting dates, chronologically ordered, distributed team-friendly.
