---
name: systematic-debug
description: >-
  Use this skill for root cause analysis, debugging complex issues, and systematic troubleshooting.
  Triggers on: debug, root cause, investigate, troubleshoot, why is this failing,
  not working, unexpected behavior, diagnose, track down.
context: fork
---

# Systematic Debugging Methodology

A 4-phase approach to finding and fixing bugs. Based on Superpowers' systematic debugging pattern.

## The 4 Phases

```
1. REPRODUCE -> 2. GATHER CONTEXT -> 3. HYPOTHESIZE -> 4. TEST & VERIFY
```

---

## Phase 1: Reproduce the Issue

**Goal**: Reliably reproduce the bug before attempting to fix it.

### Checklist
- [ ] Can you reproduce the issue consistently?
- [ ] What are the exact steps to trigger it?
- [ ] What is the expected vs actual behavior?
- [ ] Does it happen in all environments (local, staging, prod)?
- [ ] Is it intermittent or consistent?

### Questions to Ask
- When did this start happening?
- What changed recently? (deploys, config, data)
- Does it affect all users or specific ones?
- Are there specific inputs that trigger it?

### Document the Reproduction
```markdown
## Bug: [Brief description]

**Steps to reproduce:**
1.
2.
3.

**Expected:**
**Actual:**
**Environment:**
**Frequency:** Always / Sometimes / Rare
```

---

## Phase 2: Gather Context

**Goal**: Collect all relevant information before forming hypotheses.

### Sources to Check

| Source | What to Look For |
|--------|------------------|
| Logs | Error messages, stack traces, timestamps |
| Metrics | Spikes in errors, latency, CPU, memory |
| Database | Recent data changes, constraint violations |
| Git history | Recent commits in affected area |
| Dependencies | Version updates, deprecations |
| Config | Environment variables, feature flags |

### Commands

```bash
# Search for related errors in logs
grep -r "ErrorName" logs/ | head -50

# Find recent changes to affected files
git log --oneline -20 -- path/to/file.ts

# Check what changed between working and broken
git diff v1.2.3..v1.2.4 -- src/

# Find all occurrences of a function
grep -r "functionName" src/
```

### Create a Timeline
```markdown
## Timeline

- **T-2 days**: Last known working state
- **T-1 day**: Deploy #1234 (feature X)
- **T-4 hours**: First error reported
- **T-0**: Bug confirmed and reproduced
```

---

## Phase 3: Form Hypotheses

**Goal**: Generate multiple possible causes before jumping to solutions.

### Hypothesis Template
```markdown
## Hypothesis 1: [Brief description]

**Why it might be true:**
-
-

**How to test:**
-

**Likelihood:** High / Medium / Low
```

### Common Categories

1. **Code Logic Errors**
   - Off-by-one errors
   - Null/undefined handling
   - Type coercion issues
   - Race conditions

2. **Data Issues**
   - Invalid data in database
   - Schema mismatches
   - Missing migrations

3. **Configuration**
   - Wrong environment variable
   - Missing secrets
   - Feature flag state

4. **Integration Issues**
   - API contract changes
   - Timeout misconfiguration
   - Network issues

5. **Resource Issues**
   - Memory leaks
   - Connection pool exhaustion
   - Disk space

### Rank Hypotheses
Order by:
1. Likelihood (based on evidence)
2. Ease of testing
3. Impact if true

---

## Phase 4: Test and Verify

**Goal**: Systematically test hypotheses until root cause is found.

### Testing Approach

```markdown
## Testing Hypothesis 1

**Test:** [What you'll do]
**Expected if true:** [What you'd see]
**Actual result:** [What happened]
**Conclusion:** Confirmed / Ruled out / Need more data
```

### Verification Checklist

Before declaring fixed:
- [ ] Original reproduction steps no longer trigger the bug
- [ ] Unit tests pass
- [ ] No new regressions introduced
- [ ] Edge cases considered
- [ ] Fix deployed and verified in staging
- [ ] Monitoring shows issue resolved

### Post-Mortem Questions

After fixing:
- Why wasn't this caught earlier?
- Can we add tests to prevent regression?
- Are there similar issues elsewhere?
- Should we add monitoring/alerting?

---

## Quick Reference

### When Stuck

1. **Rubber duck**: Explain the problem out loud
2. **Reduce scope**: Simplify to minimal reproduction
3. **Binary search**: Bisect commits or code
4. **Fresh eyes**: Take a break or ask for help
5. **Question assumptions**: What are you taking for granted?

### Git Bisect for Finding Breaking Commit

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.2.0  # Known working version

# Test each commit, mark as good or bad
git bisect good  # or: git bisect bad

# When found:
git bisect reset
```

### Debug Logging Pattern

```typescript
// Temporary debug logging
console.log('[DEBUG] function entry', { arg1, arg2 });
console.log('[DEBUG] before operation', { state });
// ... operation ...
console.log('[DEBUG] after operation', { result });
```

```python
# Temporary debug logging
import logging
logging.debug(f"[DEBUG] function entry: {arg1=}, {arg2=}")
```

---

## Example Walkthrough

```markdown
## Bug: Users getting 500 error on profile save

### Phase 1: Reproduce
- Steps: Login -> Settings -> Change name -> Save
- Happens 100% for user ID "abc-123"
- Works for other users

### Phase 2: Context
- Error log: "TypeError: Cannot read property 'id' of null"
- Started after deploy #456
- Only affects users created before migration

### Phase 3: Hypotheses
1. **Migration didn't backfill old users** (High likelihood)
2. API response format changed (Medium)
3. Frontend sending wrong payload (Low)

### Phase 4: Test
- Query: SELECT * FROM users WHERE profile IS NULL
- Found 47 users with null profile
- Root cause: Migration added profile column but didn't backfill

### Fix
- Backfill migration for existing users
- Add null check in API as safety
- Add test for this scenario
```
