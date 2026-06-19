# Pre-registration: Upload Frequency vs. Views

> The analysis plan, fixed and publicly tagged (`003-v1-preregistration`) before any query relating
> upload frequency to views. The tagged commit is the audit trail. Deviations after this point are
> allowed but logged in Transparent Changes at the bottom.

**Status:** DRAFT (becomes PRE-REGISTERED on the tagged commit)
**Design:** [design-spec.md](./design-spec.md)

> **Supersedes a withdrawn cross-sectional plan (tag `003-v0-preregistration`).** That earlier plan
> treated upload frequency as a channel-constant cross-sectional predictor and therefore could not use
> a within-channel fixed-effects defense. We never queried the frequency↔VPD relationship under it.
> This v1 plan adds the time index that makes cadence vary *within* a channel, recovering the
> fixed-effects design. No outcome relationship has been examined under either plan; the v1 tag is the
> line in the sand.

## Prior exposure

Upload frequency is a public, timestamp-derived quantity; we have not run any frequency↔VPD analysis
on this cohort and have not looked at the outcome distribution. We hold the folk prior ("posting more
is supposed to help") and a competing prior that the cross-sectional version is mostly channel
selection and reverse causation. We pre-register to stop ourselves choosing the cadence window, the
bucket, or the practical bar after seeing which makes the relationship look biggest; the sealed 20%
channel holdout, read once, is the real protection.

## Could the predictor be circular?

No. All cadence predictors are computed **only** from each video's publish date (public metadata):
upload counts and gaps. No view, VPD, watch-time, or engagement quantity enters their computation. Untrained,
metadata-derived (protocol leakage case 1: not circular). The genuine threats are **confounding** and
**reverse causality**, handled by fixed effects + lag + disclosure, not by a leakage gate.

## The estimand, the ladder, and the honest ceiling

This study runs two designs and leads with the stronger one.

- **Design A (cross-channel benchmark, companion):** among channels of similar size, do the ones that
  post more have higher median VPD? Weakly identified (size + unobserved channel traits + reverse
  causality), reported as the quotable benchmark.
- **Design B (within-channel over time, HEADLINE):** within one channel, do videos published when the
  channel was posting more (vs its own baseline) earn more views? Channel fixed effects absorb every
  stable channel trait; calendar-quarter fixed effects absorb era; the lag breaks contemporaneous
  reverse causation.

**The A-vs-B gap is itself a primary finding.** A large A premium with a small B effect means the
cross-sectional premium was between-channel selection, not an effect of posting more.

**Ceiling, strictly associational.** B's lagged form rules out contemporaneous reverse causation and
the mechanical leak by time order. It cannot rule out **persistent within-channel momentum** (a good
year drives both more posting and better performance). We report the contamination ladder
(cross-sectional → contemporaneous within-channel → lagged within-channel) and use "associated with,"
never causal verbs. Reverse causality is the named headline limitation.

## Hypotheses

- **H1 (primary, directional, Design B):** Within a channel and net of platform-wide era, videos
  published during higher local upload cadence (more uploads in the 60-day window leading up to them)
  have **higher** `ln(VPD)`. Registered positive because that is the "post more, grow faster" claim
  under test; our private prior leans weak/null after the lag and momentum controls. The holdout
  adjudicates.
- **H2 (secondary, Design B):** Higher local cadence is associated with a higher **per-video
  probability** that a video is a breakout-for-its-channel (above its channel's own 90th VPD
  percentile), odds-per-shot, not breakout count.
- **H3 (companion, Design A):** Among channels of similar size, more frequent channels have higher
  median VPD; reported with the explicit caveat that it is between-channel and confounded.
- **Exploratory (hypothesis-generating, cannot veto):** whether B differs by subscriber band; the
  business/other split; the cadence-window-width robustness (30/90 day); first-difference convergence;
  the recent-VPD-control (momentum) leg; duration covariate.

## Locked inclusion constants

Fixed here; none may be re-chosen after the holdout is touched.

- `MIN_VIDEOS_PER_CHANNEL = 20`, qualifying videos per channel (cohort).
- `MIN_AGE_DAYS = 90`, `MAX_AGE_YEARS = 5`, video publish window (90 days removes early-life view
  volatility before any video enters; 5-year cap bounds catalog age).
- `VIEW_FLOOR = 10`, per-video minimum views.
- Video filter: long-form (regular upload, not a Short, by the platform's video-type label), public,
  and organic (not flagged primarily ad-driven by our traffic classifier).
- Channel filter: a subscriber count present (100% of cohort) and at least 1.
- **`CADENCE_WINDOW_DAYS = 60`**, the rolling window for B's local-cadence predictor. Chosen
  outcome-blind from the cadence distribution (median channel posts every ~6 days → ~10 uploads per
  60-day window). 30-day and 90-day windows are robustness legs; 60 is the committed headline width.
- **`AGE_BAND = 90–365 days at capture`**, the tight-age-band leg's locked window (the primary clock
  defense; §outcome). `TURN_MARGIN = 8%`, monotonicity-veto turning-point margin.
- **Design B identifying subset:** `MIN_ACTIVE_QUARTERS = 8` quarters with ≥1 qualifying upload, AND
  within-channel cadence variation ≥ 2× (max-active-quarter vs min-active-quarter upload count). Of
  ~23K cohort channels, **13,509** clear ≥8 active quarters and **13,370** of those also vary ≥2×
  (predictor-side feasibility, already run). B is scoped to "channels that varied their cadence over
  time."
- **Subscriber bands (Design A control only), round cutoffs, NOT data-tuned:** Small 1–9,999 /
  Mid 10,000–99,999 / Large 100,000–999,999 / Mega 1,000,000+.

## Predictor definitions (locked)

All pure functions of each video's publish date.

- **Design B, local cadence:** for each video, `window_uploads` = the channel's qualifying uploads in
  the **60-day window ending just before that video's publish date** (cadence *leading up to* the
  video, excluding it, the lag). Modeled as `ln(1 + window_uploads)`. **Note the `+1` offset distorts
  the "per doubling" unit most at low upload counts (a doubling of `1+u` near u=1 is a smaller
  real-cadence step than near u=30); with median ~10 uploads/window this is minor in the bulk, and the
  decile curve, not the single slope, is the honest picture of shape.**
