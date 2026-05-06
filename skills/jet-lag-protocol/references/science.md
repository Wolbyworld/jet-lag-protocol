# Primary literature — circadian phase-shift protocol

The skill's algorithm is grounded in this body of human-circadian research. Every rule in `algorithm-rules.md` is tagged with a citation key from this file. When updating the algorithm, update both files together.

## Phase response curves to light

**Khalsa, Jewett, Cajochen, Czeisler 2003** — *A phase response curve to single bright light pulses in human subjects.* J Physiol 549:945–952. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC2342968/))

Type-1 PRC to a 6.7 h bright-light pulse (~10,000 lux). Peak-to-trough amplitude ~5 h. Maximum delay when light is centered ~3 h before CBT_min; maximum advance when centered ~3 h after CBT_min; crossover at CBT_min. Foundational reference for *when* light shifts the clock.

**St Hilaire, Gooley, Khalsa, Kronauer, Czeisler, Lockley 2012** — *Human phase response curve to a 1 h pulse of bright white light.* J Physiol 590:3035–3045. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC3406389/))

Same shape as Khalsa 2003 but for shorter pulses. 1 h exposure: max delay −2.0 h, max advance +1.2 h. Lets the algorithm reason about realistic exposure durations rather than the lab-only 6.7 h pulse.

**Czeisler, Kronauer, Allan, Duffy, Jewett, Brown, Ronda 1989** — *Bright light induction of strong (type 0) resetting of the human circadian pacemaker.* Science 244:1328–1333.

Three consecutive days of 5 h bright-light exposures (7,000–12,000 lux) produce type-0 resetting (final phase independent of initial phase). Establishes saturation behavior at high doses.

## Light intensity — dose-response

**Zeitzer, Dijk, Kronauer, Brown, Czeisler 2000** — *Sensitivity of the human circadian pacemaker to nocturnal light: melatonin phase resetting and suppression.* J Physiol 526:695–702. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC2270041/))

Sigmoidal dose-response on log-lux. Threshold ~2 lux; half-saturation ~120 lux; saturation of phase shift ~550 lux; saturation of melatonin suppression ~200 lux. Rationale for a multi-tier light-intensity model: a continuous sigmoid is faithfully approximated by 4 discrete tiers.

## Phase response curves to melatonin

**Burgess, Revell, Eastman 2008** — *A three pulse phase response curve to three milligrams of melatonin in humans.* J Physiol 586:639–647. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC2375577/))

3.0 mg PRC, 3-pulse paradigm. Max advance ~6 h before DLMO; max delay ~5 h after DLMO. Magnitude ~1.8 h advance, 1.3 h delay over 3 days.

**Burgess, Revell, Molina, Eastman 2010** — *Human phase response curves to three days of daily melatonin: 0.5 mg vs. 3.0 mg.* J Clin Endocrinol Metab 95:3325–3331. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC2928909/))

Critical paper for dose selection. The 0.5 mg PRC is shifted ~1.5 h *later* than the 3.0 mg PRC: max advance for 0.5 mg occurs 2–4 h before DLMO (or ~5.5 h before habitual bedtime, or ~10.5 h before CBT_min); max delay occurs ~6.5 h after CBT_min or ~3.5 h after habitual wake. Shifts of comparable magnitude to 3.0 mg when timed correctly. Lower dose preferred because: shorter duration of action, no daytime grogginess, no elevated daytime melatonin.

**Burgess et al. 2010** — *Clinical implications of the melatonin phase response curve.* J Clin Endocrinol Metab 95:3325–3331 commentary. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC2928905/))

Translates the PRC into clinical timing rules. Used directly in `algorithm-rules.md` for the melatonin-time formulas.

## Caffeine

**Burke, Markwald, McHill, Chinoy, Snider, Bessman, Jung, O'Neill, Wright 2015** — *Effects of caffeine on the human circadian clock in vivo and in vitro.* Sci Transl Med 7:305ra146. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC4657156/))

200 mg caffeine 3 h before habitual bedtime delays the circadian melatonin rhythm by ~40 min (about half the magnitude of 3 h of evening bright light at ~3,000 lux). Mechanism: adenosine A1/A2A receptor antagonism / cAMP signaling. Establishes the small but real phase effect of caffeine that drives the directional asymmetry in caffeine cutoff timing (earlier cutoff for advance protocols, more permissive cutoff for delay protocols).

**Half-life and pharmacokinetics:** caffeine half-life is highly variable (median ~5 h; range 2–10 h depending on CYP1A2 genotype, smoking status, hormonal contraceptives, pregnancy). 100 mg ingested → ~12.5 mg remaining 12 h later. Drives the algorithm's "no-caffeine block from cutoff to bedtime" rule.

## DLMO estimation from self-report

**Burgess 2014** — *The dim light melatonin onset following fixed and free sleep schedules.* J Sleep Res 23:516–522. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC3841975/))

DLMO occurs ~2.5 h before habitual bedtime in adults on typical sleep schedules; ~13.5 h after habitual wake. Both estimators converge for ~7 h sleepers. Free-sleeper regression: DLMO (decimal h) = 0.80 × wake_time − 8.83.

