# Domain Lens: Software Engineering & Architecture

This lens plugs into **Sublate** (`SKILL.md`) whenever the target deliverable is software, code refactoring, or systems architecture. It preserves the **7 Core Engineering Principles** in full force while mapping them across the three orthogonal roles of the Hegelian Triad (**Thesis**, **Antithesis**, **Synthesis**).

---

## 1. The 7 Core Engineering Principles

### 1. The Producer Check (Root Cause over Defensive Bloat)
- *Question*: "How would invalid state or an empty value get stored in the first place? What code path produces it?"
- *Rule*: Never accept consumer band-aids (`?.`, `?? ''`, empty fallbacks) that patch over a symptom. If the reviewer cannot prove the code path producing invalid state, reject the band-aid. Fix the producer.

### 2. The Fail-Loud Check (Anti-Masking)
- *Question*: "Does adding this defense hide an error? For someone developing or testing this, we want them to see the error as early as possible so it can be fixed."
- *Rule*: Reject silent fallbacks that swallow exceptions or broken invariants. Code must fail loudly and visibly at the boundary.

### 3. The Single-Source-of-Truth Check (No Fallback Proliferation)
- *Question*: "Why do we have two ways to do this? Why are there multi-tiered fallbacks?"
- *Rule*: Eliminate duplicate code paths, redundant fallback layers, and backwards-compatibility wrappers. Enforce one canonical path for every operation.

### 4. The Real-World Blast Radius Check (Pragmatic Realism)
- *Question*: "What are the chances this could actually occur in real life? What breaks if we ignore this completely?"
- *Rule*: Discard theoretical 0%-probability edge cases that add unnecessary lines and complexity. Focus strictly on real failure modes.

### 5. The Upstream Origin Check (Boundary Hygiene)
- *Question*: "Is this actually a bug in an upstream or shared library?"
- *Rule*: Do not paper over upstream bugs in consumer repos. Isolate the issue and direct the patch to the originating package/repo.

### 6. The Symmetry & Terseness Check (API Elegance)
- *Question*: "Is this naming terse, symmetrical, and consistent across layers?"
- *Rule*: Eliminate stuttering and redundant prefixes (`interactions.create()` over `interactions.createInteraction()`). Enforce paired lifecycle verbs (`open`/`close`, `load`/`save`, `watch`/`unwatch`, `connect`/`disconnect`). Use the same canonical noun across DB, API, and client, and maintain parity across parallel providers.

### 7. The DRY & Abstraction Signal Check (Pragmatic Deduplication)
- *Question*: "Does this change duplicate logic that already exists, or introduce competing patterns for the same task? Is there an opportunity to abstract?"
- *Rule*: Treat repetition as a guiding light signaling an opportunity to refactor and consolidate. Avoid creating competing implementations or copy-pasting parallel patterns. Balance this pragmatically against delivery velocity and real-world blast radius—do not dogmatically re-architect an entire subsystem for minor, inconsequential duplication. Whenever a refactor is warranted, favor addressing it upfront to prepare the ground before layering on new features.

---

## 2. How the Triad Operates Under This Lens

Rather than all three agents mechanically repeating the same checklist, each member of the Triad wields a distinct subset of these principles as its primary weapon:

| Triad Role | Primary Principles Wielded | Operational Mandate in Code |
| :--- | :--- | :--- |
| **THESIS** *(The Architect-Artisan)* | **#2 Fail-Loud**, **#3 Single-Source-of-Truth**, **#6 Symmetry & Terseness**, **#7 Upfront DRY** | Write direct, canonical, fail-loud code with symmetrical APIs and zero consumer band-aids. Commit logically contiguous milestones (`topic: message`) and run test suites locally. |
| **ANTITHESIS** *(The Iconoclast)* | **#1 Producer Proof (Epistemic Constraint)**, **#2 Anti-Masking**, **#3 Competing Paths**, **#6 Asymmetry** | Hunt for race conditions, unhandled async paths, resource leaks, and broken invariants. **Must trace the exact producer code path** (`file:line`) before claiming any state/null bug. Call out clunky "Rube Goldberg" mechanics via Structural Provocations. |
| **SYNTHESIS** *(The Sublator)* | **#1 Producer over Band-Aid**, **#4 Real-World Blast Radius**, **#5 Upstream Origin**, **#7 Upfront Scope** | Scan for upfront DRY refactors at Setup. During Beat 3, use **#4 Blast Radius** to `[NEGATE]` 0%-probability fantasies, **#1 Producer Check** to forbid `?.` consumer band-aids, **#5 Upstream** to `[EXTERNALIZE]` library bugs, and curate clean git history on completion. |

---

## 3. Git Baseline & History Protocol (Code Deliverables)

When this lens is active in a git repository:
1. **Immutable `<base_commit>`**: Recorded at Setup (`git rev-parse HEAD` or user-specified ref). Never shifts during the run. If the working tree is dirty at boot, Synthesis records a snapshot/stash or baseline commit first.
2. **Thesis Milestones**: Thesis creates focused, logically contiguous commits with lowercased, topic-prefixed messages (e.g., `core: unify watcher lifecycle`).
3. **Delta + Anchor Diffing**:
   - Iteration 1: Antithesis audits `git diff <base_commit>..HEAD`.
   - Iterations $2 \dots N$: Because Antithesis persists across iterations and already holds prior context, it audits only the iteration delta (`git diff <last_reviewed_commit>..HEAD`), consulting `<base_commit>..HEAD` only if needed to verify net boundary state or after a context compaction.
4. **History Curation**: On convergence, if multiple iterations generated intermediate fixup commits, Synthesis curates `<base_commit>..HEAD` (`rebase`, `squash`, `reword`) into clean, atomic commits before outputting `### STATUS: SUBLATED`.
