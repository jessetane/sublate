# Lens: Code & Systems Architecture

Example domain lens for code, refactoring and systems architecture.

## Shared Principles (High → Low)

1. **Minimal Mechanism & Abstraction Purity**: Keep architecture generic, orthogonal and minimal. Do not bolt special-case flags or extra moving parts onto core machinery when a simpler primitive or boundary change makes the problem vanish. Generic layers (runners, engines, routers, UI) must never hardcode concrete plugin/subclass names or inspect subtype internals.
2. **Single Source of Truth & Clean Removal**: Enforce one canonical path for every operation and piece of state. Never introduce multi-tiered fallbacks, dual code paths, deprecation warnings or backwards-compatibility shims unless explicitly requested.
3. **Pragmatic DRY & Upfront Consolidation**: Treat repetition or competing patterns as a signal to consolidate shared abstractions upfront before layering on new features, balanced pragmatically against scope.
4. **Fix the Producer, Not the Consumer**: Never slap defensive band-aids (`?.`, `?? ''`, downstream workarounds) onto consumers to mask an upstream bug. Trace the bad state to its origin and fix the producer (or isolate at the external boundary).
5. **Fail Loud & Never Guess**: Never swallow exceptions (`catch (err) {}`), mask broken invariants with silent fallbacks or guess when state is missing or ambiguous (e.g. heuristic `a || b || 'default'` chains, silent coercion or magic defaults that hide upstream bugs as mysterious downstream artifacts). Let errors and violated contracts fail visibly at the boundary, and ignore theoretical 0%-probability edge cases with no real-world blast radius.
6. **Symmetry & Terseness**: Eliminate stuttering (`users.create()` over `users.createUser()`). Pair lifecycle verbs (`open`/`close`, `load`/`save`, `watch`/`unwatch`, `connect`/`disconnect`). Use the same canonical noun across DB, API and UI.
7. **Native Platform & Dense Style**: Reach for vanilla platform features first (ES modules, `fetch`, `EventTarget`, `WebSocket`, `node:test`, Web Components) before adding dependencies. Write dense, idiomatic code: tabs, no semicolons, `function` declarations, `async`/`await` with top-level `await` and no blank lines inside function bodies.

## Role Dynamics

### Thesis

- **Boundary-First Testing**: Prefer high-level integration and behavioral tests at public boundaries (especially while architecture is evolving), reserving focused unit tests for self-contained logic rather than coupling white-box assertions to internal wiring or private state. When changing or retiring a contract, delete or update superseded tests *first* so obsolete assertions never pressure you into keeping dead hooks or shims.  
- Commit logically contiguous milestones using lowercased, topic-prefixed messages (e.g. `core: unify watcher lifecycle`).  
- In your report, include commit range (`<start>..<head>`), touched files, and separate production vs. test diff stats under `CHANGES`, test commands/results under `VERIFICATION`, and architectural choices under `KEY_DECISIONS`.  

### Antithesis

- Use shell execution to inspect `git diff`, `git log`, test suites and non-destructive one-liner reproducers; never modify tracked files or git history.  
- Evaluate broad architectural strokes (`git diff <base_anchor>..HEAD`, production-only diff stats excluding test files, and repo-wide searches for unmigrated callers, subclasses or siblings outside the diff) first, then zoom into the iteration delta (`git diff <last_reviewed_state>..HEAD`). Flag `[P#]` for file/class decomposition that merely relocates state without simplifying it, legacy hooks/shims or fallback chains left behind, duplicate code paths, fix-induced bloat (e.g. boolean mode flags, ad-hoc state juggling, or net production LOC growth on a consolidation/subtraction task), and brittle white-box tests coupled to internal seams.  
- Cite exact `file:line` producer paths (or upstream package boundaries) for every `[F#]`.  

### Synthesis

- **Baseline (`<base_anchor>`)**: On clean working trees, lock `git rev-parse HEAD`. If uncommitted WIP is the subject of the task, commit it as `wip: pre-sublate baseline` (or build on `HEAD`) rather than stashing it away; stash (`git stash push -u -m "sublate-autostash"`) only unrelated noise.  
- **History Curation**: On convergence, if multiple iterations created intermediate fixup or temporary baseline commits, curate `<base_anchor>..HEAD` into clean, logical commits using `git reset --soft <base_anchor>` (never run interactive `git rebase -i`).  
