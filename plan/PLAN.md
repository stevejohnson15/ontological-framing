# PLAN.md — Ontological Framing in LLMs (Tier 1 Probing Study)

> **Working title:** *Simples Arranged Promptwise: Probing Quantifier Variance in Large Language Models*
>
> **Canonical design doc:** `DESIGN.md` in this repo — full rationale, hypotheses,
> objections, reading list, and sketches of the later tiers. When the two diverge,
> `DESIGN.md` wins on *what and why*; this file wins on *how and status*.
>
> A shareable Claude-doc version of the design also exists
> (https://claude.ai/code/artifact/87132356-44a7-4fcd-b9b7-8f25f7fa3ca9); `DESIGN.md`
> supersedes it for project work.
>
> **Program shape:** Tier 1 (this plan) builds the instrument and is a self-contained
> paper. Tier 2 (fine-tuning on ontological languages) and Tier 3 (beetle-box payoff)
> are future phases — see "Future phases" below and DESIGN.md §Tier 2 / §Tier 3.

## One-paragraph summary

Treat Hirsch's quantifier variance as empirically probeable: present LLMs with classic
composition vignettes (statue/clay, Ship of Theseus, gerrymandered fusions, vague
composition) under systematically varied ontological framings — nihilist, universalist,
common-sense — while holding the described scenario fixed. Measure whether existence
verdicts track the scenario or the framing (logprobs, paraphrase consistency), and in an
interpretability extension, whether a framing-invariant scenario representation underlies
framing-sensitive verdicts.

## Hypotheses (see design doc §Research questions)

- **H1** Hirschian: verdicts framing-dependent; translation preserves downstream judgments
- **H2** Common-sense entrenchment: verdicts track ordinary idiom regardless of framing
- **H3** Incoherence: verdicts unstable within framings (instrument reveals noise)
- **H4** Mechanistic Hirsch: shared scenario geometry; verdict computed late, flips with framing

## Repository structure (target)

```
ontological-framing/
├── PLAN.md                  # this file
├── DESIGN.md                # canonical design doc (all tiers)
├── battery/
│   ├── templates/           # vignette templates, one file per case family
│   ├── framings/            # nihilist / universalist / commonsense preambles (explicit + immersive)
│   ├── paraphrases/         # rewording rules or per-item paraphrase sets
│   └── build.py             # expands templates × framings × paraphrases → items.jsonl
├── harness/
│   ├── run_behavioral.py    # queries models, collects logprobs / sampled answers
│   ├── models.yaml          # model roster (API + open-weight), decoding params
│   └── graders/             # translation-fidelity grading (grader model + rubric)
├── analysis/
│   ├── primary.R|py         # mixed-effects logistic regression (prereg'd spec)
│   └── consistency.py       # RQ2 paraphrase-consistency gating
├── interp/                  # RQ4: probes, RSA/CKA, activation patching (open-weight only)
├── prereg/                  # OSF preregistration draft
└── results/                 # raw outputs (gitignored if large), figures, tables
```

## Milestones

### M1 — Battery construction & pilot (~3 wks)
- [ ] Write the 8 case-family templates (see design doc §Vignette battery: ordinary
      composition, gerrymandered fusions, statue/clay, Theseus, vague composition,
      scattered objects, artifacts vs. aggregates, creation by fiat)
- [ ] Each template: **neutral physical core** written once, reused verbatim across framings
- [ ] Write 3 framing preambles × 2 delivery variants (explicit instruction / immersive context)
- [ ] 4 paraphrases per item; 1 control item per family (uncontroversial under all ontologies)
- [ ] `build.py` expands to items.jsonl (~575 items); lint for leakage of framing vocab into cores
- [ ] Pilot 10% on two models; check response-format compliance, polarity/order randomization

### M2 — Preregistration (~1 wk)
- [ ] Freeze hypotheses, exclusion rules, primary regression spec; post to OSF

### M3 — Full behavioral run & analysis (~4 wks)
- [ ] Run roster: ≥3 model families, ≥2 sizes each (models.yaml)
- [ ] Primary: mixed-effects logistic (framing, family, delivery; random: template, model)
- [ ] RQ2 consistency gating; RQ3 translation-fidelity grading + hand-coded validation subsample
- [ ] Secondary: downstream-task invariance (counting, persistence, physical reasoning)

### M4 — Interpretability extension (~6 wks, parallel with writing)
- [ ] Scenario probes (cluster count, arrangement kind, connectedness) across framings
- [ ] Layerwise verdict decoding; RSA/CKA convergence-then-divergence profile
- [ ] Stretch: activation patching (framing-preamble patch → verdict flip, scenario intact)

### M5 — Writing (~6 wks)
- [ ] Paper (philosophical framing is half the work — see DESIGN.md §Objections table)
- [ ] Release battery + harness + raw outputs

## Future phases (not scheduled — design sketches in DESIGN.md)

**Tier 2 — Fine-tuning on ontological languages.** Content-matched parallel corpora in
nihilist / universalist / common-sense idioms; model variants per idiom (fine-tuning
and/or in-context constitutions); test Hirschian translatability between idioms and
behavioral convergence on downstream tasks (counting, persistence, physical reasoning).
Reuses the Tier 1 battery, measures, and interp tooling. Build triggers: Tier 1 battery
validated (M1–M3 complete); especially motivated if H2 (common-sense entrenchment) wins,
since fine-tuning directly answers the palimpsest confound. Pre-work that can start
early: corpus-construction experiments (writing scene descriptions natively in nihilist
idiom without leaking common-sense vocabulary — the hardest open problem).

**Tier 3 — Beetle-box payoff.** The philosophical capstone written over Tier 2's
results: if disjoint ontological training converges behaviorally, the "deep fact" about
composition drops out of the language game as the beetle does. No build work; a writing
project gated on Tier 2 results. Candidate successor to the metaphysics-seminar paper.

**Repo implication now:** keep `battery/`, `harness/`, and `analysis/` tier-agnostic
(nothing hardcoded to "the three framings are prompts" — Tier 2 swaps prompts for model
variants), so the Tier 1 instrument reuses cleanly.

## Design invariants (do not violate without updating the design doc)

1. Framing is the **only** manipulated variable per item — scenario cores are byte-identical
   across framings.
2. Forced-choice is the primary response format; logprobs where available.
3. Every claim about framing effects must survive RQ2 paraphrase-consistency gating.
4. Known-answer controls and non-ontological competence checks ship in every run.
5. Interpretability claims stay indexed to evidence level: probes ≠ use; patching licenses use-talk.

## Immediate next actions

1. Scaffold the repo structure above
2. Draft the first case family end-to-end (ordinary composition: table/chair) — core,
   three framings, four paraphrases, control item — as the pattern for the other seven
3. Write `build.py` + a schema for items.jsonl (id, family, core_id, framing, delivery,
   paraphrase_idx, question, options, polarity)

## References

- Full reading list: DESIGN.md §Background reading list (groups A–G)
- Gap check (novelty): no prior work found on quantifier variance / composition / metaontology
  tested against LLMs (scan of PhilPapers LLM category + web, 2026-10-17; DESIGN.md §G)
- Methods anchors: Rose & Schaffer "Folk Mereology is Teleological" (vignette style);
  Binz & Schulz PNAS 2023 (battery-on-models paradigm); Li et al. Othello-GPT + Nanda
  follow-up (scenario probing); Belinkov 2022 (probing pitfalls)
