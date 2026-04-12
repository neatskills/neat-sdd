# Risk Assessment Algorithm

Risk-based gate triggering determines whether to run spec gates based on feature complexity and criticality.

## Design Phase Assessment

Run before Step 5 (design + plan verification).

**High-risk signals:**
- Task count > 15
- Keywords in goal/criteria: `auth`, `payment`, `security`, `migration`, `database`, `breaking`, `API`
- Blast area > 5 files
- Dependencies on other features (has `depends_on`)

**Medium-risk signals:**
- Task count 10-15
- ADRs extracted > 0 (architectural significance)
- Blast area 3-5 files

**Decision:**
- ANY high-risk signal → Run gate
- ANY medium-risk signal (and no high) → Run gate
- All signals low → Skip gate, log decision

**Log format (skip):**
```
Skipped design gate (low-risk feature: [X] tasks, [Y] files, no keywords)
```

**Log destination:** Console output during build execution. Not written to gate log file since gate doesn't run.

## Execute Phase Assessment

Run before Step 7 (code verification).

**High-risk signals:**
- Git diff files > 10
- Git diff lines (insertions + deletions) > 500
- Keywords in diff: `auth`, `payment`, `security`, `migration`, `schema`, `breaking`, `deprecated`
- Modified files in critical paths: `auth/`, `payment/`, `security/`, `migrations/`, `api/`

**Medium-risk signals:**
- Git diff files 5-10
- Git diff lines 200-500
- New database models or API endpoints

**Decision:**
- ANY high-risk signal → Run gate
- ANY medium-risk signal (and no high) → Run gate
- All signals low → Skip gate, log decision

**Log format (skip):**
```
Skipped execute gate (low-risk implementation: [X] files, [Y] lines changed)
```

**Log destination:** Console output during build execution. Not written to gate log file since gate doesn't run.

**Detection commands:**
```bash
git diff --stat main...HEAD                    # Count files and lines
git diff main...HEAD | grep -i "auth\|payment"  # Check for keywords
```

## Conservative Approach

The algorithm is designed to be conservative - ANY risk signal triggers the gate. This ensures:
- Critical features always get verified
- False negatives (missing a risky feature) are minimized
- False positives (running gate unnecessarily) are acceptable
