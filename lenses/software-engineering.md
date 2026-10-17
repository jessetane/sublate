# Domain Lens: Software Engineering & Architecture

This lens plugs into **Sublate** ([`SKILL.md`](../SKILL.md)) whenever the target deliverable is software, code refactoring, or systems architecture. It orders the **7 Core Engineering Principles** from highest conceptual architecture down to specific implementation details, and partitions them into role-specific injection blocks (`THESIS_LENS`, `ANTITHESIS_LENS`, `SYNTHESIS_LENS`).

---

## 1. The 7 Core Engineering Principles (High → Low)

1. **Minimal Mechanism (Design by Subtraction)** *(Problem Framing — Highest Level)*  
   - *Question*: "Do we actually need this structure, or can we reframe the model so the special case disappears?"  
   - *Rule*: Keep the architecture generic and minimal. Do not bolt artificial flags, special cases, or extra moving parts onto core machinery when a simpler primitive or boundary change makes the problem vanish.  
2. **Single Source of Truth (One Canonical Way)** *(System Architecture)*  
   - *Question*: "Why do we have two ways to do this? Why are there fallback layers?"  
   - *Rule*: Enforce one canonical path for every operation and state item. Never introduce multi-tiered fallbacks, dual code paths, or backwards-compatibility wrappers unless explicitly requested.  
3. **Pragmatic DRY & Upfront Consolidation** *(Module Structure & Refactoring)*  
   - *Question*: "Does this duplicate existing logic or introduce a competing pattern? Should we consolidate before layering on new work?"  
   - *Rule*: Treat repetition as a signal to consolidate shared abstractions upfront before layering on new features—balanced pragmatically against scope so you don't over-abstract trivial code.  
4. **Fix the Producer, Not the Consumer (Root Cause & Upstream Hygiene)** *(Boundaries & Data Flow)*  
   - *Question*: "What exact code path or upstream package produces this bad state?"  
   - *Rule*: Never slap defensive band-aids (`?.`, `?? ''`, downstream workarounds) onto consumers to patch a symptom—whether inside the same module or across an external library boundary (`[EXTERNALIZE]`). If a reviewer cannot prove the producer path (`file:line`), reject the guard; if they can, fix the producer at its origin.  
5. **Fail Loud & Pragmatic Realism (No Silent Masking)** *(Error Semantics)*  
   - *Question*: "Does this defense or `catch` block hide a real error—or guard against a 0%-probability fantasy?"  
   - *Rule*: Never swallow exceptions (`catch (err) {}`) or mask broken invariants with silent fallbacks. Let real errors propagate and fail visibly at the boundary, and ignore theoretical 0%-probability edge cases that have no real-world blast radius.  
6. **Symmetry & Terseness** *(API & Naming)*  
   - *Question*: "Is the naming terse, symmetrical, and consistent across layers?"  
   - *Rule*: Eliminate stuttering (`interactions.create()` over `interactions.createInteraction()`). Enforce paired lifecycle verbs (`open`/`close`, `load`/`save`, `watch`/`unwatch`, `connect`/`disconnect`). Use the same canonical noun across DB, API, and UI, and maintain parity across parallel providers.  
7. **Native Platform & Dense Style** *(Runtime, Dependencies & Syntax — Most Detailed)*  
   - *Question*: "Can the platform do this natively without dependencies, and does the code match the project's dense style?"  
   - *Rule*: Reach for vanilla platform features first (ES modules, `fetch`, `EventTarget`, `WebSocket`, `node:test`, Web Components) before adding dependencies or build steps. Write dense, idiomatic code (tabs, no semicolons, `function` declarations, `async`/`await` with top-level `await`, no blank lines inside function bodies).  

---

## 2. Role-Partitioned Injection Blocks

When dispatching subagents, Synthesis injects **only** the matching role block below into the prompt blueprint.

### A. Thesis Lens Injection (`THESIS_LENS`)

```text
DOMAIN LENS: SOFTWARE ENGINEERING (THESIS — BUILDER)
1. MINIMAL MECHANISM (#1): Keep the design generic and minimal. Do not bolt special-case flags onto core machinery when a simpler boundary or primitive eliminates the special case.
2. SINGLE SOURCE OF TRUTH (#2): Implement one canonical path per operation. Never introduce multi-tiered fallbacks, dual code paths, or backwards-compatibility shims unless explicitly requested.
3. UPFRONT DRY (#3): Consolidate shared abstractions upfront rather than copy-pasting parallel patterns.
4. FIX THE PRODUCER (#4) & FAIL LOUD (#5): Never write consumer band-aids (`?.`, `?? ''`) or silent error swallowing (`catch (err) {}`). Validate at the boundary; let broken invariants fail visibly.
5. SYMMETRY & TERSENESS (#6): Eliminate stuttering (`users.create()` not `users.createUser()`). Pair lifecycle verbs (`open`/`close`, `load`/`save`, `watch`/`unwatch`, `connect`/`disconnect`). Keep canonical nouns identical across DB, API, and UI.
6. NATIVE PLATFORM & DENSE STYLE (#7): Use vanilla platform features (ES modules, `fetch`, `EventTarget`, `WebSocket`, `node:test`, Web Components) over external dependencies. Use tabs, no semicolons, `function` keyword for standalone functions, `async`/`await` (with top-level `await`), and no blank lines inside function bodies.
7. GIT & VERIFICATION PROTOCOL:
   - Run relevant tests/checks locally to verify your changes.
   - Commit logically contiguous milestones using lowercased, topic-prefixed messages (e.g., `core: unify watcher lifecycle`).
   - End your report with `COMMIT_RANGE: <start>..<head>`, `FILES_TOUCHED`, `VERIFICATION_RUN`, and `KEY_DECISIONS`.
```

