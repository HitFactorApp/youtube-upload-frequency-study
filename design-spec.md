# Design Spec: Upload Frequency vs. Views

> Phase 0 + Phase 1 deliverable. Written before the pre-registration and before any query that
> relates upload frequency to views. The only queries run so far are predictor-side feasibility
> checks (cohort sizes, upload-cadence distribution, snapshot/quarter coverage), never the outcome
> distribution.

## Research question (Phase 0)

**The question:** Is a channel's **upload frequency** (how often it publishes) associated with the
**views-per-day** of its videos, both *between* channels (do channels that post more out-perform
similar ones?) and, the stronger version, *within* a channel over time (when a channel posts more
than its own baseline, do its videos earn more views)?

**Why it matters.** "Post more, grow faster" is advice you hear constantly,
and one of the least carefully measured. Coaches sell upload-cadence calendars; the platform's own
"consistency helps" messaging is taken as gospel. The obvious counter-story is just as plausible:
frequency may be a proxy for channel size (big teams ship more), and success may *fund* frequency
rather than the reverse. A concrete number, with the channel held as its own control, settles a
recurring argument and is quotable whichever way it lands.

**Question-selection checklist:**
- [x] On-brand (what makes content perform; uses our organic-classified cohort at scale)
- [x] Citable (the answer is a concrete, quotable stat)
- [x] Defensible (framed strictly as a relationship; reverse causality named, not hidden)
- [x] Counter-intuitive or benchmark-filling (no within-channel upload-cadence benchmark exists publicly)
- [x] Written down before peeking at outcome distributions

## The design, in one paragraph (read this first)

Upload frequency varies in two places: **across channels** (some post more than others) and **within
a channel over time** (a channel posts weekly one year, daily the next). These give two designs that
answer subtly different questions, and this study runs both:

- **Design A, cross-channel (the quotable benchmark).** Among channels of similar size, do the ones
  that post more have higher typical views-per-day? Simple and citable, but weakly identified: it
  compares *different* channels, so channel size and every unobserved channel trait (team, budget,
  talent) leak in, and it cannot touch reverse causality.