- **Left-censoring rule (locked).** A video whose 60-day pre-window predates the channel's first
  observed qualifying upload has a truncated history. **Rule:** such videos are **kept**, with
  `window_uploads` counted over the available (truncated) history AND a `truncated_window` flag
  covariate included in the model. We do not drop them (dropping launch-phase videos is itself
  selective, launches are high-cadence/high-novelty). A robustness leg drops them instead and confirms
  the slope is stable.
- **Design A, median inter-upload interval `upload_interval_days`** (modeled `ln`); reported as
  uploads/week = `7 / upload_interval_days`. Companion throughput metric `uploads_per_week` (count /
  active-span-weeks) reported as a *different* (dormancy-inclusive) lens, not an agreeing cross-check.
- **Hiatus handling:** `HIATUS_DAYS = 90`. Report per-band the fraction of channels with a 90+ day gap;
  a robustness leg excludes hiatus channels.

## Outcome definitions (locked)

- Per video: `vpd = latest_views / age_days`, `age_days = capture_date − publish_date`.
- **Design B outcome:** the video's `ln(vpd)`.
- **Design A outcome:** the channel's median over its videos of `ln(vpd)` (one value per channel).
- **Breakout (H2):** a video is a breakout-for-its-channel iff its **age-residualized** `ln(vpd)`
  exceeds its own channel's 90th percentile of that residual (raw-VPD percentile would be clock-
  contaminated, full definition in the H2 section).

**The measurement-clock control (load-bearing for B).** VPD is read at one recent capture (single
clock, confirmed, though "single" is a ~45-day-wide capture window, disclosed: two same-publish-date
videos captured 40 days apart differ in age, adding noise to `age_days`). Older videos read lower-VPD
mechanically (views/age with decayed tails). Day-90 VPD is **infeasible** (median 1 snapshot/video;
~0.5% have a day-90 snapshot, confirmed), so B controls the clock with: (1) an `ln(age_days)`
covariate; (2) calendar-quarter fixed effects; (3) a **load-bearing tight-age-band leg**; (4) the
≥90-day floor.

