# Khalsa, Jewett, Cajochen, Czeisler 2003

*A phase response curve to single bright light pulses in human subjects.* J Physiol 549(3):945–952. PMC2342968.

## Methods (one line)
21 healthy entrained subjects, pre- and post-stimulus constant routines in dim light (~2–7 lux), single 6.7 h bright light pulse alternating fixed-gaze (~10,000 lux) and free-gaze (~5,000–9,000 lux). Phase shift = difference in melatonin midpoint phase between pre- and post-stimulus constant routines.

## Numbers used by the algorithm

- **PRC type**: type 1 (continuous, smooth crossover at CBT_min)
- **Peak-to-trough amplitude**: ~5.0 hours
- **Maximum delay**: ~−2.0 h, occurs when light pulse is centered ~3 h *before* CBT_min
- **Maximum advance**: ~+2.0 h, occurs when light pulse is centered ~3 h *after* CBT_min
- **Crossover (zero shift)**: at CBT_min
- **Dead zone**: subjective day, between ~2 h after wake and ~2 h before bed; phase shifts diminish gradually rather than dropping sharply to zero

## Practical implications encoded in algorithm

- CBT_min is the pivot point of the light PRC. Light *before* it delays; light *after* it advances. Used in R5 (light windows).
- The peak-advance window sits at [CBT_min + 1h, CBT_min + 6h] approximately, with strongest effect 2–4 h post-CBT_min.
- The peak-delay window sits at [CBT_min − 6h, CBT_min − 1h] approximately, with strongest effect 2–4 h pre-CBT_min.
- A 6.7 h pulse is laboratory-only; for real-world durations, reference St Hilaire 2012 (1 h pulse) for proportional scaling.

## Caveats

- The PRC was measured in highly entrained subjects under constant routine; field conditions yield smaller magnitudes.
- The dim-light reference (~7 lux) is brighter than a typical bedroom — real-world "ambient" daytime light contaminates the pre-pulse condition in field protocols. The PRC slightly underestimates field response.
- Single pulses; cumulative effects of multi-day exposures need separate citation (Czeisler 1989).

## Algorithm dependence

R4 (light tiers) and R5 (light windows) depend on this paper for shape. R3 (adaptation rate) depends on the magnitude per pulse, scaled to realistic durations.
