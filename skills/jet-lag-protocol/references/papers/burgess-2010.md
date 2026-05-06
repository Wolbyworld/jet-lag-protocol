# Burgess, Revell, Molina, Eastman 2010

*Human phase response curves to three days of daily melatonin: 0.5 mg vs. 3.0 mg.* J Clin Endocrinol Metab 95(7):3325–3331. PMC2928909.

**The single most important paper for the algorithm's melatonin rules (R6).**

## Methods (one line)
Two 5-day laboratory sessions per subject, each preceded by 7–9 days of fixed sleep. Each session: phase assessment (DLMO) → 3 days of ultradian dim-light/dark cycle with daily melatonin pulses → second phase assessment. Two arms: 0.5 mg vs. 3.0 mg.

## Numbers used by the algorithm (0.5 mg PRC)

### Maximum advance
- Magnitude: ~+1.5 h over 3 days
- **Timing**: 2–4 h before DLMO
- Equivalents:
  - ~5.5 h before habitual bedtime
  - ~10.5 h before CBT_min
- For DLMO ≈ 21:00, optimal dose at 17:00–19:00

### Maximum delay
- Magnitude: ~−1.0 h over 3 days
- **Timing**: 6.5 h after CBT_min
- Equivalents:
  - ~3.5 h after habitual wake time
  - For CBT_min ≈ 04:00, wake ≈ 06:00, optimal dose at ~10:30

### Crossover
- ~1.5 h before habitual bedtime (no shift)

## 0.5 mg vs 3.0 mg

The 0.5 mg PRC is shifted ~1.5 h *later* than the 3.0 mg PRC. This is counterintuitive but mechanistically clean: lower dose has shorter half-life (30–60 min vs ~2 h for 3 mg), so the "effective dose window" at the SCN is narrower and centered later.

When timed correctly, 0.5 mg produces shifts equivalent to or larger than 3.0 mg. The smaller dose is preferred because:
- No elevated daytime melatonin
- No morning grogginess
- Less inter-individual variability in response

## Practical implications encoded in algorithm

The "key dose" of the eastward return-flight protocol — taken shortly after takeoff, just before the in-flight sleep window — sits at approximately 3 h before the user's currently-advancing DLMO. For a Madrid-adapted user about to fly back from California:
- DLMO baseline (SF-adapted): 21:30 PDT
- Dose at takeoff + 1 h ≈ 18:30 PDT
- That's 3 h before SF-adapted DLMO → right at the 0.5 mg advance peak

The dose doubles as a soporific to anchor the in-flight sleep block.

## Caveats

- Three-day cumulative shift; single-dose magnitude is smaller (~⅓).
- All subjects entrained on 8 h sleep; users with shorter or longer sleep may have shifted phase angles between DLMO and bedtime.
- Inter-individual response varies. Some users get strong soporific effect with weak phase shift, others vice versa.

## Algorithm dependence

R6 is built directly on this paper. The pre-flight afternoon dose, the early-in-flight key dose, and the recovery taper all use the timing rules above.