**Kantermann, Sung, Burgess 2015** — *Comparing the Morningness-Eveningness Questionnaire and Munich ChronoType Questionnaire to the Dim Light Melatonin Onset.* J Biol Rhythms 30:449–453. ([PubMed](https://pubmed.ncbi.nlm.nih.gov/26243627/))

MEQ correlates r = −0.70 with DLMO. MSF (mid-sleep on free days, MCTQ-derived) is a slightly better DLMO predictor than MEQ. Linear regression using MSFsc + age explains ~60% of DLMO variance. Establishes that chronotype questionnaires are an acceptable substitute for direct DLMO measurement.

**Roenneberg et al. 2007** — *Epidemiology of the human circadian clock.* Sleep Med Rev 11:429–438. ([Stanford Longevity](https://longevity.stanford.edu/wp-content/uploads/sites/2/2016/04/2007SleepMedRevRoenneberg.pdf))

Population distribution of MSFsc across the day. The MCTQ chronotype questionnaire (origin paper) and the rationale for using mid-sleep on free days as the canonical chronotype marker.

## Two-process model (sleep regulation)

**Borbély 1982** — *A two process model of sleep regulation.* Hum Neurobiol 1:195–204.

Process S (homeostatic sleep pressure, builds during wake, decays during sleep) interacts with Process C (circadian propensity to be awake/asleep, peaks of alertness 2–4 h before habitual bedtime — the "wake maintenance zone"). Foundation for the "sleep-bank" pre-flight rule: if logistical constraints force an early wake, the night before must be early enough that the user falls asleep at the sleep gate (post-WMZ), not in the wake-maintenance zone. Trying to sleep in the WMZ is the canonical recipe for traveler insomnia.

## Jet lag — operational protocols

**Eastman, Burgess 2009** — *How to travel the world without jet lag.* Sleep Med Clin 4:241–255. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC2829880/))

Synthesis of pre-flight, in-flight, and post-arrival protocols for advance and delay shifts. Practical adaptation rates: with intervention, ~1.0 h/day advance, ~1.5 h/day delay. Without intervention (real-world flights), ~57 min/day advance, ~92 min/day delay. Asymmetry is grounded in human τ ≈ 24.2 h (slightly long → easier to delay than advance).

**Eastman, Gazda, Burgess, Crowley, Fogg 2005** — *Advancing circadian rhythms before eastward flight.* Sleep 28:33–44. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC1249488/))

3-day pre-flight advance protocol with morning intermittent bright light + gradually advancing sleep schedule. Demonstrates ~1 h/day advance feasibly achievable.

**Burgess, Crowley, Gazda, Fogg, Eastman 2003** — *Preflight adjustment to eastward travel: 3 days of advancing sleep with and without morning bright light.* Sleep 26:679–684. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC1262683/))

Bright light + advanced sleep schedule outperforms advanced sleep schedule alone. Justifies the algorithm's combined light + sleep-shift recommendations.

**Revell, Burgess, Gazda, Smith, Fogg, Eastman 2006** — *Advancing human circadian rhythms with afternoon melatonin and morning intermittent bright light.* J Clin Endocrinol Metab 91:54–59. ([Oxford](https://academic.oup.com/jcem/article-abstract/91/1/54/2843255))

Adding 0.5 mg afternoon melatonin to the morning-light + advanced-sleep protocol gets you "almost 1 h per day" of phase advance. Combined treatment basis.

**Roach, Sargent 2019** — *Interventions to minimize jet lag after westward and eastward flight.* Front Physiol 10:927. ([Frontiers](https://www.frontiersin.org/journals/physiology/articles/10.3389/fphys.2019.00927/full))

Recent review. Westward delay protocols: gradually delay bedtime/wake-time, evening bright light, minimize morning light, evening melatonin not strongly indicated, morning small-dose melatonin can help.

**Burgess (Penn CBTI) — *Using bright light and melatonin to reduce jet lag.*** Clinical handout. ([PDF](https://www.med.upenn.edu/cbti/assets/user-content/documents/Burgess_UsingBrightLightandMelatonintoReduceJetLag.pdf))

Practitioner-facing summary. The recovery-melatonin recommendation of "2–3 nights, taper based on sleep quality" cited from this handout.

## Sleep quality / readiness — generic wearable data

The skill consumes summary signals from any wearable or health source (Garmin, Apple Watch via HealthKit, Oura, Whoop, Withings, etc.) without depending on a specific vendor:

- 7-day mean sleep duration and continuity
- HRV trend vs personal baseline (rMSSD; lower = sympathetic dominance, often poor recovery)
- Resting heart rate trend vs personal baseline
- Recovery / readiness proxy (vendor-specific naming: body battery, readiness score, recovery %)

Modulation rationale comes from sport-science / training-load literature on autonomic recovery, not from the circadian PRC literature directly. Conservative defaults: when 7-day signals indicate poor recovery, soften the protocol. References (background, not algorithm-binding):

- **Plews, Laursen, Stanley, Kilding, Buchheit 2013** — *Training adaptation and heart rate variability.* Sports Med 43:773–781. (HRV-based training prescription; rationale for using HRV trend as a recovery proxy.)
- **Knufinke, Nieuwenhuys, Maase, Moen, Geurts, Coenen, Kompier 2018** — *Effects of natural between-days variation in sleep on elite athletes' psychomotor vigilance and sport-specific measures of performance.* J Sports Sci Med 17:515–524. (Sleep-quality variation effects on next-day performance; informs how seriously to weight a single bad night.)
