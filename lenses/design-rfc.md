# Domain Lens: Design Docs, RFCs & Strategy

This lens plugs into **Sublate** ([`SKILL.md`](../SKILL.md)) whenever the target deliverable is an architectural RFC, system design specification, technical strategy, or analytical prose document.

---

## 1. Core Specification & Strategy Principles (Reference)

1. **The Falsifiable Claim Check (Concrete over Hand-Waving)**: *What would prove this design or strategy wrong? What are the explicit non-goals and trade-offs?* Reject vague aspirational language ("scalable", "seamless", "lightweight") that hides unmade decisions. Every major architectural choice must state what it sacrifices.  
2. **The Single Canonical Model Check (No Split Brain)**: *Are two different mental models or vocabularies used to describe the same mechanism?* Enforce one canonical conceptual model and glossary from top to bottom.  
3. **The Boundary & Invariant Check (Explicit Contracts)**: *Where do responsibilities hand off, and what must remain true across that boundary?* Specify state transitions, failure semantics, and ownership boundaries explicitly rather than leaving them to implementation guesswork.  
4. **The Signal-to-Noise Check (Sculpting Prose)**: *Can this section be cut in half without losing operational meaning?* Prefer tables, state diagrams, and concrete examples over walls of throat-clearing prose.  

---

## 2. Role-Partitioned Injection Blocks

When dispatching subagents, Synthesis injects **only** the matching role block below into the prompt blueprint.

### A. Thesis Lens Injection (`THESIS_LENS`)

```text
DOMAIN LENS: DESIGN DOCS, RFCS & STRATEGY (THESIS — THE ARCHITECT-ARTISAN)
- DECISIVE CLARITY (#1): Take clear, falsifiable positions. State explicit trade-offs and non-goals instead of hedging with vague adjectives ("flexible", "robust", "seamless").
- CANONICAL MODEL & VOCABULARY (#2): Introduce one coherent conceptual model. Use the exact same noun for the same concept in every section and diagram.
- EXPLICIT BOUNDARIES & INVARIANTS (#3): Define concrete inputs, outputs, state transitions, and failure modes at every boundary.
- HIGH SIGNAL-TO-NOISE (#4): Eliminate throat-clearing introductions and filler prose. Use compact tables, Mermaid diagrams, and concrete before/after examples where they compress complexity.
- OUTPUT SUMMARY: End your report with `FILES_TOUCHED`, `SECTIONS_CHANGED`, and `KEY_DECISIONS`.
```

### B. Antithesis Lens Injection (`ANTITHESIS_LENS`)

```text
DOMAIN LENS: DESIGN DOCS, RFCS & STRATEGY (ANTITHESIS — THE ICONOCLAST)
- IMMUTABLE WORKSPACE: Inspect the target documents and repository context, but DO NOT modify target files directly.
- EXPOSE HAND-WAVING & HIDDEN DECISIONS (#1 — EPISTEMIC CONSTRAINT): Flag places where the specification defers a hard design contradiction behind vague language. Trace the exact operational scenario (`section/heading` or `line`) where the ambiguity breaks down in practice.
- CONTRADICTIONS & SPLIT MODELS (#2, #3): Hunt for mismatches between diagrams, tables, and prose, or unstated assumptions at system boundaries.
- ANTI-COMMITTEE BLOAT (#4): Call out sections that read like bureaucratic filler, over-engineered taxonomy, or premature complexity, and propose 1-2 Structural Provocations that collapse the design into a simpler model.
```

### C. Synthesis Governance Rules (`SYNTHESIS_LENS`)

Synthesis applies these rules during **Setup** and **Beat 3**:

- **Scope & Premise Alignment (Setup)**: Before dispatching Thesis Iteration 1, ensure the core problem statement and constraints are explicit so Thesis does not write a sprawling encyclopedia.  
- **Sharpen Decisions, Never Hedge (Beat 3)**: When Antithesis exposes a real trade-off or ambiguity, never instruct Thesis to add a wishy-washy disclaimer or "future work" footnote. Issue a `[SUBLATE]` or `[TRANSCEND]` directive that forces a clean, committed design decision.  
- **Pragmatic Depth Filter (Beat 3)**: `[NEGATE]` demands for exhaustive implementation minutiae that do not belong at the RFC/specification layer, recording them in the Settled Ledger so the document stays crisp and readable.  
