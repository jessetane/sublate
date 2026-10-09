# Crucible

An autonomous 3-agent dialectic development workflow for AI coding assistants.

Crucible replaces linear AI execution with a rigorous, adversarial feedback loop governed by a senior technical lead persona.

```text
                              ┌────────────────────────────────────────────────────────┐
                              │                                                        │
                              ▼                                                        │
[Setup: Scope] ──> [Beat 1: Worker] ──(git diff)──> [Beat 2: Reviewer] ──> [Beat 3: Decider]
                                                                               │
                                                                     (Clean? 0 Issues)
                                                                               │
                                                                               ▼
                                                                    [### STATUS: COMPLETE]
```

---

## The 3-Beat State Machine

1. **Beat 1: Implementation (Worker)**  
   Applies the scoped action plan in a focused context, verifies changes locally, and makes logical git commits.
2. **Beat 2: Adversarial Audit (Reviewer)**  
   Audits the cumulative git diff (`git diff <base_commit>..HEAD`) against the specification and core principles in an isolated context. Surfaces bugs, broken symmetry, edge cases, and duplication with exact line citations.
3. **Beat 3: Gatekeeper Triage & Decision (Decider)**  
   Triages findings against the **7 Core Engineering Principles**. Accepts root-cause fixes, rejects defensive bloat, and either loops back to Beat 1 with a refined plan or signs off when the review is clean.

> [!IMPORTANT]
> **The Terminal Invariant**: The Decider is **always** the terminal actor. A Crucible run can **never** terminate directly after a Worker turn. Every code change must undergo adversarial audit and decider review before completion.

---

## The 7 Core Engineering Principles

All three roles operate under a shared engineering rubric:

1. **The Producer Check**: Fix the root cause in the producer rather than accepting defensive band-aids (`?.`, `?? ''`, empty fallbacks) in the consumer.
2. **The Fail-Loud Check**: Reject silent fallbacks that swallow exceptions or mask broken invariants.
3. **The Single-Source-of-Truth Check**: Eliminate duplicate code paths, redundant fallback layers, and legacy wrappers.
4. **The Real-World Blast Radius Check**: Discard theoretical 0%-probability edge cases; focus strictly on real failure modes.
5. **The Upstream Origin Check**: Do not paper over upstream bugs in consumer repos; isolate and fix at the source.
6. **The Symmetry & Terseness Check**: Maintain terse naming, paired lifecycle verbs (`open`/`close`), and cross-layer noun consistency.
7. **The DRY & Abstraction Signal Check**: Treat repetition as a guiding light signaling an opportunity to refactor and consolidate upfront before adding new features.

---

## Installation

Crucible follows the open **[Agent Skills](https://agentskills.io)** format (`SKILL.md`), making it natively portable across AI coding platforms.

### Antigravity / Gemini CLI (`agy`)
Symlink this repository into your global Antigravity skills directory:

```bash
mkdir -p ~/.gemini/config/skills
ln -s "$(pwd)" ~/.gemini/config/skills/crucible
```

Trigger it in any conversation:
```text
Run crucible on this task: <your feature or refactoring description>
```

### Claude Code
Symlink this repository into your global Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills
ln -s "$(pwd)" ~/.claude/skills/crucible
```

Trigger it via slash command:
```text
/crucible <your task>
```

### Standalone / Any Agent
Point any AI agent directly to [SKILL.md](./SKILL.md):
```text
Read SKILL.md and execute the 3-beat dialectic loop for this task.
```
