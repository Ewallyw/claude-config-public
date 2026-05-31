---
name: verification-loop
description: Use when about to claim work is complete, fixed, or passing — requires running verification commands and confirming output before making any success claims; evidence before assertions always.
---

# Verification Loop

## The Iron Law

NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.

If you haven't run the verification command in this message, you cannot claim it passes.

## The Gate Function

BEFORE claiming any status:
1. IDENTIFY: What command proves this claim?
2. RUN: Execute the FULL command (fresh, complete)
3. READ: Full output, check exit code, count failures
4. VERIFY: Does output confirm the claim?
5. ONLY THEN: Make the claim

## Common Failures

| Claim | Requires | Not Sufficient |
|-------|----------|----------------|
| Tests pass | Test command output: 0 failures | Previous run, "should pass" |
| Linter clean | Linter output: 0 errors | Partial check, extrapolation |
| Build succeeds | Build command: exit 0 | Linter passing, logs look good |
| Bug fixed | Test original symptom: passes | Code changed, assumed fixed |
| Agent completed | VCS diff shows changes | Agent reports "success" |

## Verification Phases

### Phase 1: Build
```bash
npm run build 2>&1 | tail -20    # Node/TS
# OR
python -c "import compileall"     # Python
```
If build fails, STOP.

### Phase 2: Type Check
```bash
npx tsc --noEmit 2>&1 | head -30   # TypeScript
pyright . 2>&1 | head -30          # Python
```

### Phase 3: Lint
```bash
npm run lint 2>&1 | head -30       # JS/TS
ruff check . 2>&1 | head -30       # Python
```

### Phase 4: Tests
```bash
npm test -- --coverage 2>&1 | tail -50   # JS/TS
pytest --cov --tb=short 2>&1 | tail -30  # Python
```

### Phase 5: Quick Security Check
```bash
grep -rn "sk-" --include="*.py" --include="*.ts" . | head -10
grep -rn "api_key\|password\|secret" --include="*.py" . | head -10
```

### Phase 6: Diff Review
```bash
git diff --stat
```
Review each changed file for unintended changes, missing error handling.

## Output Format

```
VERIFICATION REPORT
Build:     [PASS/FAIL]
Types:     [PASS/FAIL] (X errors)
Lint:      [PASS/FAIL] (X warnings)
Tests:     [PASS/FAIL] (X/Y passed, Z% coverage)
Security:  [PASS/FAIL] (X issues)
Diff:      [X files changed]

Overall: [READY/NOT READY]
```

## Red Flags

- Using "should", "probably", "seems to"
- Expressing satisfaction before verification
- Trusting agent success reports without checking
- About to commit/push/PR without verification

## Rationalization Prevention

| Excuse | Reality |
|--------|---------|
| "Should work now" | RUN the verification |
| "I'm confident" | Confidence != evidence |
| "Just this once" | No exceptions |
| "Partial check is enough" | Partial proves nothing |