**The residual clock risk, named (per statistician review).** After channel + quarter demeaning,
`ln(age_days)` is nearly collinear with the quarter fixed effects (age ≈ const − publish_date under a
fixed capture clock, and the quarter FE is a step function of publish_date), so the quarter FE absorbs
almost all of the age gradient and the `ln(age)` covariate is weakly identified. The danger: quarters
are coarse 90-day steps, age is continuous, and at the steep early VPD-decay a within-quarter 90-day
age spread is a large VPD difference, so a within-quarter cadence-vs-age correlation (a burst at
quarter-start vs stragglers at quarter-end) could leak age into the cadence coefficient. **This is why
the tight-age-band leg is the primary clock defense, not the covariate:** restricting to a narrow
age-at-capture band removes the gradient *by design*. **Locked: `AGE_BAND = videos aged 90–365 days at
capture`** for the leg, AND a registered check that **≥ 1,000 channels retain ≥2× cadence variation
inside the band** (a narrow age band also narrows the publish-date window, which can shrink
within-channel cadence variation, the tension is checked against a numeric floor; below it the
age-band leg is reported as underpowered, not as a clean pass). The artifact
is disclosed as a named B limitation regardless; (1)–(4) bound it rather than eliminate it.

**The window-density / `n` coupling (registered).** A higher-cadence window mechanically contains more
uploads. This is the predictor, intended. The related noise coupling, channels/periods with more
videos have more-precisely-estimated quantities, is bounded by working at the video level (every
video is one row with its own VPD, no per-period median to grow noisy) and by an `n`-robustness leg.

## Primary analysis, Design B (the headline)

Computed on the **20% channel holdout** (a deterministic hash of the channel identifier, one fixed
bucket in five) **once**, code fixed
in advance; the exploration-set value is the confirming companion.

- **Model:** `linearmodels.PanelOLS`, entity = channel (`entity_effects=True`), time = calendar
  quarter (`time_effects=True`), `cov_type='clustered', cluster_entity=True`.
  `ln(vpd) ~ ln(1 + window_uploads_lagged) + ln(age_days)` on the B identifying subset.
- **Headline coefficient:** the within-channel, era-netted slope of `ln(vpd)` on `ln(1 + window
  cadence)`, back-transformed to a **% change in VPD per doubling of local cadence**
  (`100·(2^β − 1)`), with a channel-clustered 95% CI.
- **The contamination ladder, all three reported:**
  1. **Cross-sectional A** (companion): channel-level, median `ln(vpd)` on `ln(interval)`, within
     subscriber band, channel bootstrap. The most-confounded rung.
  2. **Contemporaneous B** (labeled upper bound): same panel model but cadence of the video's *own*
     window (not lagged), still carries contemporaneous momentum.
  3. **Lagged B** (HEADLINE): cadence of the window *before* the video.
  The report leads with the lagged B number and shows the ladder so the confounding shrinks visibly.

## Analyses fixed in advance (can change or veto the headline)

1. **The lagged-B slope (headline)**, above.
2. **Tight-age-band gate (load-bearing, not routine).** Re-run the headline on videos in the locked
   `AGE_BAND` (90–365 days at capture). This is the primary clock defense (removes the age gradient by
   design, not via the quarter-collinear covariate). Includes the registered check that enough
   ≥2×-variation channels survive inside the band to identify B. If the headline only holds outside the
   band, the result is reported as clock-confounded, not a cadence effect.
