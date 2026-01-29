# Agent Orchestration

## Available Agents

Located in `~/.claude/agents/`:

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| planner | Implementation planning | Complex features, refactoring |
| architect | System design | Architectural decisions |
| tdd-guide | Test-driven development | New features, bug fixes |
| code-reviewer | Code review | After writing code |
| security-reviewer | Security analysis | Before commits |
| build-error-resolver | Fix build errors | When build fails |
| e2e-runner | E2E testing | Critical user flows |
| refactor-cleaner | Dead code cleanup | Code maintenance |
| requirements-validator | Requirements compliance | Scope validation, cleanup planning |
| doc-updater | Documentation | Updating docs |
| database-reviewer | Database review | Schema design, query optimization |
| python-reviewer | Python code review | Python projects |
| python-linter | Python linting | Python code quality |
| python-tester | Python testing | Pytest with TDD |
| go-reviewer | Go code review | Go projects |
| go-build-resolver | Go build fixes | Go compilation errors |
| keiba-coder | Horse racing development | Keiba domain coding |
| keiba-checker | Horse racing data validation | Data integrity checks |
| leakage-detector | Data leakage detection | ML model validation |
| datarobot-specialist | DataRobot AutoML | DataRobot SDK implementation |

## Immediate Agent Usage

No user prompt needed:
1. Complex feature requests - Use **planner** agent
2. Code just written/modified - Use **code-reviewer** agent
3. Bug fix or new feature - Use **tdd-guide** agent
4. Architectural decision - Use **architect** agent
5. Before refactoring/cleanup - Use **requirements-validator** agent
6. Security-sensitive code - Use **security-reviewer** agent

## Parallel Task Execution

ALWAYS use parallel Task execution for independent operations:

```markdown
# GOOD: Parallel execution
Launch 3 agents in parallel:
1. Agent 1: Security analysis of auth.ts
2. Agent 2: Performance review of cache system
3. Agent 3: Type checking of utils.ts

# BAD: Sequential when unnecessary
First agent 1, then agent 2, then agent 3
```

## Multi-Perspective Analysis

For complex problems, use split role sub-agents:
- Factual reviewer
- Senior engineer
- Security expert
- Consistency reviewer
- Redundancy checker
