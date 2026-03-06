---
name: codex-review
version: 1.0.0
description: "Three-tier code quality defense: L1 quick scan, L2 deep audit (triggers bug-audit), L3 cross-validation with adversarial testing. Orchestration layer that coordinates multiple AI reviewers without modifying underlying tools."
metadata:
  { "openclaw": { "emoji": "🔍" } }
tags:
  - code-review
  - quality-assurance
  - bug-detection
  - security-audit
  - cross-validation
  - ai-code-review
  - nodejs
  - openclaw-skill
  - clawhub
  - devops
---

# Codex Review — Three-Tier Code Quality Defense 🔍

Unified orchestration layer: selects audit depth based on trigger words. Works standalone or coordinates with `bug-audit` skill for deep analysis.

## When to Activate

| User says | Level | What happens | Est. time |
|-----------|-------|--------------|-----------|
| "review" / "quick check" / "review下" / "检查下" | L1 | Dual-model quick scan | 5-10 min |
| "audit" / "deep check" / "审计下" / "排查下" | L2 | Full bug-audit flow | 30-60 min |
| "pre-deploy check" / "上线前检查" | L1→L2 | L1 scan → hotspots → bug-audit → gap fill | 40-70 min |
| "cross-validate" / "max check" / "交叉验证" | L3 | Independent dual audit + compare + adversarial | 60-90 min |

---

## Level 1: Quick Scan (Core)

### Flow
1. Gather code (local read or scp from server)
2. Exclude: `node_modules/`, `.git/`, `package-lock.json`, `dist/`, `*.db`
3. Send to a fast code-review model (e.g. Codex/GPT) — Round 1
4. Self-review (Opus/Sonnet) — Round 2 deep supplement
5. Merge, deduplicate, output graded report
6. Write hotspot file for L1→L2 handoff

### Round 1: Fast Model Scan

Use any available code-review API. System prompt:

```
You are an expert code reviewer. Find ALL bugs and security issues:
1. CRITICAL — Security vulnerabilities (XSS, injection, auth bypass), crash bugs
2. HIGH — Logic errors, race conditions, unhandled exceptions
3. MEDIUM — Missing validation, edge cases, performance issues
4. LOW — Code style, dead code, minor improvements

For each: Severity, File+line, Issue, Fix with code snippet.
Focus on real bugs, not style nitpicks.
```

### Round 2: Self-Review Checklist
- [ ] Cross-file logic consistency
- [ ] Business logic exploits (anti-cheat bypass, negative balance, privilege escalation)
- [ ] Race conditions (concurrent requests, DB write conflicts)
- [ ] Timezone issues (UTC vs local)
- [ ] SQLite pitfalls (DEFAULT can't use functions, double-quotes = column names)
- [ ] Nginx sub-path routing issues
- [ ] WeChat WebView compatibility
- [ ] API auth bypass vectors
- [ ] Frontend XSS (unescaped innerHTML)
- [ ] Node.js memory leaks (event listeners, unclosed streams)

### Code Volume Control
- Single request ≤ backend core files (server + routes + db + config)
- Frontend in separate batch if needed
- Use standard model, not high-compute variants (timeout risk)

### Hotspot File (L1→L2 Handoff)
After L1, write discovered issues to `/tmp/codex-review-hotspots.json`:
```json
{
  "project": "example-project",
  "timestamp": "2026-03-05T22:00:00",
  "hotspots": [
    {"file": "routes/admin.js", "severity": "CRITICAL", "brief": "Admin auth bypass via localhost"},
    {"file": "routes/game.js", "severity": "CRITICAL", "brief": "Score submission no server validation"}
  ]
}
```

---

## Level 2: Deep Audit (Delegates to bug-audit)

### Flow
1. Load and execute `bug-audit` SKILL.md in full (all 6 Phases)
2. Zero modifications to bug-audit's own process
3. Act as faithful executor of bug-audit's specification

### Trigger
When user requests deep audit, read `bug-audit/SKILL.md` and follow strictly.

---

## Level 1→2 Cascade: Pre-Deploy Check

### Flow
1. Execute L1 quick scan
2. Write hotspot file
3. Execute L2 (full bug-audit)
4. **Post-audit gap analysis**:
   - Read hotspot file
   - Check which L1 hotspots bug-audit already covered → mark "covered"
   - Uncovered hotspots → targeted deep analysis, append to report
   - Contradictions between L1 and L2 → flag for human review
5. Output merged final report

---

## Level 3: Cross-Validation (Maximum)

### Flow
```
Step 1: Model A — Independent audit
  → Full code to fast review model, detailed prompt
  → Output: Report A

Step 2: Model B — Independent audit (bug-audit full flow)
  → Execute bug-audit SKILL.md
  → Output: Report B

Step 3: Cross-compare
  → Both found → 🔴 Confirmed high-risk (high confidence)
  → Only A found → 🟡 B verifies (possible false positive)
  → Only B found → 🟡 A verifies (possible deep logic bug)
  → Contradictions → ⚠️ Deep analysis, render judgment

Step 4: Adversarial testing
  → Ask Model A to bypass the proposed fixes
  → Prompt: "Given these fixes, find ways to bypass them"
  → Validate fix robustness
```

### Adversarial Test Prompt
```
You are a security researcher. The following security fixes were applied to a Node.js project.
For each fix, analyze:
1. Can the fix be bypassed? How?
2. Does the fix introduce new vulnerabilities?
3. Are there edge cases the fix doesn't cover?
Be adversarial and thorough.
```

---

## Report Format (All Levels)

```markdown
# 🔍 Code Audit Report — [Project Name]
## Audit Level: L1/L2/L3
## 📊 Summary
- Files scanned: X
- Issues found: X (🔴 Critical X | 🟠 High X | 🟡 Medium X | 🔵 Low X)
- [L3 only] Cross-validation: Both agreed X | Only A X | Only B X | Contradictions X

## 🔴 Critical Issues
### 1. [Issue title]
- **File**: `path/to/file.js:42-55`
- **Found by**: Model A / Model B / Both
- **Description**: ...
- **Fix**:
(code snippet)

## ✅ Highlights
- [Things done well]
```

---

## Notes

1. API timeout: 120s per request. If fast model fails, skip its round — self-review covers
2. Large projects: batch by layer (backend → frontend → config)
3. Long reports: split across multiple messages
4. L2/L3 bug-audit execution follows its SKILL.md exactly — no shortcuts
5. Hotspot file is ephemeral, overwritten each L1 run
6. **Works best with `bug-audit` skill installed** — L2/L3 depend on it
