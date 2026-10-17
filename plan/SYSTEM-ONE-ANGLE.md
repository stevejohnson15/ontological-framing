# PROPOSAL — System One / dual-process response-mode arm

> **Status: proposal, not adopted.** Nothing here is part of the Tier 1 design until it is
> folded into `DESIGN.md` (what/why) and `PLAN.md` (how/status). Decide before M2 prereg.

## Context

Question (2026-10-17): do "System One" decision models like Jev and Laya fit the study? They are
recent decision models that answer typed questions with calibrated probabilities over
fixed options, and never generate text:

- **Jev** (TypeSafe AI, 2026-09-15): closed and API-only (TypeSafe API `jev-latest`;
  OpenRouter `/api/v1/systemone`). Docs: docs.typesafe.ai/concepts/system-one,
  docs.typesafe.ai/models, openrouter.ai/docs/guides/community/jev
- **Laya** (Convai Innovations, 2026-09-18): Apache 2.0, the open reproduction of Jev.
  - Architecture: ModernBERT-large (bidirectional) plus a decision head, 421M params
    total; each option is scored at its own `[MASK]` and softmaxed over the options.
  - Training: RL against a strictly proper scoring rule (RLCD).
  - Checkpoints: `convaiinnovations/laya` (EN, 512 tokens), `-multilingual` (1024–8192
    tokens), `-typed-decisions`.
  - Code and docs: github.com/NandhaKishorM/laya, nandhakishorm.github.io/laya; Node
    wrapper github.com/receptron/laya.

**Fit:** the battery's primary measure is a forced choice with P(yes) (invariant 2), and
these models output exactly that natively. They add a reasoning-free-by-design arm, which
makes "System 1" concrete. Laya's open weights make the interpretability work feasible on
a non-decoder architecture.

## Proposed changes (if adopted)

### 1. `plan/DESIGN.md`
- **RQ5:** does framing-sensitivity depend on response mode? Three levels:
  1. System One decision models (Jev, Laya)
  2. Autoregressive LLMs, immediate answer (logprob, minimal token budget)
  3. Autoregressive LLMs, deliberative (chain-of-thought / extended thinking)
- **H5:** if verdicts in modes 1–2 follow common sense and framing effects appear only in
  mode 3, the ontological idiom is applied as a rule, not spoken (bad for Hirsch). Framing
  effects in mode 1 would be the strongest Hirschian signal. State each outcome's
  philosophical consequence.
- **Model sweep:** add a System One arm (Laya multilingual, plus Jev if access allows).
  - Only forced-choice items: there's no free generation, so neither the
    hedging/confidence measures nor RQ3 translation apply.
  - Context limit: the immersive framings need the 1024+ token checkpoint, and every item
    must be checked to fit.
- **Controls:**
  - Option order and polarity become critical (the model card warns Laya "can follow
    option labels rather than state content").
  - Known-answer controls detect degenerate scoring (base checkpoints score below the
    majority baseline zero-shot).
  - Calibration: analyse framing *shifts* within model; report temperature-scaling
    status.
- **Analysis:** add response mode as a fixed effect, with framing×mode and
  framing×mode×delivery interactions.
- **Interpretability:** run Laya scenario/verdict probes alongside a decoder model; does
  shared scenario geometry hold in a bidirectional encoder?
- **Human baseline:** speeded vs reflective condition (Kelemen's method; Rose & Schaffer
  cite it).
- **Objections:** add rows for:
  - "System 1" is a marketing analogy.
  - Dual-process theory is contested (Melnikoff & Bargh).
  - Chain-of-thought faithfulness (Turpin; Lanham).
  - Decision-model calibration is domain-dependent.
- **Reading list:** add group H (dual-process / reasoning modes) and group I (System One
  models).

### 2. `plan/PLAN.md`
- Add H5 to the hypotheses list.
- `models.yaml`: add a `system_one` model type (state + typed questions, no
  decoding params).
- **M1** pilot: Laya on the pilot items; check context fit and option-label sensitivity
  (swap labels with content fixed).
- **M2:** freeze the mode factor.
- **M3:** add mode to the regression.
- **M4:** Laya probes.

### 3. `research/`
- `I-system-one-models/`: save the Laya model card (Apache 2.0) and a `LINKS.md` with the
  Jev and Laya docs above. No PDFs exist for these; there is no arXiv paper.
- `H-dual-process/`: Evans & Stanovich 2013; Melnikoff & Bargh 2018; Kelemen & Rosset
  2009; Kelemen, Rottman & Seston 2013; Hagendorff, Fabi & Kosinski 2023 (arXiv
  2306.07622); Wei et al. 2022 (2201.11903); Turpin et al. 2023 (2305.04388); Lanham et
  al. 2023 (2307.13702); Sprague et al. 2024 (2409.12183). Verify the IDs against their
  titles before downloading; open sources only.
- Update `research/README.md` with groups H and I.

## Verification
- H5/RQ5 appear consistently in both docs; the five design invariants are unchanged.
- PDFs pass `file`, and the arXiv titles match their IDs; the PDFs stay gitignored.
- Pilot check: Laya returns valid distributions on 5 sample items, with token counts
  under the checkpoint limit.

