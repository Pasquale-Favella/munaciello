# 🤌 Munaciello

> *Evidence-first coding agent for GitHub Copilot. Verifies before presenting. Never shows broken code.*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Copilot Plugin](https://img.shields.io/badge/Copilot-Plugin-blue?logo=github)](https://github.com/Pasquale-Favella/munaciello)
[![Version](https://img.shields.io/badge/version-1.0.0-green)](https://github.com/Pasquale-Favella/munaciello)

---

## What is Munaciello?

Munaciello is a senior-engineer-grade coding agent that **proves its work**. Rather than just generating code and hoping for the best, it runs IDE diagnostics, build checks, linters, and adversarial multi-model review — and records every result in a SQL verification ledger — before showing you anything.

It has opinions. It pushes back. It never guesses at requirements.

---

## Install

```bash
copilot plugin install Pasquale-Favella/munaciello
```

---

## Core Principles

| Principle | What it means |
|-----------|---------------|
| **Evidence-first** | Every claim is backed by a tool-call result, not a self-reported assertion |
| **Adversarial review** | Attacks its own output before presenting it |
| **SQL-tracked verification** | Every check is an INSERT into `munaciello_checks`, not prose |
| **Automatic rollback** | If it can't fix something after 2 attempts, it reverts and tells you |
| **Senior opinions** | Pushes back on bad requirements and implementation decisions |

---

## How It Works

Munaciello classifies every task by size, then runs an appropriate verification pipeline.

### Task Sizes

| Size | When | Key differences |
|------|------|-----------------|
| **Small** | Typo, rename, config tweak, one-liner | Diagnostics + verification, no ledger |
| **Medium** | Bug fix, feature addition, refactor | Full ledger, 1 adversarial reviewer, auto-commit |
| **Large** | New feature, multi-file architecture, auth/crypto | 3 parallel reviewers, shown plan + confirmation, full ledger |

> **Exception:** Any change touching auth, crypto, payments, or data deletion escalates to **Large**, regardless of scope.

### Risk Classification

| Level | File types |
|-------|-----------|
| 🟢 **Low** | Additive changes, new tests, docs, config, comments |
| 🟡 **Medium** | Modifying existing logic, function signatures, DB queries, UI state |
| 🔴 **High** | Auth, crypto, payments, data deletion, schema migrations, public API surface |

---

## The Workflow

```
0.  Boost          — rewrite the prompt into a precise specification
0b. Git Hygiene    — check dirty state, branch, worktree (Medium/Large)
1.  Understand     — parse goal, criteria, open questions
1b. Recall         — query session history for past failures on target files
2.  Survey         — search codebase for reusable code and blast radius
3.  Plan           — risk-classify files; confirm with user on Large tasks
3b. Baseline       — capture build/test state before touching anything
4.  Implement      — surgical changes, follow existing patterns
5a. IDE Diagnostics — errors on changed files and their importers
5b. Verification   — build, type check, lint, tests, smoke execution
5c. Adversarial    — 1 reviewer (Medium) or 3 parallel reviewers (Large)
5d. Readiness      — observability, degradation, secrets (Large only)
5e. Evidence Bundle — full ledger query, confidence rating, rollback cmd
6.  Learn          — store confirmed facts immediately (not in a batch)
7.  Present        — change summary + Evidence Bundle
8.  Commit         — auto-commit (Medium/Large) or ask first (Small)
```

---

## Evidence Bundle

After every Medium and Large task, Munaciello presents a structured report:

```
## 🔨 Munaciello Evidence Bundle

Task: fix-login-crash | Size: M | Risk: 🟡

### Baseline (before changes)
| Check    | Result | Command        | Detail |
|----------|--------|----------------|--------|
| lint     | ✅     | pnpm lint      | 0 warnings |
| tests    | ✅     | pnpm test      | 42 passed  |

### Verification (after changes)
| Check    | Result | Command        | Detail |
|----------|--------|----------------|--------|
| lint     | ✅     | pnpm lint      | 0 warnings |
| tests    | ✅     | pnpm test      | 43 passed  |

### Adversarial Review
| Reviewer | Verdict | Findings |
|----------|---------|----------|
| gpt-4o   | ✅ Clean | No issues found |

Changes: src/auth/login.ts — fixed null deref on expired token
Blast radius: 2 files import login.ts, both checked
Confidence: High
Rollback: git checkout HEAD -- src/auth/login.ts
```

---

## Pushback

Munaciello won't silently implement a bad idea. If it spots a problem at the implementation or requirements level, it surfaces a clear callout and asks how to proceed — **before writing a single line of code**.

```
⚠️ Munaciello pushback: You asked for a "delete all" button with no confirmation.
The Firestore delete is permanent — users who fat-finger this lose everything.
Real problem: users want to clean up history, not destroy it permanently.

→ Proceed as requested / Help me understand the real goal / Let me rethink
```

---

## Verification Cascade

Munaciello runs verification in tiers — never stops at the first passing check:

1. **IDE diagnostics** — always
2. **Syntax/parse check** — always
3. **Build/compile** — if tooling exists
4. **Type checker** — if tooling exists
5. **Linter** — if tooling exists
6. **Test suite** — if tests exist
7. **Import/load test** — if tiers 1–2 produce no runtime signal
8. **Smoke execution** — throwaway script exercising the changed path

---

## License

[MIT](LICENSE) © [Pasquale Favella](https://pasquale-favella.github.io)
