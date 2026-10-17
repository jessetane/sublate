# Domain Lens: Software Engineering & Architecture

This lens plugs into **Sublate** ([`SKILL.md`](../SKILL.md)) whenever the target deliverable is software, code refactoring, or systems architecture. It partitions the **7 Core Engineering Principles** and the **Git Baseline Protocol** into role-specific injection blocks so each member of the Triad receives only its orthogonal mandate.

---

## 1. The 7 Core Engineering Principles (Reference)

1. **The Producer Check (Root Cause over Defensive Bloat)**: *How would invalid state or an empty value get stored in the first place? What code path produces it?* Never accept consumer band-aids (`?.`, `?? ''`, empty fallbacks) that patch over a symptom. Fix the producer.  
2. **The Fail-Loud Check (Anti-Masking)**: *Does adding this defense hide an error?* Reject silent fallbacks that swallow exceptions or broken invariants. Code must fail loudly and visibly at the boundary.  
3. **The Single-Source-of-Truth Check (No Fallback Proliferation)**: *Why do we have two ways to do this?* Eliminate duplicate code paths, redundant fallback layers, and backwards-compatibility wrappers. Enforce one canonical path for every operation.  
4. **The Real-World Blast Radius Check (Pragmatic Realism)**: *What are the chances this could actually occur in real life? What breaks if we ignore this completely?* Discard theoretical 0%-probability edge cases that add unnecessary lines and complexity.  
5. **The Upstream Origin Check (Boundary Hygiene)**: *Is this actually a bug in an upstream or shared library?* Do not paper over upstream bugs in consumer repos. Isolate the issue and direct the patch to the originating package/repo.  
6. **The Symmetry & Terseness Check (API Elegance)**: *Is this naming terse, symmetrical, and consistent across layers?* Eliminate stuttering (`interactions.create()` over `interactions.createInteraction()`). Enforce paired lifecycle verbs (`open`/`close`, `load`/`save`, `watch`/`unwatch`, `connect`/`disconnect`). Use the same canonical noun across DB, API, and client, and maintain parity across parallel providers.  
7. **The DRY & Abstraction Signal Check (Pragmatic Deduplication)**: *Does this change duplicate logic or introduce competing patterns?* Treat repetition as a signal to consolidate upfront before layering on new features, balanced pragmatically against real-world blast radius.  

---

## 2. Role-Partitioned Injection Blocks

When dispatching subagents, Synthesis injects **only** the matching role block below into the prompt blueprint.

### A. Thesis Lens Injection (`THESIS_LENS`)

```text
DOMAIN LENS: SOFTWARE ENGINEERING (THESIS — THE ARCHITECT-ARTISAN)
- SINGLE SOURCE OF TRUTH (#3): Implement one canonical path per operation. Never introduce multi-tiered fallbacks, duplicate code paths, or backwards-compatibility shims unless explicitly requested.
- FAIL LOUD (#2) & NO CONSUMER BAND-AIDS (#1): Never write silent fallbacks (`?.`, `?? ''`, swallowed catches) to mask broken upstream state. Validate at the boundary; trust internal invariants.
- SYMMETRY & TERSENESS (#6): Eliminate stuttering in names (`users.create()` not `users.createUser()`). Pair lifecycle verbs (`open`/`close`, `load`/`save`, `watch`/`unwatch`, `connect`/`disconnect`). Keep canonical nouns identical across DB, API, and UI.
- UPFRONT DRY (#7): Consolidate shared abstractions upfront rather than copy-pasting parallel patterns.
- GIT & VERIFICATION PROTOCOL:
  1. Run relevant tests/checks locally to verify your changes.
  2. Commit logically contiguous milestones using lowercased, topic-prefixed messages (e.g., `core: unify watcher lifecycle`).
  3. End your report with `COMMIT_RANGE: <start>..<head>`, `FILES_TOUCHED`, `VERIFICATION_RUN`, and `KEY_DECISIONS`.
```

### B. Antithesis Lens Injection (`ANTITHESIS_LENS`)

```text
DOMAIN LENS: SOFTWARE ENGINEERING (ANTITHESIS — THE ICONOCLAST)
- EXECUTION & IMMUTABLE WORKSPACE: You have shell execution to run `git diff`, `git log`, test suites, and non-destructive one-liner reproducers. You MUST NOT modify tracked files, commit, or alter git history (unless explicitly running in an isolated branch workspace).
- PRODUCER PROOF (#1 — MANDATORY EPISTEMIC CONSTRAINT): You are FORBIDDEN from demanding a null check, `?.`, or defensive guard on a consumer unless you trace and cite the exact PRODUCER code path (`file:line`) capable of emitting that invalid state. Unproved "what if X is undefined" nitpicks will be negated.
- ANTI-MASKING (#2) & COMPETING PATHS (#3): Flag any silent error swallowing, fallback proliferation, or duplicate implementations introduced by Thesis.
- ASYMMETRY & STUTTERING (#6): Flag asymmetrical lifecycle verbs, stuttering method names, or terminology drift across layers.
- EMPIRICAL FALSIFICATION: Prefer running a targeted test or reproducer command over armchair speculation whenever verifying a race condition, boundary failure, or broken invariant.
```

### C. Synthesis Governance Rules (`SYNTHESIS_LENS`)

Synthesis applies these rules during **Setup** and **Beat 3**:

- **Upfront Consolidation Scan (#7 at Setup)**: Before dispatching Thesis Iteration 1, scan target files for competing patterns or duplication. If an upfront refactor simplifies the feature, make it Step 1 of Thesis's initial Action Plan.  
- **Root-Cause over Band-Aid (#1 at Beat 3)**: When Antithesis proves a state bug, never instruct Thesis to add a consumer band-aid (`?.`). Issue a `[SUBLATE]` directive targeting the **producer** or boundary parser.  
- **Real-World Blast Radius Filter (#4 at Beat 3)**: `[NEGATE]` theoretical 0%-probability edge cases, unproved producer paths, and pedantic nitpicks that would scar the code with defensive branches.  
- **Upstream Hygiene (#5 at Beat 3)**: `[EXTERNALIZE]` defects originating in external packages or upstream libraries rather than papering over them locally.  

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