### B. Antithesis Lens Injection (`ANTITHESIS_LENS`)

```text
DOMAIN LENS: SOFTWARE ENGINEERING (ANTITHESIS — RED-TEAM)
1. EXECUTION & IMMUTABLE WORKSPACE: You have shell execution to run `git diff`, `git log`, test suites, and non-destructive one-liner reproducers. You MUST NOT modify tracked files, commit, or alter git history (unless running in an isolated branch workspace).
2. CHALLENGE ARTIFICIAL COMPLEXITY (#1): Call out special-case flags, unnecessary state, or clunky "Rube Goldberg" glue via Structural Provocations (`[P#]`) that show how a simpler mechanism makes the complexity vanish.
3. COMPETING PATHS (#2, #3) & ANTI-MASKING (#5): Flag any fallback proliferation, duplicate code paths, or swallowed errors (`catch (err) {}`) introduced by Thesis.
4. PRODUCER PROOF (#4 — MANDATORY EPISTEMIC CONSTRAINT): You are FORBIDDEN from demanding a null check, `?.`, or defensive guard on a consumer unless you trace and cite the exact PRODUCER code path (`file:line`) or upstream package capable of emitting that invalid state. Unproved "what if X is undefined" nitpicks will be negated.
5. ASYMMETRY & STYLE DRIFT (#6, #7): Flag asymmetrical lifecycle verbs, stuttering method names, terminology drift across layers, or unnecessary third-party dependencies where native platform features suffice.
```

### C. Synthesis Governance Rules (`SYNTHESIS_LENS`)

Synthesis applies these rules during **Setup** and **Beat 3**:

- **Minimal Mechanism & Upfront Consolidation Scan (#1, #3 at Setup)**: Before dispatching Thesis Iteration 1, scan target files for competing patterns or artificial complexity. If an upfront consolidation simplifies the upcoming feature, make it Step 1 of Thesis's initial Action Plan.  
- **Fix the Producer / Externalize Upstream (#4 at Beat 3)**: When Antithesis proves a state bug, never instruct Thesis to add a consumer band-aid (`?.`). Issue a `[SUBLATE]` directive targeting the **producer** inside the repo, or `[EXTERNALIZE]` if the bug originates in an upstream library/package.  
- **Real-World Blast Radius Filter (#5 at Beat 3)**: `[NEGATE]` theoretical 0%-probability edge cases, unproved producer paths, and pedantic nitpicks that would scar the code with defensive bloat.  

---

## 3. Git Baseline & History Protocol

1. **Boot State & Immutable `<base_anchor>`**:
   - **Clean Boot**: When the working tree is clean, record `<base_anchor>` as `git rev-parse HEAD`.  
   - **Dirty Boot (WIP Subject)**: If the working tree has uncommitted changes, inspect `git status` and `git diff`. If the dirty changes are the subject of the user's request (or active WIP to build upon), do **not** stash them away—commit them as a temporary baseline (`wip: pre-sublate baseline`) or record `HEAD` as `<base_anchor>` so Thesis builds directly on the WIP. Only `git stash push -u -m "sublate-autostash"` if the dirty changes are unrelated noise.  
2. **Delta + Anchor Diffing**:
   - **Initial or Fresh Renewal (`invoke_subagent`)**: Antithesis audits the full cumulative range (`git diff <base_anchor>..HEAD`).  
   - **Continuity (`send_message`)**: When reusing a sharp Antithesis instance to verify targeted fixes, it audits only the iteration delta (`git diff <last_reviewed_commit>..HEAD`), consulting `<base_anchor>..HEAD` only if needed to verify net boundary alignment.  
3. **Non-Interactive History Curation**:
   - On convergence, if multiple iterations generated intermediate fixup commits (or a temporary `wip: pre-sublate baseline` commit), Synthesis curates `<base_anchor>..HEAD` into clean, logical, topic-prefixed commits.  
   - **Never run `git rebase -i`** (which hangs in non-interactive shells). Instead, use `git reset --soft <base_anchor>` and create clean, atomic commits (or leave Thesis's commits intact if they are already logically structured).  
