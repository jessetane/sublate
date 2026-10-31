# Lens: Legal Contracts & Agreements

Example domain lens for drafting, reviewing and negotiating commercial contracts, licenses and legal agreements.

## Shared Principles (High → Low)

1. **Plain Language & Structural Clarity**: Write in direct, active prose ("Customer must pay within 30 days") rather than archaic legalese ("heretofore", "witnesseth", "shall endeavor"). If a clause requires three nested exceptions to parse, restructure the underlying rule.
2. **Single Source of Definitions & Obligations**: Define each capitalized term and state each core obligation, threshold or timeline in exactly one canonical section. Never repeat a duty or numeric cap in multiple places where future edits can drift out of sync.
3. **Upfront Harmonization**: Consolidate overlapping representations, warranties and boilerplate upfront rather than layering conflicting schedules, addenda or patch clauses onto a fragmented agreement.
4. **Fix the Upstream Scope, Not Downstream Carve-Outs**: Do not bury chains of *"notwithstanding the foregoing, except to the extent that..."* bandaids inside indemnity or liability sections to patch an overbroad grant or covenant upstream. Tighten the primary scope, definition or obligation at its source.
5. **Explicit Triggers, Remedies & Cap Coherence**: Every material obligation, cure period, termination right and remedy must have an unambiguous trigger and consequence. Indemnification, limitation of liability, super-caps, warranty disclaimers and survival clauses must interlock cleanly without circularity or accidental uncapped exposure.
6. **Symmetry & Defined-Term Hygiene**: Never use a capitalized term without defining it; never define a term that goes unused. Keep reciprocal protections (confidentiality, standard of care, notice mechanics) symmetrical unless commercial asymmetry is explicitly intended by the deal terms.

## Role Dynamics

### Thesis

- Draft or refactor clauses so commercial mechanics (scope, payment, IP ownership, risk allocation, termination) read as a single cohesive architecture.  
- In your report, list modified sections under `CHANGES`, defined-term and cross-reference verification under `VERIFICATION`, and commercial risk allocations under `KEY_DECISIONS`.  

### Antithesis

- Audit the agreement as adversarial opposing counsel and a hostile future litigant without modifying the document.  
- On continuity turns, inspect the newly revised clauses (`<last_reviewed_state>..current`) first, then verify full-agreement coherence against `<base_anchor>`.  
- For every claimed loophole, ambiguity or cap leak (`[F#]`), cite the exact section/clause and trace the concrete dispute scenario and textual interaction that makes the exploit work.  

### Synthesis

- **Baseline (`<base_anchor>`)**: Lock the baseline draft (via git commit or baseline document snapshot) and confirm party posture, governing law and core deal constraints before Iteration 1.  
- **Pragmatic Risk Filter**: `[NEGATE]` speculative 0%-probability hypotheticals ("what if a meteor strikes the data center") that would bloat the agreement with defensive boilerplate, while issuing `[SUBLATE]` / `[TRANSCEND]` directives for real structural ambiguity, broken cross-references or unintended liability exposure.  
