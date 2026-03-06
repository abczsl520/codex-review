# 🔍 Codex Review — Three-Tier Code Quality Defense

> An OpenClaw Agent Skill that orchestrates multi-model code review with escalating depth levels.

[![ClawHub](https://img.shields.io/badge/ClawHub-codex--review-blue)](https://clawhub.com/skills/codex-review)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## The Problem

AI agents write code fast — but they also **patch bugs in the wrong direction** fast. Single-pass reviews miss cross-file logic issues, business exploits, and race conditions.

## The Solution

**Three levels of defense**, each deeper than the last:

| Level | Trigger | What Happens | Time |
|-------|---------|-------------|------|
| **L1** Quick Scan | "review this" / "review下" | External model scan + agent deep pass | 5-10 min |
| **L2** Deep Audit | "audit this" / "审计下" | Full bug-audit flow or built-in fallback | 30-60 min |
| **L1→L2** Pre-Deploy | "pre-deploy check" / "上线前检查" | L1 → hotspot handoff → L2 → gap analysis | 40-70 min |
| **L3** Cross-Validate | "cross-validate" / "交叉验证" | Independent dual audit + compare + adversarial bypass testing | 60-90 min |

## Key Features

- 🎯 **Smart Escalation** — Say "review" for quick, "audit" for deep, "cross-validate" for maximum
- 🔀 **Dual-Model Cross-Check** — Two AI reviewers independently find bugs, then compare notes
- ⚔️ **Adversarial Testing** (L3) — One model tries to bypass the other's proposed fixes
- 📋 **Hotspot Handoff** — L1 findings automatically feed into L2 for targeted deep analysis
- 🔌 **Composable** — Works standalone (L1) or with `bug-audit` skill (L2/L3)
- 🌍 **Bilingual** — Chinese & English trigger words, auto-detect output language
- 🔧 **Configurable** — Bring your own API (any OpenAI-compatible endpoint)
- 🛡️ **Graceful Fallback** — No bug-audit? Built-in deep audit. API down? Agent-only mode.

## Install

```bash
clawhub install codex-review
```

### Configuration

Set environment variables for the external model used in L1/L3:

```bash
export CODEX_REVIEW_API_BASE="https://api.openai.com/v1"  # Any OpenAI-compatible API
export CODEX_REVIEW_API_KEY="your-api-key"
export CODEX_REVIEW_MODEL="gpt-4o"  # Or any model you prefer
```

Works with: OpenAI, Azure OpenAI, LiteLLM, Ollama, vLLM, Together AI, etc.

**Recommended companion:**
```bash
clawhub install bug-audit  # Unlocks full L2/L3 capability
```

## How It Works

```
L1: Quick Scan
  External Model ──┐
                    ├─→ Merge & Dedup → Report
  Agent Deep Pass ──┘

L2: Deep Audit
  bug-audit (6 phases) → Full Report
  OR built-in fallback (if bug-audit not installed)

L1→L2: Pre-Deploy
  L1 → Hotspots → L2 → Gap Analysis → Merged Report

L3: Cross-Validation
  External Model ──→ Report A ──┐
                                ├─→ Compare → Adversarial Test → Final Report
  Agent (bug-audit) ──→ Report B ──┘
```

## Universal Review Checklist

**Always checked (any language/framework):**
- Cross-file logic consistency
- Authentication & authorization bypass
- Race conditions & concurrent writes
- Input validation (SQL injection, XSS, path traversal)
- Memory/resource leaks
- Sensitive data exposure
- Timezone handling

**Auto-detected tech-stack extras:**
- Node.js: middleware ordering, pm2 compatibility, SQLite pitfalls
- Python: ORM N+1, CSRF, debug mode in production
- Frontend: innerHTML XSS, WebView compat, sub-path routing

## User Options

Customize on the fly:
- `"only scan backend"` — skip frontend files
- `"ignore LOW"` — filter out low-severity issues
- `"output in English"` — control report language
- `"scan this PR"` — audit PR diff instead of full codebase
- `"skip external model"` — agent-only audit

## Real-World Results

Built from auditing **24+ projects** with **200+ real bugs found**:
- 🔴 Admin auth bypasses via localhost detection
- 🔴 Score submission without server validation
- 🔴 Race conditions in concurrent API requests
- 🟡 Timezone UTC vs local mismatches
- 🟡 WebView compatibility issues

## Part of the AI Dev Quality Suite

| Skill | Purpose |
|-------|---------|
| **codex-review** | Multi-tier code review orchestration |
| [bug-audit](https://github.com/abczsl520/bug-audit-skill) | Dynamic bug hunting (200+ pitfall patterns) |
| [debug-methodology](https://github.com/abczsl520/debug-methodology) | Root-cause debugging (no more patch-chaining) |
| [game-quality-gates](https://github.com/abczsl520/game-quality-gates) | Game-specific quality checks |
| [nodejs-project-arch](https://github.com/abczsl520/nodejs-project-arch) | AI-friendly architecture (70-93% token savings) |

---

⭐ **Star this repo** if it saved you from a production bug!

## License

MIT
