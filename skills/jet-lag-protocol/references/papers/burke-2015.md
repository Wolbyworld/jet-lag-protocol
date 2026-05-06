# Burke, Markwald, McHill, Chinoy, Snider, Bessman, Jung, O'Neill, Wright 2015

*Effects of caffeine on the human circadian clock in vivo and in vitro.* Sci Transl Med 7:305ra146. PMC4657156.

## Methods (one line)
Double-blind, placebo-controlled, ~49-day within-subject study: caffeine equivalent to a double espresso (~200 mg) given 3 h before habitual bedtime, with parallel in-vitro experiments on cultured SCN cells.

## Numbers used by the algorithm

### Phase effect of caffeine
- **200 mg caffeine, 3 h before bedtime → ~−40 min phase delay** of melatonin rhythm
- Mechanism: adenosine A1/A2A receptor antagonism → cAMP signaling perturbation in SCN cells
- Magnitude: about half the magnitude of 3 h of evening bright light at ~3,000 lux

### Pharmacokinetics
- **Half-life**: median 5 h, range 2–10 h
- Variability is genetic (CYP1A2 *1F/*1F = slow metabolizer), behavioral (smoking induces CYP1A2 → faster metabolism; smoking cessation slows it back down), pharmacological (hormonal contraceptives slow it ~2x; pregnancy slows it 2–3x)
- 100 mg ingested → ~12.5 mg remaining 12 h later

### Onset
- 15–45 minutes after oral ingestion to peak plasma concentration

## Practical implications encoded in algorithm

- **Direction-asymmetric cutoff timing (R7):**
  - Advance protocol (eastward): cutoff bedtime − 8 h. Caffeine in late afternoon would phase-delay against the goal.
  - Delay protocol (westward): cutoff bedtime − 6 h. Caffeine's mild phase delay is in the right direction; helps both the protocol and alertness.
- **Slow-metabolizer override (R13)**: extend cutoffs to bedtime − 10 h (advance) or bedtime − 8 h (delay).
- **No-caffeine block as positive output**: rather than just specifying a cutoff hour, the algorithm emits a block from cutoff to bedtime. This is rendered as a discrete commitment in the plan.

## Caveats

- The 200 mg / 3 h pre-bed dose is one specific point on the dose-time-response surface; smaller doses earlier in the day have smaller effects but are not zero.
- The phase effect is small in absolute terms (~40 min delay from a substantial caffeine dose). For most travelers, caffeine is dominated by its alertness effect, not its phase effect — the alertness effect is what matters operationally.
- The ~5 h half-life is the median; for slow metabolizers (10% of European-descent populations), the effective half-life is closer to 10 h.

## Algorithm dependence

R7 (caffeine windows) is built on this paper's pharmacokinetics and phase-effect findings. R13 (slow metabolizers) extends the cutoff times based on the caffeine half-life variability documented here.
