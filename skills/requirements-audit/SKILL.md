---
name: requirements-audit
description: Audit codebase compliance with requirements.md. Identifies out-of-scope features, missing implementations, and generates cleanup recommendations.
---

# Requirements Audit Skill

Comprehensive requirements compliance validation. Ensures codebase matches documented requirements and identifies code that should be removed.

## When to Use

Invoke `/requirements-audit` when:
- After major feature development
- Before production releases
- During refactoring sessions
- When codebase feels bloated
- To verify scope compliance
- Before architectural decisions

## What This Skill Does

1. **Reads Requirements** - Analyzes .tmp/requirements.md or docs/requirements.md
2. **Scans Codebase** - Inventories all features, modules, and files
3. **Maps Implementation** - Links code to requirements
4. **Detects Gaps** - Finds missing required features
5. **Identifies Excess** - Finds features not in requirements
6. **Generates Report** - Creates actionable audit report
7. **Cleanup Plan** - Provides deletion checklist

## Workflow

```markdown
┌─────────────────────────────────────────────┐
│ 1. Read requirements.md                     │
│    - Extract all requirements               │
│    - Identify feature scope                 │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 2. Inventory codebase                       │
│    - Scan all source files                  │
│    - List all features/modules              │
│    - Map directory structure                │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 3. Compliance analysis                      │
│    - Match code to requirements             │
│    - Identify gaps (missing)                │
│    - Identify overages (out-of-scope)       │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 4. Generate audit report                    │
│    - .tmp/requirements-audit.md             │
│    - .tmp/deletion-checklist.md             │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 5. Present findings                         │
│    - Summary to user                        │
│    - Deletion candidates                    │
│    - Missing features                       │
└─────────────────────────────────────────────┘
```

## Usage

```bash
# Run full requirements audit
/requirements-audit

# Typical output locations:
# - .tmp/requirements-audit.md       (detailed report)
# - .tmp/deletion-checklist.md       (actionable cleanup plan)
```

## Output Report Structure

### requirements-audit.md

```markdown
# Requirements Compliance Audit

## Executive Summary
- Requirements coverage: 85%
- Out-of-scope features: 3
- Missing implementations: 2
- Cleanup potential: ~1,200 LOC

## Implemented Requirements
[List of requirements with implementation status]

## Missing Requirements
[Gap analysis with priority]

## Out-of-Scope Features
[Features not in requirements - DELETION CANDIDATES]

## Cleanup Recommendations
[Phased deletion plan]

## Action Items
[Next steps for team]
```

### deletion-checklist.md

```markdown
# Deletion Checklist

## Phase 1: Safe Deletions (Execute immediately)
- [ ] Delete src/features/admin-dashboard/
- [ ] Remove src/utils/crypto.ts
- [ ] Remove unused dependencies: graphql, d3

## Phase 2: Review Required (Verify with team)
- [ ] Verify src/hooks/useWebSocket.ts usage
- [ ] Check if cache-manager.ts needed

## Phase 3: Test & Verify
- [ ] Run full test suite
- [ ] Check build succeeds
- [ ] Manual testing
```

## Integration with Other Tools

### With refactor-cleaner Agent

```markdown
Sequential workflow:

1. /requirements-audit
   → Identifies out-of-scope features
   → Marks for deletion

2. Launch refactor-cleaner agent
   → Finds unused code within valid scope
   → Combines with audit findings
   → Executes safe deletions
```

### With code-reviewer Agent

```markdown
During code review:

If code-reviewer finds:
"This feature has no tests and poor quality"

Check requirements-audit:
→ If feature not in requirements: DELETE
→ If feature in requirements: FIX
```

## Safety Checks

Before recommending any deletion:

```markdown
✓ Feature not in requirements.md
✓ No git activity in last 30 days
✓ Not used by other features (grep verification)
✓ Not part of public API
✓ Removal won't break tests
✓ Not infrastructure code
```

## Example Session