3. **Channel-weighted companion (per-channel-then-aggregate, the house's preferred tier).** `PanelOLS`
   row-weights toward high-volume (so large/prolific) channels. We therefore also compute the
   within-channel slope **per channel, then report the channel-median + channel-bootstrap CI**, giving
   each channel equal weight. If row-weighted and channel-weighted agree, weighting is a non-issue; if
   they diverge, the row-weighted headline is a big-channel statement and is labeled as such.
4. **Burst-replication / episode-robustness leg (channel-quarter collapsed panel).** Adjacent videos
   share ~55/60 window days, so a dense upload sprint contributes many near-identical high-cadence rows
  , channel clustering fixes the SE but not the within-channel *weighting* (one cadence episode gets
   many votes). Confirmatory check: collapse to one row per channel-quarter (outcome = quarter's median
   `ln(VPD)`, predictor = quarter cadence), channel + quarter FE. Each cadence episode then gets one
   vote per quarter regardless of video count. Agreement with the video-level headline kills the
   burst-weighting concern; divergence means the video-level number is a burst artifact.
5. **Identifying-subset honesty.** Report the headline on the full B panel AND on the ≥2×-variation
   subset; the published estimand is the variation subset. (Selecting on a *noisy* windowed-cadence
   variance mildly over-samples upward-noisy channels, a small selection-on-variance bias, diagnosed by
   this full-vs-subset comparison, disclosed in prose.)
6. **Monotonicity veto (magnitude + replication, NOT significance).** A single slope can hide a cadence
   sweet spot (an inverted-U: too little = no momentum, too much = quality/burnout). Bin videos by
   window cadence into deciles; the veto fires **only if** an interior decile sits above/below **both**
   neighbors by a margin exceeding **`TURN_MARGIN = 8%` in VPD terms** AND that turning point reproduces
   in **two independent halves of the channel set, split deterministically by a hash of the channel
   identifier** (locked, not analyst-chosen). On a trigger the headline is "shape, see the curve." (The 8%
   absolute margin + replication is the multiplicity control at large N, where per-point CIs are tight;
   we gate on the point estimate, not a p-value.)

## Practical-significance threshold (declared now, unit spelled out)

At N in the millions of videos / ~13K channels, every effect is "significant," so the threshold is the
study.

- **MEANINGFUL only if the within-channel cadence slope reaches ±15% VPD per doubling of local
  cadence**, AND its channel-clustered 95% CI excludes 0. This is a **per-doubling rate** (the within-
  channel dose unit), distinct from any cross-sectional contrast unit; the unit is spelled out so it is
  not read as a one-step difference.
- **Why ±15% per doubling:** doubling cadence is a large, expensive change for a creator (their time).
  A within-channel design holds the creator fixed, so the bar can be a bit lower than a cross-sectional
  most-vs-least contrast, but still high enough that the advice would be worth the cost. A typical
  channel's cadence spans ~2–3 doublings across its life, so ±15%/doubling implies a meaningful
  cumulative swing.

**The three-state verdict, defined now.** A common error returns "inconclusive" whenever the CI
includes 0, conflating a precise null with a power failure. Let `H` = the slope's CI half-width:
- **MEANINGFUL:** point reaches ±15%/doubling AND CI excludes 0.
- **PRECISE NULL / TRIVIAL:** the entire CI sits inside ±15% (whether or not it includes 0), a real,
  tight, small-or-zero effect. **A finding, not a failure** ("within a channel, posting more does not
  meaningfully move VPD" is a publishable benchmark).
- **INCONCLUSIVE:** ±15% sits inside the CI (`H ≳ 15%`), the data cannot separate trivial from
  meaningful.

**Pre-holdout power check.** Compute `H` on the exploration set against ±15% before the one-shot
holdout read; if the holdout would land INCONCLUSIVE by construction, record the study as underpowered
up front. A reporting commitment, not a license to re-spec.

## Secondary analysis, Design B breakout odds (H2)

- **Model:** video-level **linear-probability** FE (channel-clustered SEs) as the H2 headline:
  `is_breakout_for_channel ~ ln(1 + window_uploads_lagged) + ln(age_days)`, channel + quarter effects.
  LPM (not FE-logistic) is the headline because a fixed-effects logistic has the incidental-parameters
  problem (channel dummies are nuisance parameters → inconsistent MLE); if a logistic version is shown
  it uses **conditional logit** (conditions the channel FE out exactly), reported as a companion.
- **Breakout threshold defined on age-adjusted VPD (registered fix).** Breakout is **not** the channel's
  90th percentile of *raw* `ln(VPD)`, young videos have mechanically higher VPD, so raw-VPD breakouts
  are disproportionately the channel's youngest videos, a clock path straight into the outcome. Instead:
  residualize `ln(VPD)` on `ln(age_days)` (and quarter) **first**, then a video is a breakout iff its
  *residual* exceeds its channel's own 90th percentile of the residual. This takes the clock out of the
  outcome *definition*, not just the regressor list.
- **The lottery-ticket guard (registered):** the outcome is **per-video probability** (odds-per-shot),
  so more uploads cannot inflate it mechanically. If a breakout-count version is shown, it carries a
  `log(uploads)` offset (rate model) and the explicit "more uploads → more total breakouts mechanically"
  disclosure.
- **What H2 can and cannot say (framing, registered).** Because breakout = a channel's own top decile,
  the within-channel breakout rate is fixed at ~10% **by construction**. H2 therefore measures whether
  high-cadence videos **disproportionately fill** a channel's fixed top-decile budget, NOT whether
  posting more *creates* more breakouts (that is the lottery-ticket claim H2 explicitly refutes). The
  report states this so a positive H2 is not misread as the count claim.

## Companion analysis, Design A (H3)

Within each subscriber band, median `ln(vpd)` on `ln(upload_interval_days)` (channel bootstrap, 10,000
resamples, seed 0), plus a most-vs-least-frequent (Q5-vs-Q1) median-VPD % contrast on size-residuals
with a <50-channel min-cell rule. Reported as the quotable benchmark and as the top rung of the
contamination ladder, explicitly between-channel and confounded. Niche is unavailable (data-reality
note in the design spec) → residual confounder for A only; business/other is a coarse robustness split.

## Outlier handling (decided in advance)

- B works in `ln(vpd)` at the video level; the per-channel 90th-pct breakout outcome is rank-based and
  robust. Across the panel we run the house Three-Analysis Rule on the headline slope: (i) the log
  primary, (ii) drop the top/bottom 1% of videos by `ln(vpd)`, (iii) winsorize `ln(vpd)` at 1/99 pct.
  Convergence required; divergence flagged. Robustness legs, not vetoes.
- We do not trim the cadence predictor; `ln(1 + window_uploads)` and the 60-day window bound burst
  leverage. We report the window-cadence distribution.

## Everything else is exploratory

FDR-corrected at q = 0.10 within family, logged in the lab run registry, cannot veto:
per-subscriber-band B interaction; business/other split; 30/90-day cadence-window robustness;
first-difference convergence; duration covariate; hiatus-excluded leg; left-censoring drop-vs-keep leg.

**Recent-VPD-control (momentum) leg, confirmatory-with-caveat, reported next to the headline.** This
is the *only* direct test of the named headline limitation (persistent momentum), so it is not buried
with the exploratory legs: add the channel's VPD in the prior 1–2 windows as controls and report
whether the cadence slope survives. **Caveat that keeps it from being a veto:** a lagged dependent
variable under fixed effects carries finite-sample (Nickell) bias of O(1/T); our panels are long
(≥8 quarters, usually more) so the bias is small, but a null here is a *probe, not a proof* of no
momentum. Reported beside the headline with this caveat stated.

## N-aware stance

> With ~1.87M videos clustered in ~23K channels (B's identifying subset ~13.4K), we de-emphasize
> p-values, near-zero for any non-zero effect at this scale, and report effect sizes (within-channel
> % VPD per doubling of cadence; per-video breakout-probability change), channel-clustered confidence
> intervals, and a practical-significance threshold (±15% per doubling) declared before analysis. Every
> relationship is correlational; the data is observational and nothing was randomized. The within-
> channel design removes stable confounders and the lag removes contemporaneous reverse causation, but
> persistent within-channel momentum cannot be excluded and is disclosed as the central limitation.

## Data release

De-identified per protocol: HMAC-hashed `channel_hash` + `video_hash` (salt unpublished); per-row
columns (`window_cadence`, `upload_interval_days`, `uploads_per_week`, `subscribers`,
`subscriber_band`, `content_category`, `age_days`, `ln_vpd`, `publish_quarter`, `is_breakout`); binned
aggregates; replication notebook. No raw ids/titles/thumbnails. "Methods reproduction, not measurement
verification." Re-verify YouTube API ToS on the publish push.

## Statistician review (mandatory gate)

The `data-scientist` agent reviewed the v1 plan. First pass: "NOT TAGGABLE", the architecture (C,
B-led) was sound but the panel design introduced gaps that had to be registered before the tag. All
folded in below; a confirmation pass is required to clear the gate.

- **Left-censoring rule (CLOSED):** early videos lacking 60 days of pre-window are kept with a
  truncated history + `truncated_window` flag; drop-instead leg registered.
- **Channel-weighted companion (CLOSED):** per-channel-then-aggregate (channel-median slope +
  channel-bootstrap CI) alongside the row-weighted PanelOLS headline; divergence ⇒ "big-channel
  statement," labeled.
- **Burst-replication leg (CLOSED):** channel-quarter collapsed panel as a confirmatory check that the
  video-level slope is not a burst-weighting artifact.
- **Breakout threshold age-adjusted (CLOSED):** breakout = above the channel's 90th percentile of
  *age-residualized* `ln(VPD)`, removing the clock from the outcome definition; H2 = LPM headline
  (FE-logistic's incidental-parameters problem noted, conditional-logit companion).
- **Age-band locked + identification check (CLOSED):** `AGE_BAND = 90–365 days`; registered check that
  ≥2×-variation channels survive inside the band.
- **Disclosures (CLOSED):** 45-day capture-window dispersion; `+1`-offset distortion of the doubling
  unit; selection-on-noisy-variance; H2 measures redistribution of a fixed 10% budget, not breakout
  creation. Monotonicity split-half locked to `md5 % 2`. Momentum leg promoted to
  confirmatory-with-caveat.

## Transparent Changes (deviations after the tagged commit)

**2026-06-15, Monotonicity-veto trigger refined (on exploration, before the holdout read).**
The registered veto fired only on "an interior decile above/below **both immediate** neighbors by
`TURN_MARGIN`." On the exploration set the cadence→VPD curve is a **broad rise-plateau-collapse** (VPD
climbs to a plateau around decile 6, then collapses at the highest-cadence decile 10). That shape is
plainly non-monotonic, but the literal "both immediate neighbors" rule does not fire on it because the
plateau means no single decile beats *both* its immediate neighbors by the margin. The trigger was too
local for the actual shape. **Refinement (`nonmonotonic()` in `analysis_lib.py`):** the veto now fires
on EITHER (a) an **interior maximum**, the peak sits at an interior decile and exceeds both the first
and last deciles by > `TURN_MARGIN` (robust to a flat top), OR (b) an **end-collapse**, the last
decile falls below the peak by > `TURN_MARGIN` and below at least one earlier decile. Still gated on
magnitude (not a p-value) and still requires **replication in both `md5 % 2` channel-halves**.
Rationale: this captures the registered *intent* (don't report a single slope when the relationship is
non-monotonic) for the shape the data actually shows; it was decided on exploration before the sealed
holdout was read. The `TURN_MARGIN = 8%` magnitude bar and the two-half replication requirement are
unchanged.

**2026-06-15, Tight-age-band gate identification rule (bugfix, on exploration).** The age-band leg
restricts videos to 90–365 days at capture. The registered identifying filter (`≥ 8 active quarters`)
cannot be satisfied inside a ≤1-year age window by construction, so the gate initially reported 0
qualifying channels. Corrected so the in-band identification requirement is **within-band cadence
variation** (a channel's in-band videos still span ≥ `MIN_CADENCE_VARIATION`× window cadence, with ≥5
in-band videos), consistent with the design-spec note that a narrow age band narrows the publish-date
window. With the fix, 5,052 channels qualify in-band (clears `AGE_BAND_MIN_CHANNELS = 1,000`); the
band slope is reported.
