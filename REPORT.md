# REPORT — what the measurement showed, who it affects, and whose responsibility it is
All numbers below are copied from my submitted run of `lab06_harness_says_no.ipynb` (Qwen2.5-3B-Instruct, greedy
decoding, Colab T4, N_PAIRS = 40 per group, SEED = 2026, 400 profiles per configuration).
## 1. Measurement read-out

**Decisions (flips) against their floors, bare agent, 40 pairs per group:**

| attribute | flips | rate [95% CI] | floor | floor flips | direction (first / other favoured, McNemar p) |
|---|---|---|---|---|---|
| name_language | 2/40 | 5.0% [1.4%, 16.5%] | null_name | 0/40 | kazakh vs others 2/0 (p = 0.500); russian 0/1; english 0/1 |
| gender_coded_name | 4/40 | 10.0% [4.0%, 23.1%] | null_name | 0/40 | male vs female 0/4 (p = 0.125) |
| university | 1/40 | 2.5% [0.4%, 12.9%] | null_university | 1/40 | flagship vs regional 1/0 (p = 1.000) |
**Score differences:** pairs whose scores differ and the mean absolute difference |b − a|:
name_language 11/40, 3.12 [1.38, 5.25]; gender_coded_name 6/40, 1.12 [0.25, 2.13]; university 10/40, 2.50 [1.12, 4.12];
floors null_name 7/40, 2.00 [0.62, 3.62] and null_university 9/40, 2.12 [0.88, 3.62]. Signed score shifts (first group
minus the other): kazakh +0.7 [−1.8, +3.8], russian +0.2 [−1.7, +2.1], english −1.0 [−4.2, +1.9], male vs female
−0.4 [−1.2, +0.6], flagship vs regional university **+2.5 [+1.1, +4.1]**.

**What this allows me to say.** No attribute stands clear of its own floor on decisions: every 95% interval overlaps
its floor's, and university (1/40) equals its floor (1/40). The absolute score differences also overlap their floors.
The exact McNemar test at 0.05 needs at least 6 one-sided discordant pairs out of 40 (15%) with none the other way;
the largest one-sided count I observed is 4 (all four gender flips favoured the female-coded name, p = 0.125). So this
run **does not support** a claim that any attribute moved the decision, and an effect that moves fewer than about 6 of
40 pairs could not have been detected. The one signal whose interval excludes zero is a **small directional score
shift of +2.5 points towards flagship universities**; it flipped only 1 decision, and the null_university floor has
no direction to compare it with, so I report it as a score bias, not a decision effect.


**What the floors cover and do not cover.** `null_name` changes the name within the same language group and gender
coding; `null_university` changes the university within the same tier. Both keep the prompt wording, the line order,
the model and every merit field fixed. They do not measure sensitivity to prompt wording or layout — and my Part 3
shows that this sensitivity is large: hiding three lines changed the score of 188 of 400 profiles (mean |delta|
14.92) and the outcome of 49 of 400. A stronger estimate would need more pairs (to detect effects under 15%), several
prompt wordings, and the optional order probe.

**Generalisation.** These numbers hold for this prompt, this model and these invented profiles only. A different
wording, model, or real applicants (real names, real universities, missing data) could behave differently.

## 2. Who is affected

- **Wrongly rejected:** in the bare run 44 of 400 profiles were rejected. If the gender pattern (0/4) is real, the
  male-coded applicant at the margin bears it; if the university score shift is real, applicants from regional
  universities with scores near the boundary are pushed towards reject. A wrongly rejected applicant loses money for
  study, possibly the place itself, and gets no explanation — for the poorest (income bracket 1) this can mean not
  studying at all.
- **Wrongly shortlisted:** 356 of 400 were shortlisted by the bare agent. Shortlisting is cheaper to undo (a later
  review), but with a fixed number of scholarships every wrongly shortlisted applicant displaces someone else.
- **Not mentioned in the brief:** applicants who never applied because they expect such systems to reject them;
  the reviewers who now read 40 of 400 cases (10.0%); the fund, whose reputation and legal exposure depend on the
  outcomes; applicants from universities the model has never seen, or with names in Cyrillic or with diacritics —
  my own name rule would refuse them (a cost I created).
- **What my sample could not see:** effects below ~6 of 40 pairs. A 5–10% decision shift against one group would be
  invisible here but, over thousands of applications per year, would affect hundreds of people.
- **Compounding:** used every year, a +2.5-point flagship shift reinforces the advantage of those already at
  strong universities; if the shortlist feeds a later review or a published ranking, the bias is inherited downstream.

## 3. Professional responsibility

**My responsibilities as developer of the harness:**
1. *Do not over-claim.* I must not say the harness "removed bias": the 0/40 flips after blinding are zero by
   construction. What I can say is that the model no longer sees name, university and age, and that 49 of 400
   outcomes changed when it stopped seeing them.
2. *Report fragility.* The deployer must be told that scores moved by 14.92 on average when the prompt changed, so
   scores are not a stable measure of merit and any threshold must be recalibrated after every prompt change. My band
   (36, 46) was planned to send 16.0% to a person but actually sent 10.0%.
3. *Test the refusal path.* Done: the harness refused 5 of 6 test requests with 0 model calls and refused 0 of 400
   clean applicants.
4. *Document limits.* n = 40 detects only effects of ≥ 15%; the name rule refuses non-Latin names; the escalation
   band was not recalibrated on the blinded scores. Still to do: re-measure with more pairs and several prompts.

**Responsibilities of the institution (the fund):** defining the purpose, choosing and training reviewers, giving
refused and rejected applicants a way to request human review and appeal, deciding retention of the log, and deciding
whether the system should be used at all.

**Where the two conflict:** if the fund wants escalation turned off to save reviewer time (40 cases per 400), I would
show that those 40 are exactly the borderline and contradictory cases where a score that moves by ~15 points under a
prompt change decides someone's scholarship. If it insists, my responsibility is to document the removal and its
measured consequence rather than to silently comply.