```markdown
User: /requirements-audit

Agent:
1. Reading requirements from .tmp/requirements.md...
   Found 15 functional requirements, 5 non-functional

2. Scanning codebase...
   - 45 source files
   - 8 feature modules
   - 12 API endpoints

3. Analyzing compliance...
   ✓ 12 requirements fully implemented
   ⚠ 2 requirements missing
   ❌ 3 features out of scope

4. Generating reports...
   - .tmp/requirements-audit.md
   - .tmp/deletion-checklist.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 AUDIT SUMMARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Requirements Coverage: 80% (12/15)

⚠️  Missing Implementations:
  - REQ-007: Email notifications
  - REQ-011: CSV export

❌ Out-of-Scope Features (Deletion candidates):
  1. src/features/admin-dashboard/ (450 LOC)
     → Admin features explicitly excluded

  2. src/utils/blockchain-utils.ts (180 LOC)
     → Blockchain not in requirements

  3. src/graphql/ (320 LOC)
     → GraphQL API not required

💡 Cleanup Potential:
  - 950 LOC can be safely removed
  - 3 dependencies can be uninstalled
  - Estimated bundle size reduction: ~85 KB

📋 Next Steps:
  1. Review deletion-checklist.md
  2. Approve Phase 1 deletions
  3. Create tickets for missing requirements
  4. Schedule team review for Phase 2

Full report: .tmp/requirements-audit.md
```

## Common Findings

### Typical Out-of-Scope Features

```markdown
1. Admin panels (if not in requirements)
2. Experimental features (POC code)
3. Alternative implementations (duplicate approaches)
4. Deprecated features (replaced but not removed)
5. Over-engineered utilities (YAGNI violations)
```

### Legitimate Undocumented Code

```markdown
These are OK even if not in requirements:

✓ Build tooling (webpack, vite config)
✓ Development utilities (logging, debugging)
✓ Test infrastructure
✓ Type definitions
✓ Error handling utilities
✓ Performance monitoring
```

## Best Practices

### Before Running Audit

```markdown
1. Ensure requirements.md is up-to-date
2. Commit current work
3. Create audit branch if planning deletions
4. Ensure all tests passing
```

### After Receiving Report

```markdown
1. Review with product owner
2. Verify out-of-scope findings
3. Prioritize missing implementations
4. Get approval for deletions
5. Execute cleanup in phases
6. Update requirements if needed
```

## Customization

### Requirements File Location

By default, looks for:
1. .tmp/requirements.md (highest priority)
2. docs/requirements.md
3. REQUIREMENTS.md
4. README.md (if contains requirements section)

### Exclusion Patterns

Certain directories are always excluded from audit:
- node_modules/
- .git/
- dist/, build/
- coverage/
- .next/, .cache/

### Report Format

Customize report sections in agent prompt:
- Add project-specific categories
- Adjust priority levels
- Include custom metrics
- Add compliance scores

## Troubleshooting

### "No requirements.md found"

```bash
# Create requirements document first
/requirements

# Or specify location
echo "Requirements at custom/path/requirements.md"
```

### "Too many false positives"

```markdown
Agent may flag legitimate infrastructure code.
Review Medium/Low priority items carefully.
Add exceptions in agent configuration if needed.
```

### "Missing obvious out-of-scope features"

```markdown
Requirements may be too vague.
Refine requirements.md with explicit scope.
Add "Out of Scope" section to requirements.
```

## Integration with Development Workflow

### Weekly Audits

```bash
# Run every Friday
/requirements-audit

# Quick check during PR review
/requirements-audit --quick
```

### Pre-Release Checklist

```markdown
Before every release:

1. [ ] /requirements-audit
2. [ ] Review out-of-scope features
3. [ ] Verify all requirements covered
4. [ ] Execute approved deletions
5. [ ] Re-run tests
6. [ ] Update changelog
```

## Metrics to Track

```markdown
Track over time:

- Requirements coverage %
- Out-of-scope LOC
- Cleanup frequency
- Bundle size reduction
- Technical debt paydown

Include in sprint retrospectives.
```

---

**Goal**: Maintain a lean, focused codebase that precisely matches documented requirements. Every feature should have a purpose, every line of code should serve a requirement.