- **Design B, within-channel over time (the rigorous core, the headline).** Within one channel,
  comparing the periods it posted more to the periods it posted less, do its videos earn more views?
  Channel fixed effects cancel **every stable channel trait**, size, niche, team, talent, baseline
  production quality, because the channel is its own control. This is the strong identification that
  a cross-sectional design cannot reach, and the time index is exactly what makes the within-channel
  fixed-effects framing (the house's preferred, most debunk-proof tier) available for a predictor that
  is otherwise channel-constant.

**The headline is B; A is the companion; the gap between them is the finding.** If A shows a large
frequency premium and B shows little, that gap *is* the story: the cross-sectional premium was
between-channel selection (bigger/better channels post more), not an effect of a channel posting more.
Presenting both, and letting the reader watch the confounding shrink from A to B, is more
debunk-proof than either number alone.

**The honest ceiling: the whole study stays associational.** B removes every *stable* confounder, and
its lagged form (below) rules out the *contemporaneous* reverse-causal direction by time order. It
cannot rule out **persistent within-channel momentum** (a channel having a good year posts more *and*
performs better, with the arrow running from success to posting). So we report a contamination
**ladder**, cross-sectional → contemporaneous within-channel → lagged within-channel, and stop short
of a causal claim. Reverse causality is named as the headline limitation, not a footnote.

## Study design (Phase 1)

### 1. Observational vs. experimental

Observational. Every finding is correlational by construction. Upload frequency is a creator behavior
bundled with unobserved time-varying factors (a hot streak, a new editor, a budget change); the
within-channel design removes everything *stable* about the channel and the lag removes the
contemporaneous reverse direction, but nothing is randomized and no causal claim is made. A true
cadence experiment, and a quasi-experiment on exogenous cadence shocks (forced breaks), are named as
future tiers (§10), scoped out here.

### 2. Unit of analysis + clustering

**Two designs, two units, one clustering rule.**

- **Design A unit: the channel** (~23K). The predictor (a channel's typical cadence) and the control
  (subscriber band) are channel-level, and the estimand is a statement about channels. Collapsing to
  one row per channel dissolves video-level pseudoreplication for A.
- **Design B unit: the video** (~1.87M). Each video's views are the outcome; its predictor is the
  channel's **local upload cadence in a rolling window around its own publish date** (§4). Working at
  the video level (rather than a per-period channel median) means the bucket width sets only how
  cadence is *measured*, not the outcome's sample size, so the per-period median-noise tradeoff that
  plagues a bucketed panel does not arise.

**Clustering (both designs): the channel is the independent unit.** Videos and channel-periods within
a channel are not independent. Every CI resamples or clusters by **channel** (channel-clustered SEs in
the B panel regression; channel bootstrap for the A contrasts), never by video. The unit getting finer
in B does not make videos independent, the cluster stays the channel.

### 3. Outcome metric and the measurement-clock problem

**Metric:** views-per-day, `VPD = latest_views / age_days`, from a single recent view capture.

- **Design A outcome:** the channel's **median `ln(VPD)`** across its qualifying videos, an
  outlier-robust summary of the typical video, one value per channel.
- **Design B outcome:** the individual video's **`ln(VPD)`**, age-controlled (below).

**Skew handling:** medians and log space throughout; VPD is dominated by viral outliers, so raw means
are never used.

**The measurement-clock artifact (load-bearing for B, and the trap that has fooled us before).** VPD
is views over age read at one recent capture date (confirmed: 99.99% of snapshots are within the
last 45 days, a single clock). Because views accrue fast then decay, an **older video's lifetime-
averaged daily rate is mechanically lower** than a younger video's, regardless of true performance.
In A this averages out within a channel. **In B it does not**, B compares a channel's older periods
to its newer periods, and older-period videos are systematically older-at-capture, so the artifact
lines up with the very time axis B exploits and can manufacture a fake "VPD rose as cadence rose"
trend. (A naive per-period VPD series rises over time even for a channel whose true per-video
performance was flat; this is exactly the failure mode behind a prior mis-read of a single channel's
trajectory.)

**Why we cannot use day-90 VPD (the clean fix), and what we do instead.** The clean fix (measure
every video's views at a fixed age, e.g. day 90, so the clock is constant) requires per-video early-
life view history. **Confirmed infeasible for this cohort:** our snapshot history holds a median of **1**
capture per video, ~all from the last 45 days; only ~0.5% of videos have a snapshot by day 90. A
secondary capture source covers ~14% of videos with almost none early. So day-90 VPD is not reconstructable.
Instead, B controls the clock directly:
1. **An explicit `ln(age_days)` covariate** in the panel model absorbs the age→VPD gradient.
2. **Calendar-period fixed effects** (below): videos published in the same calendar window were
   published at nearly the same real-world time, so they are ~the same age at our single capture; the
   period fixed effect therefore absorbs the bulk of the age-at-capture artifact directly.
3. **A load-bearing tight-age-band leg** (restrict to videos in a narrow age-at-capture window and
   confirm the result holds), promoted from a routine robustness check to a named gate for B.
4. **The ≥90-day age floor** (cohort filter) removes early-life view volatility, so every video is
   past its settling phase before it enters.

The clock artifact is disclosed as a named B limitation regardless; (1)–(4) bound it rather than
eliminate it.

### 4. Predictor: upload cadence (locked definitions)

All predictors are pure functions of each video's publish date (public metadata); none touches the outcome.

- **Design A predictor, median inter-upload interval, `upload_interval_days`:** the median day-gap
  between a channel's consecutive qualifying uploads (robust to a single hiatus/burst). Reported on the
  page as uploads/week = `7 / upload_interval_days`; modeled as `ln(upload_interval_days)`. A throughput
  companion, `uploads_per_week` (count / active-span-weeks), is reported as a *different* lens (it folds
  dormancy into the denominator), not as an agreeing cross-check.
- **Design B predictor, local cadence in a `CADENCE_WINDOW_DAYS = 60` rolling window:** for each
  video, the number of the channel's qualifying uploads within ±30 days of that video's publish date
  (a 60-day window centered on it), modeled as `ln(1 + window_uploads)`. **The 60-day width was chosen
  outcome-blind from the cadence distribution:** the median channel posts every ~6 days, so a 60-day
  window holds ~10 uploads for a typical channel, enough to measure a stable local rate while still
  resolving multi-quarter cadence shifts. 30-day (~5 uploads) and 90-day (~15 uploads) windows are
  pre-registered robustness legs; 60 is the committed headline width regardless of which looks biggest.
- **Calendar period for the period fixed effect:** calendar **quarter** (a shared bucket across
  channels so the period FE absorbs platform-wide era drift). Note this quarter is only the FE grouping;
  cadence itself is measured on the continuous 60-day rolling window, so the analysis is not bucketed
  into quarters.

### 5. The Design B estimator (locked)

- **Primary (lagged) estimand:** regress an individual video's `ln(VPD)` on the channel's **local
  cadence in the 60-day window ending just before that video's publish date** (i.e. cadence leading
  *up to* the video, not including it), with **channel fixed effects** + **calendar-quarter fixed
  effects** + `ln(age_days)`, channel-clustered SEs (`linearmodels.PanelOLS`, entity + time effects).
  The coefficient is the within-channel, era-netted association between recent posting cadence and the
  next video's views. Lagging (cadence *before* the video) breaks the contemporaneous reverse arrow and
  the mechanical "this video did well so they posted again" path by time order.
- **What lagging does NOT fix (disclosed):** a persistent within-channel "good year" state can drive
  both high cadence and high subsequent VPD. Lagging converts simultaneous reverse causation into
  serially-correlated confounding; it does not delete it. A **corroborating leg** adds recent-VPD
  controls (the channel's VPD in the prior windows) to probe whether the cadence effect survives the
  momentum state, reported with the known finite-sample (Nickell) bias caveat, as corroboration not
  headline.
- **The contamination ladder, all reported:** (i) cross-sectional A; (ii) contemporaneous within-
  channel B (cadence of the video's own window, labeled the most-contaminated "upper bound"); (iii)
  lagged within-channel B (headline). The reader watches the confounding shrink rung by rung.
- **First-difference** version as a convergence robustness leg (FE and FD agree → robust; diverge →
  trending-unobservables flagged).

### 6. The identifying subset (B's central honesty disclosure)

Only channels whose cadence **actually varies over time** contribute identifying information to B; a
channel that posts at a dead-constant rate has ~zero within-channel cadence variation and drops out of
the fixed-effects estimate. If unreported, B silently estimates off the subset that *changed* cadence,
which is non-random (launches, going-pro, burnout, rebrands).

- **Locked inclusion:** a channel contributes to B only with **`MIN_ACTIVE_QUARTERS = 8`** quarters
  holding ≥1 qualifying upload (a real time series), AND within-channel cadence varying by **≥ 2×**
  (max-active-quarter vs min-active-quarter upload count).
- **Reported as a headline diagnostic:** of ~23K channels, the fraction clearing these. **Predictor-
  side feasibility already run:** **13,509** channels have ≥8 active quarters and **13,370** of those
  also vary cadence ≥2×, so B rests on ~13.4K channels, a real population, not a sliver. The published
  B sentence is scoped to **"among channels that varied their upload cadence over time."**

### 7. Confounders, DAG

Headline (B) relationship: **within-channel change in cadence → that channel's later video VPD.**

```
   (stable channel traits: size, niche, team, talent, baseline quality)
                          │  ── ALL absorbed by channel fixed effects (B) ──
                          ▼
   platform-wide era ──► VPD ◄── video age-at-capture (clock artifact)
        │ (calendar-quarter FE)        ▲   (ln(age) covariate + quarter FE + age-band leg)
        ▼                              │
   LOCAL CADENCE (60d window) ──(the arrow B estimates, lagged)──┘
        ▲                              │
        └── persistent momentum: a good year drives both ──► (NOT removable; disclosed)
```

| Covariate | Role | Handling |
|---|---|---|
| **All stable channel traits** (size, niche, team, talent, production quality) | confounders | **Absorbed by channel fixed effects (B).** This is what B buys and A cannot. |
| **Platform-wide era / temporal drift** | confounder | Absorbed by **calendar-quarter fixed effects** (B); a robustness leg in A. |
| **Video age-at-capture (clock artifact)** | artifact | `ln(age_days)` covariate + quarter FE + load-bearing tight-age-band leg (§3). |
| **Persistent within-channel momentum** (a good year → more posting *and* more views) | residual confounder / reverse direction | **Not removable.** Lag breaks the contemporaneous arrow; recent-VPD-control leg probes it; named as the headline limitation. |
| Channel size (subscribers) | confounder for **A only** | A: stratify into subscriber bands. B: absorbed by channel FE, so not needed. |
| Niche | confounder for **A only** | Unavailable in data (§ data-reality note); a residual confounder for A. **Irrelevant to B** (channel FE absorb it), which is a further reason B is the headline. |
| Video length / duration | weak proxy | Sensitivity covariate (window/video median duration), with/without. |
| CTR / impressions | mediator | Not controlled (on the path). |
| Recommendation-shelf placement | collider | Never controlled. |

**The within-channel design satisfies the house directive to "lead with the within-channel
fixed-effects version"**, available here precisely because cadence varies over time. A's subscriber-
band stratification and its missing-niche control matter only for the companion benchmark, not the
headline.

**Data-reality note (niche, for Design A only).** Confirmed in our data: a per-channel niche field is
null for 98.5% of the cohort; the coarse category field has only two values (business / other); the
per-channel topic text is free-form with no shared cross-channel taxonomy. So A cannot
stratify on niche and discloses it as a residual confounder, using business/other only as a coarse
2-way robustness split. **B does not need niche**, channel fixed effects absorb it, so this data gap
does not touch the headline.

### 8. Feature provenance & leakage

All cadence predictors derive **only** from each video's publish date (public metadata): upload counts and gaps.
No view/VPD/engagement quantity enters their computation. Untrained, metadata-derived (protocol
leakage case 1: not circular). The genuine threats are confounding and reverse causality (handled by
the FE + lag + disclosure), not leakage.

### 9. Secondary outcome, does posting more raise breakout odds?

A separate, quotable question: does higher cadence raise the chance of a *breakout* video, not just
move the median? **The trap:** more uploads = more lottery tickets = more breakouts *mechanically*,
even if per-video odds are flat. We therefore measure the **per-video probability**, not the count:

- **Outlier definition (per-channel-relative, consistent with the FE design):** a video is a
  breakout-for-its-channel if its `ln(VPD)` exceeds its **own channel's 90th percentile**. This means
  "broke out relative to this channel's normal," which is what a creator means, and it is automatically
  size-neutral.
- **Estimator:** video-level outlier probability (FE logistic / linear-probability, channel-clustered
  SEs), predictor = the video's 60-day window cadence. This is **odds-per-shot**, immune to the
  lottery-ticket trap. If a count version is shown, it carries a `log(uploads)` offset (a rate model)
  and the explicit "more uploads → more total breakouts mechanically" disclosure.
- Inherits the clock handling from §3 (a young video measured at peak VPD is likelier to clear the
  threshold), via the age controls and age-band leg.

### 10. Locked holdout + future tiers

**Holdout:** a channel is in the **~20% holdout** by a deterministic hash of its identifier (one
fixed bucket in five): a
deterministic, whole-channel rule (the unit is the channel in A and the cluster in B, so a channel is
entirely in or out; a video-level split would leak channel signal across train/test). All EDA, the
window-width and band choices, and spec confirmation run on the ~80% exploration set; the B headline
and the A contrast are read off the holdout **once**, with the analysis code fixed in the pre-reg.

**Future tiers, named and scoped out (so they are not post-hoc forking paths):**
- **Quasi-experimental cadence shocks:** channels with a forced, externally-caused break (illness,
  demonetization, platform strike) that resumed, plausibly exogenous to performance, breaking the
  momentum confound B cannot. A separate data-construction project.
- **True cadence experiment.** Out of scope for an observational study.

## Generalization

The frame is English-language business/creator-economy YouTube channels from our own funnel, skewed
mid-to-large and toward *frequent* posters (median inter-upload gap ~6 days), not a random sample of
YouTube. B's estimand is further scoped to channels that varied their cadence over time (~13.4K). The
cohort's subscriber-count and cadence distributions are published with the study; B is reported as a
within-channel effect for *these* channels, not a universal law.

## Data release (de-identified, ToS-compliant)

HMAC-hashed `channel_hash` and `video_hash` (secret salt never published); per-row analysis columns
(`window_cadence`, `upload_interval_days`, `uploads_per_week`, `subscribers`, `subscriber_band`,
`content_category`, `age_days`, `ln_vpd`, `publish_quarter`); binned aggregate tables; replication
notebook. No raw YouTube ids, titles, or thumbnails. "Methods reproduction, not measurement
verification." Re-verify against the YouTube API ToS on the publish push.

## Sign-off

- [x] Question framed as a relationship; reverse causality named in Phase 0
- [x] Two designs: A (cross-channel benchmark) + B (within-channel over time, the headline); the gap is the finding
- [x] B unit = video; predictor = 60-day rolling cadence window (width chosen outcome-blind from the cadence distribution); channel + calendar-quarter FE; channel-clustered SEs
- [x] Lagged estimand breaks contemporaneous reverse causation; persistent momentum disclosed as the headline limitation; contamination ladder reported
- [x] Day-90 VPD confirmed infeasible (1 snapshot/video median); clock artifact handled by ln(age) + quarter FE + load-bearing age-band leg + ≥90-day floor, and disclosed
- [x] Identifying subset (≥8 active quarters + ≥2× cadence variation ≈ 13.4K channels) reported; B scoped to "channels that varied their cadence"
- [x] Breakout secondary measured as per-video probability (odds-per-shot), lottery-ticket trap handled
- [x] DAG: channel FE absorb all stable traits incl. niche/size; only momentum + clock remain
- [x] Predictor leakage-audited and cleared (timestamp-derived, untrained)
- [x] Locked whole-channel 20% holdout; future quasi-experimental tier named and scoped out; ToS-checked release
- [ ] → ready to rewrite the pre-registration, re-tagged `003-v1-preregistration` before touching frequency-vs-VPD
