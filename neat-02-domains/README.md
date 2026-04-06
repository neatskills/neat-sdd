# Domain Knowledge Skill

Build comprehensive domain knowledge through focused code investigations that merge into living documentation.

**Skill:** `neat-sdd-domains <domain>` or `neat-sdd-domains` (prompts for domain)

**Prerequisites:** Requires existing `neat-sdd-analysis` output (specs.md and analysis file)

## What It Does

Select domain → build foundation → investigate topics → merge findings into one growing file.

Uses a taxonomy of **7 technical domains** (01-07: infrastructure, data, auth, backend, frontend, mobile, devops) and **business domains** (08-business-*) defined in specs.md. Each investigation becomes a section in the domain knowledge file with findings, impact assessment, recommendations, and references.

Domain knowledge supersedes analysis for its topic. Materials (specs, ADRs, design docs) are optional. Uses Explore subagent for code investigation. Supports contradiction detection when updating existing investigations.

## Workflow

1. Locate specs.md and read target analysis
2. Ask user to select domain (or define new business domain)
3. Check for optional materials (specs, ADRs, design docs)
4. Build/update domain knowledge file
5. Ask for investigation topic
6. Clarify scope with 2-4 questions
7. Investigate via Explore subagent
8. Merge findings with contradiction detection
9. Loop to investigate another topic or finish
10. Offer PDF generation and register in knowledge base

## When to Use

- Building domain knowledge through code investigation
- Investigating specific topics within a domain
- Updating or re-investigating existing knowledge (detects contradictions)
- Defining new business domains

**Not for:** Quick Q&A (use `neat-knowledge-query`) or initial analysis (use `neat-sdd-analysis`)

## Output

```text
docs/specs/<product>/domains/
  domain-knowledge-{NN}-{name}.md   # One file per domain (e.g., domain-knowledge-03-auth.md)
```

Each file contains:

- **Heading** with domain name and Last Updated timestamp
- **Overview** (Sources, Summary, Context)
- **Investigations** (one section per topic with Findings, Impact Assessment, Recommendations, References)
- **Change Log** (chronological record of updates)
