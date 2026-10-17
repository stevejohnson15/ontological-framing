# Ontological Framing in LLMs — Research Design

> **Working title (Tier 1 paper):** *Simples Arranged Promptwise: Probing Quantifier Variance in Large Language Models*
>
> This is the canonical design document for the project, versioned in-repo.
> Originally drafted as a Claude doc (https://claude.ai/code/artifact/87132356-44a7-4fcd-b9b7-8f25f7fa3ca9);
> this file supersedes it for project work. `PLAN.md` tracks build status and defers
> to this file on *what and why*.
>
> As of: 2026-10-17 · Steve Johnson

---

## The research program at a glance

| Tier | Study | Core question | Status |
| --- | --- | --- | --- |
| **1** | Probing (no training) | Do existence verdicts track scenarios or ontological framing in off-the-shelf models? | **Active — detailed below** |
| **2** | Fine-tuning on ontological languages | Do models tuned on nihilist / universalist / common-sense corpora diverge only verbally, or also behaviorally? | Future — sketch in §Tier 2 |
| **3** | Beetle-box payoff | If disjoint ontological training yields behavioral convergence, has the "deep fact" about composition dropped out of the language game as the beetle does? | Future — sketch in §Tier 3 |

Tier 1 is a self-contained paper and builds the instrument (validated vignette battery)
the later tiers reuse. Tier 2 is the flagship empirical study. Tier 3 is the philosophical
payoff piece, and connects to the broader "Experimental Wittgenstein" program.

---

# Tier 1 — Probing study (detailed design)

## Working title and abstract

**Working title:** *Simples Arranged Promptwise: Probing Quantifier Variance in Large Language Models*

**Abstract (draft):** Quantifier variance (Hirsch) holds that disputants in composition
debates speak different but equally adequate languages, so that nothing in the world
settles whether simples arranged chairwise compose a chair. This paper treats that claim
as empirically probeable. We present large language models with a battery of classic
composition vignettes (statue/clay, Ship of Theseus, gerrymandered fusions, vague
composition) under systematically varied ontological framings — nihilist, universalist,
and common-sense idioms — while holding the described scenario fixed. We measure whether
existence verdicts track the scenario or the framing, using answer distributions,
paraphrase consistency, and (in an interpretability extension) linear probes for scenario
representations that remain invariant across framings. Framing-dependent verdicts over
invariant scenario representations would supply a mechanistic analogue of Hirsch's
picture: shared facts, divergent ontological languages, with "exist" functioning as a
parameter of the language game rather than a world-tracking term. We discuss what such
results do and do not show against ontological realism, and how the method extends
Wittgenstein's public-use arguments to artificial speakers.

## Motivation

The paper sits at the intersection of two literatures that have not yet met:
metametaphysical deflationism and empirical work on LLM representations.

**The philosophical side.** Hirsch's quantifier variance claims composition disputes are
verbal: the nihilist and the universalist can each translate the other's claims without
loss, so neither language is metaphysically privileged. Thomasson's easy ontology reaches
a similar deflationary verdict by a different route. Sider replies that quantificational
structure can "carve at the joints," so there is a fact about which language is better.
The debate has mostly been conducted by intuition about imagined linguistic communities —
what *would* a tribe of native nihilist speakers be able to say, translate, or do?

**The empirical opening.** LLMs are the first artifacts that actually *are* large-scale
linguistic competence detached from any prior ontological commitment of their own. They
let us replace the imagined tribe with a measurable system: we can hold a described
scenario fixed, vary only the ontological idiom, and observe whether existence verdicts
are scenario-driven or framing-driven — and, with interpretability tools, whether the
internal scenario representation is shared across framings while the verdict is computed
late.

**The Wittgensteinian stake.** The beetle-box argument says a referent that plays no role
in public use drops out of the language game. The composition analogue: if "object" has
no content beyond its inferential and practical role in a corpus, then models exposed to
different ontological idioms should diverge only verbally, not behaviorally. Tier 1 does
not test this fully (that is Tier 2's fine-tuning study), but it establishes the
instrument: a validated vignette battery and the finding, positive or negative, about
framing-sensitivity in off-the-shelf models.

**Why this is a contribution even if results are "boring."** Framing-insensitivity
(verdicts track scenarios) would itself be notable — evidence that corpus-trained
competence encodes a stable common-sense ontology that resists idiom. Either outcome
feeds the metametaphysics debate; the paper is not hostage to one result.

## Research questions and hypotheses

**RQ1.** Do LLM existence/composition verdicts vary with ontological framing when the
described scenario is held fixed?

**RQ2.** Are verdicts consistent across semantically equivalent paraphrases *within* a
framing (i.e., is the effect framing, not surface wording)?

**RQ3.** Do models preserve truth-conditions when asked to translate between ontological
idioms (nihilist ↔ universalist ↔ common-sense)?

**RQ4 (interpretability extension).** Is there a scenario representation, recoverable by
linear probes, that is invariant across framings while the verdict is framing-sensitive
and computed in later layers?

**Hypotheses:**

- **H1 (Hirschian prediction):** Verdicts are substantially framing-dependent;
  translation between idioms preserves downstream judgments (counting, persistence,
  tracking).
- **H2 (common-sense entrenchment):** Verdicts track the common-sense idiom regardless of
  framing, because training corpora are saturated with it; nihilist and universalist
  framings are treated as deviant registers, not alternative languages.
- **H3 (incoherence):** Verdicts are unstable within framings (fail RQ2), in which case
  the instrument reveals noise, not language-games, and the philosophical reading must be
  heavily qualified.
- **H4 (mechanistic Hirsch):** Probes find shared scenario geometry across framings;
  verdict direction is linearly decodable only late and flips with framing.

H2 and H3 are the sober null-adjacent outcomes; the paper is written so each hypothesis
has a stated philosophical consequence.

## Vignette battery design

Target: 8 case families × ~6 base vignettes × 3 framings × 4 paraphrases ≈ **575 items**,
machine-expandable from templates. Each vignette describes a scenario in ontologically
neutral physical terms first, then poses the probe question.

| Case family | Canonical source | Example probe | What it tests |
| --- | --- | --- | --- |
| Ordinary composition | van Inwagen, *Material Beings* | Particles bonded in a lattice on four legs — is there a table here? | Baseline existence verdicts |
| Gerrymandered fusions | Lewis; Hirsch's "incars" | The fusion of your left shoe and the Eiffel Tower — is that an object? | Universalist tolerance |
| Statue and clay | Gibbard; Burke | Lump shaped into a statue — one thing here or two? | Coincidence, constitution |
| Ship of Theseus | Hobbes; modern variants | Planks replaced one by one; originals reassembled — which ship is which? | Persistence, identity |
| Vague composition | Sider's argument from vagueness | Grains added one at a time — when does a heap begin to exist? | Sharp cutoffs vs. tolerance |
| Scattered objects | Cartwright | A disassembled watch in a drawer — is the watch in the drawer? | Spatial connectedness intuitions |
| Artifacts vs. aggregates | Thomasson, *Ordinary Objects* | Sand piled by wind vs. piled by a child making a castle — same ontology? | Intention-dependence |
| Creation by fiat | Hirsch; Thomasson's easy arguments | We decide to call the fusion of the moon and a coin "Monet" — does Monet exist? | Easy existence inferences |

**Construction rules:**

- Every vignette has a *neutral physical core* (particle/arrangement description) written
  once and reused verbatim across framings, so framing is the only manipulated variable.
- Probes come in two response formats: forced-choice (yes/no, A/B) for logprob
  measurement, and free generation for qualitative coding. Forced-choice is primary.
- Each family includes one *control item* with an uncontroversial answer under all three
  ontologies (e.g., "is there at least one simple here?") to catch degenerate response
  strategies.
- Templates parameterized by object kind (table, chair, statue, heap) to wash out lexical
  idiosyncrasies of any single noun.

## Framing conditions and controls

**Three framing conditions**, instantiated as a preamble plus consistent vocabulary
throughout the probe:

1. **Nihilist idiom.** Preamble defines the "strict" register: only simples exist;
   ordinary object talk is paraphrased as plural predications ("simples arranged
   tablewise"). Probe questions use the paraphrase vocabulary.
2. **Universalist idiom.** Preamble states unrestricted composition: any plurality
   composes something. Probe questions freely quantify over fusions.
3. **Common-sense idiom.** No ontological preamble; ordinary English. This is both a
   condition and the de facto baseline.

**Framing-delivery variants** (to separate instruction-following from
language-internalization):

- *Explicit instruction* ("adopt the following view…") — measures compliance.
- *Immersive context* (several paragraphs written natively in the idiom, no instruction)
  — closer to speaking the language than obeying a rule. The Hirschian reading leans on
  the immersive variant; the contrast between the two is itself a finding.

**Controls:**

- **Paraphrase set (RQ2):** 4 surface rewordings per item per framing; within-framing
  instability caps any claims about framing effects.
- **Order and position:** randomize yes/no polarity and option order; known LLM response
  biases make this non-optional.
- **Non-ontological control questions** embedded in each framing (arithmetic, spatial
  relations) to confirm the framing doesn't degrade general competence — ruling out "the
  nihilist preamble just confuses the model."
- **Model sweep:** ≥3 model families, ≥2 sizes each, to separate idiosyncrasy from
  pattern; scale trends (does framing-sensitivity grow or shrink with capability?) are
  philosophically interesting either way.
- **Human baseline (optional but strong):** run a subset on human participants via
  standard survey platforms; experimental-philosophy literature on composition intuitions
  gives prior art and effect sizes to compare against.

## Measures and analysis plan

**Primary measures:**

- **Verdict distribution:** P("yes") from token logprobs on forced-choice items
  (open-weight models), answer frequencies over sampled generations (API models). Report
  both where possible; note they can diverge.
- **Framing effect size:** per-item shift in P("yes") across framings with the scenario
  fixed; aggregate via mixed-effects logistic regression — fixed effects: framing, case
  family, delivery variant; random intercepts: item template and model.
- **Paraphrase consistency (RQ2):** within-framing variance across the 4 rewordings;
  items failing a consistency threshold are flagged, and headline analyses rerun
  excluding them.
- **Translation fidelity (RQ3):** model translates its own claims between idioms; score
  truth-conditional equivalence with an independent grader model plus a hand-coded
  subsample for grader validation. Report inter-rater agreement.

**Secondary measures:**

- **Downstream task invariance:** counting objects, persistence tracking, and simple
  physical reasoning posed inside each framing; performance parity across framings is the
  behavioral cash-value of "merely verbal."
- **Confidence language:** hedging and meta-commentary in free generations, qualitatively
  coded — does the model treat nihilist questions as deviant (H2 signature) or as a
  register it inhabits (H1 signature)?

**Inference discipline:**

- Preregister hypotheses, exclusion rules, and the primary regression before full data
  collection (OSF); the philosophical audience will be rightly suspicious of post-hoc
  storytelling.
- Power/stability check on a 10% pilot; expand templates where variance is high.
- All prompts, code, and raw outputs released; the battery itself is a citable artifact
  independent of any one result.

## Interpretability layer (RQ4)

Scoped as an extension on one or two open-weight models — the paper stands without it,
but it is the distinctive contribution if it lands.

- **Scenario probes:** train linear probes on residual-stream activations to decode
  scenario properties (number of maximal bonded clusters, arrangement kind, spatial
  connectedness) from the neutral physical core. Test whether probe accuracy and
  representational geometry are invariant across the three framings — the "shared facts"
  half of the Hirschian picture.
- **Verdict probes:** decode the eventual yes/no verdict layer by layer. The H4
  signature: scenario information is present early and framing-invariant; verdict
  direction becomes decodable late and flips with framing.
- **Representational similarity:** CKA or RSA between activation sets for identical
  scenarios under different framings, layerwise — a convergence-then-divergence profile
  across depth is the headline figure.
- **Causal check (stretch):** activation patching — patch the framing-preamble
  representations from a nihilist run into a common-sense run and see whether the verdict
  flips while scenario decoding is undisturbed. This upgrades the correlational story to
  a causal one and would substantially strengthen the paper.

**Interpretive caution to state in the paper:** probes finding a representation is weaker
than the model *using* it; the patching experiment is what licenses use-talk. Keep the
philosophical conclusions indexed to which level of evidence was achieved.

## Anticipated objections and responses

| Objection | Source | Response in the paper |
| --- | --- | --- |
| "LLMs aren't speakers; their outputs tell us nothing about languages." | General philosophical skepticism | Claim is calibrated: LLMs model *linguistic practice at corpus scale*, which is exactly the level quantifier variance is about. No claims about model understanding are needed. |
| "Framing effects just show prompt-following, not alternative languages." | Methodological | The explicit-instruction vs. immersive-context contrast is designed for this; immersive effects are the evidentially relevant ones, and the contrast is reported either way. |
| "Behavioral equivalence doesn't touch realism — structure isn't a matter of what speakers do." | Sider | Conceded as a limit, then sharpened: the result specifies what the realist must hold — a difference making no difference to any realizable linguistic practice. That reframing is a contribution, not a refutation. |
| "Models are palimpsests of common-sense English; there's no clean nihilist condition." | Training-data confound | True, and stated. H2 exists for this reason; Tier 2's fine-tuning addresses it directly. Also note the Wittgensteinian spin: no neutral standpoint from which to raise a speaker may itself be the lesson. |
| "Wittgenstein would reject treating his method as a thesis to test." | Hacker-style exegetes | The paper tests *Hirsch's* thesis, which is a theory; Wittgenstein supplies motivation and interpretive frame, not the experimental target. Flag this explicitly in the framing section. |
| "Verdict instability (H3) makes the whole instrument meaningless." | Internal | Paraphrase-consistency gating is the firewall: claims are only made over items that pass RQ2. H3 as a global outcome is reported as a negative result about the method's applicability. |

## Feasibility, deliverables, and venues

**Budget and infrastructure:** API costs for the behavioral battery are modest (hundreds
of dollars at ~575 items × samples × models). The interpretability layer needs one
workstation-class GPU for a 7–13B open-weight model; rentable by the hour if not owned.

**Rough sequence (part-time, ~4–5 months):**

1. Battery construction and pilot (≈3 weeks) — templates, framings, paraphrases; pilot
   10% on two models.
2. Preregistration (≈1 week) — hypotheses, exclusions, primary regression.
3. Full behavioral run and analysis (≈4 weeks).
4. Interpretability extension (≈6 weeks, parallelizable with writing).
5. Writing (≈6 weeks) — philosophical framing is half the paper; budget accordingly.

**Deliverables:**

- The paper itself.
- The open-sourced battery + harness (a citable artifact; others will reuse it, which
  builds the research program's footprint).
- A short philosophy-audience companion piece is a natural spin-out — and the seminar
  paper on dissolution vs. Hirsch/Thomasson can cite the empirical work as "in
  progress," linking the two efforts.

**Candidate venues:** *Synthese* or *Philosophical Studies* (phil-of-science/metaphysics
crossover, receptive to empirical methods); *Ergo* (open access, fast); *Minds and
Machines* or *Philosophy & Technology* (if the LLM methodology leads); an
experimental-philosophy venue if the human-baseline arm is run. An ML-workshop version
(interpretability or NLP-meets-cognition workshops) is a low-cost way to get the
empirical half reviewed early.

---

# Tier 2 — Fine-tuning on ontological languages (future)

The "training on different ontological models" study. Depends on the Tier 1 battery and
is motivated regardless of Tier 1's outcome (and *especially* if H2 — common-sense
entrenchment — wins, since fine-tuning is the direct answer to the palimpsest confound).

**Design sketch:**

- Construct parallel corpora describing the same scenes in three idioms: mereological
  nihilist ("simples arranged chairwise"), universalist, and common-sense. Corpora must
  be content-matched (same scenes, same facts) and differ only in ontological vocabulary
  and quantificational practice.
- Produce model variants per idiom via fine-tuning, or more cheaply via in-context
  "constitution" documents; compare the two induction methods as a secondary question.
- **Test 1 — Translatability (Hirsch's prediction):** can the nihilist-tuned variant
  systematically paraphrase into the universalist's claims and vice versa, preserving
  truth-conditions? A Siderian realist might predict residue — claims about *structure*
  that don't survive translation.
- **Test 2 — Convergence under pressure:** give variants downstream tasks where ontology
  could matter — counting, persistence tracking, physical reasoning. Identical
  performance ⇒ the ontological vocabulary does no predictive work (deflationism
  vindicated in a small way). Divergence on *non-verbal* tasks ⇒ evidence the "merely
  verbal" diagnosis undersells something.
- Reuse Tier 1's battery, measures, consistency gating, and interpretability tooling on
  the tuned variants (do the variants' scenario representations converge while their
  verdict readouts differ?).

**Known hard problems to solve before committing:** corpus construction at sufficient
scale without leaking common-sense idiom; deciding what "same facts" means in the
nihilist corpus without begging the question; separating idiom-learning from
instruction-memorization.

# Tier 3 — The beetle-box payoff (future)

The philosophical capstone, written once Tier 2's results exist. Connects the program to
the private-language argument and the broader "Experimental Wittgenstein" agenda.

- The beetle argument: a referent that plays no role in public use drops out of the
  language game. The composition analogue: if models with disjoint ontological training
  converge on all behavior, the "deep fact" about composition has dropped out exactly as
  the beetle does — "object" has no content beyond its inferential/practical role.
- LLMs serve as model organisms for public-use arguments applied to ontology: the first
  setting where "a community that speaks the other language" can actually be built and
  measured rather than imagined.
- Honest limit, stated up front: the Siderian can say behavioral equivalence doesn't
  touch realism. The paper's contribution is sharpening the realist's commitment — a
  difference that makes no difference to any realizable linguistic practice — not
  refuting it.
- Likely venue: a metaphysics/metaphilosophy journal; this is the piece that can also
  stand as the successor to the metaphysics-seminar paper on dissolution vs.
  Hirsch/Thomasson.

---

# Background reading list

Ordered within each group roughly by priority. Starred (★) items are the minimum viable
core — about a dozen pieces that make the paper writable.

**A. Quantifier variance and metaontology (the paper's direct targets)**

- ★ Eli Hirsch, "Quantifier Variance and Realism" (2002) and "Physical-Object Ontology,
  Verbal Disputes, and Common Sense" (2005) — both collected in his *Quantifier Variance
  and Realism: Essays in Metaontology* (OUP, 2011).
- ★ David Chalmers, David Manley & Ryan Wasserman (eds.), *Metametaphysics: New Essays on
  the Foundations of Ontology* (OUP, 2009) — especially Chalmers's "Ontological
  Anti-Realism," Hirsch's chapter, Thomasson's chapter, Yablo's "Must Existence-Questions
  Have Answers?", and Sider's "Ontological Realism."
- ★ Amie Thomasson, *Ontology Made Easy* (OUP, 2015); her *Ordinary Objects* (OUP, 2007)
  for the first-order defense.
- ★ Ted Sider, *Writing the Book of the World* (OUP, 2011) — chs. 1–5 and the
  quantifier-variance discussion; the realist position the study must survive.
- Rudolf Carnap, "Empiricism, Semantics, and Ontology" (1950) — the ancestor of the whole
  deflationary line.
- Vera Flocke, "Recent Work on Quantifier Variance" (survey article) and the SEP entry
  "Metaontology" — orientation and current state of play.
- David Plunkett & Tim Sundell, "Disagreement and the Semantics of Normative and
  Evaluative Terms" (2013) — metalinguistic negotiation, the modern toolkit for "verbal"
  disputes that aren't idle.

**B. Composition and material objects (the first-order debate being probed)**

- ★ Peter van Inwagen, *Material Beings* (Cornell, 1990) — ch. 2 and §§8–13; source of
  "simples arranged chairwise."
- ★ Daniel Z. Korman, *Objects: Nothing Out of the Ordinary* (OUP, 2015) — the best
  single map of the composition debate, and the conservative position the common-sense
  framing instantiates. His SEP entry "Ordinary Objects" is the quick version.
- David Lewis, *On the Plurality of Worlds* §4.3 and *Parts of Classes* — unrestricted
  composition and the vagueness argument.
- Trenton Merricks, *Objects and Persons* (OUP, 2001) — eliminativism with a different
  rationale than van Inwagen's.
- Karen Bennett, "Composition, Colocation, and Metaontology" (in *Metametaphysics*) — the
  "difference-minimizing" diagnosis; very close in spirit to what the experiment
  operationalizes.

**C. Wittgenstein and the dissolution tradition (frame and motivation)**

- ★ Wittgenstein, *Philosophical Investigations* — §§1–64 (chair, simples,
  Theseus-adjacent §§79–80) and §§89–133 (the methodological core); §§243–315 for the
  beetle (§293).
- Gordon Baker & P.M.S. Hacker, *Wittgenstein: Understanding and Meaning* — exegetical
  backing for the method sections (consult, don't read whole).
- Oswald Hanfling, *Wittgenstein and the Human Form of Life* (2002) — ordinary-language
  treatment of ontological questions.
- Paul Horwich, *Wittgenstein's Metaphilosophy* (OUP, 2012) — a contemporary analytic
  reconstruction of the deflationary method; useful bridge to the metaontology
  literature.
- A.W. Moore or David G. Stern on the method of §§109–133 — pick one commentary for the
  "theses vs. therapy" issue the Hacker-style objection raises.

**D. Experimental philosophy of ordinary objects (prior art for the human baseline)**

- Joshua Knobe & Shaun Nichols (eds.), *Experimental Philosophy* vols. 1–2 — methods
  orientation.
- David Rose & Jonathan Schaffer, "Folk Mereology is Teleological" (*Noûs*, 2017) — the
  landmark empirical study of composition intuitions; its vignette style is a direct
  template for the battery.
- David Rose et al., cross-cultural follow-ups on object concepts and the Ship of Theseus
  — effect sizes and materials to compare against.
- Daniel Korman & Chad Carmichael, "Composition" (Oxford Handbooks / Philosophy Compass
  surveys) — where intuitions enter the dialectic.

**E. LLMs, world models, and interpretability (the instrument)**

- ★ Kenneth Li et al., "Emergent World Representations" (Othello-GPT, ICLR 2023) and Neel
  Nanda's linear-probe follow-up — the paradigm for "scenario representation" probing.
- Wes Gurnee & Max Tegmark, "Language Models Represent Space and Time" (2023) — probing
  for world-structure representations.
- Collin Burns et al., "Discovering Latent Knowledge in Language Models Without
  Supervision" (2022) — separating what a model represents from what it says; directly
  relevant to the verdict-vs-scenario split.
- Activation patching / causal tracing methodology: Meng et al., "Locating and Editing
  Factual Associations in GPT" (ROME, 2022) and subsequent patching best-practices papers
  — for the causal check.
- A current survey of probing methods and their pitfalls (e.g., Belinkov, "Probing
  Classifiers: Promises, Shortcomings, and Advances," *Computational Linguistics* 2022) —
  the "probes ≠ use" caution has a literature; cite it.

**F. Machine psychology and philosophy of LLMs (positioning and related work)**

- ★ Marcel Binz & Eric Schulz, "Using Cognitive Psychology to Understand GPT-3" (*PNAS*,
  2023) — the template for running vignette batteries on models with experimental
  controls.
- Thilo Hagendorff, "Machine Psychology" (2023–) — the methodological manifesto for this
  kind of study, including its pitfalls (prompt sensitivity, contamination).
- Raphaël Millière & Cameron Buckner, "A Philosophical Introduction to Language Models"
  (2024, two parts) — the best current map of what philosophical conclusions LLM evidence
  can and cannot support.
- David Chalmers, "Does Thought Require Sensory Grounding?" and his LLM-adjacent lectures
  — for calibrating the "models as linguistic practice" claim.
- Data-contamination and template-sensitivity critiques of LLM evaluation (e.g., work on
  benchmark contamination and prompt-perturbation robustness) — the methods section must
  show awareness.

**G. Recent adjacent work — scan of PhilPapers'
[Large Language Models category](https://philpapers.org/browse/large-language-models)
and the web, as of Oct 17, 2026**

The category holds 550+ entries, dominated by consciousness, moral status, and AI-ethics
work. Closest items to this project:

- [Coelho Mollo & Millière, "The Vector Grounding Problem"](https://philpapers.org/rec/COETVG)
  — whether LLM internal states achieve referential grounding; heavily cited; the
  reference-side counterpart to this project's existence-side question.
- [Du Sordet, "World Models in LLMs: Representing Causes and Principles"](https://philpapers.org/rec/DUSWLH)
  — argues from mechanistic-interpretability evidence about what LLM world models
  represent; directly relevant to RQ4, and to the point that wrong outputs are weak
  evidence against internal representations.
- [Grindrod, "LLMs, Descriptivism, and Structuralism"](https://philpapers.org/rec/GRILDA-4)
  — how structural representations handle singular reference, with proposed experiments;
  the closest methodological cousin found (philosophy-of-language question → probing
  study design).
- [Arai & Tsugawa, "Do Large Language Models Defend Inferentialist Semantics?"](https://arxiv.org/abs/2412.14501)
  — Brandomian inferentialism and anti-representationalism as foundational semantics for
  LLMs; adjacent to the meaning-as-use frame.
- [Piantadosi & Hill, "Meaning without reference in large language models"](https://arxiv.org/abs/2208.02957)
  — conceptual-role account of LLM meaning; useful for the beetle-box argument's
  "referent drops out" move.
- [Grzankowski, Keeling, Shevlin & Street, "Deflating Deflationism"](https://philpapers.org/rec/GRZDDA)
  — evaluates debunking arguments against attributing mentality to LLMs; relevant to
  calibrating what the paper's probes do and don't license.
- [Chalmers, "What we talk to when we talk to language models"](https://philpapers.org/rec/CHAWWT-8)
  — characterizing the LLM interlocutor; background for the "models as linguistic
  practice" claim.
- ["Asymmetric Communication: Large Language Models and Language Games"](https://arxiv.org/pdf/2607.28137)
  (2026) — applies *Investigations*-style language games, forms of life, and the private
  language argument to LLMs; the most directly Wittgensteinian recent entry found.
- [WoLaLa workshop — "Large Language Models and the Philosophy of Language Games"](https://wolala.nytud.hu/wp-content/uploads/2025/11/WoLaLa-2025_paper_27-2.pdf)
  (2025) — evidence an active workshop community exists at this intersection; a candidate
  early venue.
- *The Philosophy of Language Models*
  ([Philosophy Compass, 2026](https://ora.ox.ac.uk/objects/uuid:d06d32ab-8620-4984-858e-cce2a560c89f))
  — peer-reviewed survey of the philosophical debates (compositionality, meaning,
  representation, reasoning); updates the Millière & Buckner map.

**Gap check:** nothing found in the category or targeted searches on quantifier variance,
composition, or metaontology tested against LLMs — the philosophy-of-LLMs literature
clusters on mind, meaning, and reference, not existence questions. The novelty claim in
the Motivation section holds as of this scan.

**Reading order suggestion:** A★ → B★ → Rose & Schaffer (D) → E★/F★ → the rest as the
writing demands. Groups A and B double as preparation for the metaphysics seminar itself;
the seminar paper and this study share roughly half their bibliography by design.
