# Methodology: Upload Frequency vs. Views

> The full methods for the study. The plan was registered and publicly timestamped before any
> frequency-vs-views number was queried (see `preregistration.md`, tag `003-v1-preregistration`).
> This document describes what we did; the [design spec](./design-spec.md) explains why each choice
> was made.

## The question

Within a channel over time, when it posts more than its own baseline, do its videos earn more,
fewer, or about the same views, and does its total reach rise or stay flat? "Post more" is advice
you hear constantly, and the common counter-worry is that extra uploads just slice the same audience
across more videos ("spreading thin"). We take each channel as given and ask how its views move with
its own upload cadence.

## Dataset

The cohort is English-language business and creator-economy YouTube channels drawn from our own
dataset, each with at least 20 qualifying videos. They are **not a random sample of YouTube** (see
Limitations): they entered through our funnel and skew toward English-speaking, business and
creator-economy, mid-to-large channels, and toward frequent posters (the typical channel posts about
every six days).

A video qualifies if it is:

- a regular long-form upload (not a Short), decided by the platform's own video-type label;
- public (not unlisted, private, hidden, or deleted);
- organic (we exclude videos our traffic classifier flags as primarily ad-driven, because
  ad-driven views don't test whether the upload earned its audience);
- old enough that views have settled: at least 90 days before measurement.

This is **1,191,937 videos across 16,267 channels**. The qualifying set is also the set that defines
upload cadence: a channel's cadence at any moment is its count of *qualifying* uploads, so the same
filter draws both the outcome rows and the predictor.

## Outcome

The outcome is **views**, measured at a single point in time (one capture per video, for almost the
entire cohort within a short window), looked at two ways: per video (views per day, to hold age
constant) and as the channel's total per calendar quarter. We work in log space because view counts
are heavily skewed by viral videos; a single outlier cannot swing a log-based comparison.

**Why views per day, age-controlled, rather than raw views.** The whole cohort was measured on a
single clock, so a video's age is really *when we captured its views*, not a behavior of the video.
The estimator removes age explicitly: a channel-fixed-effects regression of **log(views per day) on
the cadence term plus log(age)**, so the age artifact is differenced out rather than baked into the
outcome.

## The cadence measurement

For each video, **local cadence** is the count of the channel's qualifying uploads in a **60-day
rolling window ending just before that video's publish date** (a lag: the cadence leading up to the
video, excluding the video itself). We model it as `log(1 + uploads in window)` and report the slope
as a percent change in views per day per **doubling** of cadence. The 60-day width and the lag were
fixed in the plan; we report 30-day and 90-day windows as robustness legs.

A video published so early in a channel's history that the 60-day window predates its first upload
has a truncated window; such videos are kept, with the cadence counted over the available history and
a left-censoring flag, exactly as the plan specified.

## The comparison

**The unit is the video; the channel is the cluster.** Videos in the same channel are not
independent (shared creator, audience, niche, cadence), so we never treat videos as independent
observations. Every confidence interval uses the **channel** as the resampling unit.

**The headline is within-channel, over time.** We hold each channel fixed (channel fixed effects)
and add calendar-quarter effects to absorb platform-wide trends, then ask how its videos did in the
stretches it posted above its own baseline. Holding the channel fixed cancels every stable channel
trait (size, niche, audience, production quality), so the result can't be dismissed as "big channels
just post more."

**A contamination ladder.** We report three rungs in order so the confounding shrinks visibly: a
cross-sectional between-channel comparison (most confounded), a contemporaneous within-channel
window (still picks up a video's own neighbors), and the lagged within-channel window (the headline,
cadence strictly before the video). Reading the rungs in sequence shows how much of the raw
association is between-channel selection.

**Two co-equal estimators, fixed in the plan.** We compute the within-channel slope two ways and read
them together:

- the **pooled fixed-effects** slope (video-count weighted), and
- the **median of per-channel slopes** (one channel, one vote; immune to a few high-volume channels).

The two estimators answering the same question differently is the study's main guard against a result
that lives only in the weighting.

**Confidence intervals.** A whole-channel bootstrap: resample channels with replacement (never
videos), 10,000 times, fixed seed. With over a million videos, statistical significance is automatic
for any non-zero effect, so we de-emphasize p-values and report effect sizes, channel-clustered
intervals, and a practical-significance threshold (a ±15% step per doubling) declared in advance.

## The decision rule

The per-video headline is graded against a three-state rule fixed in advance, with a ±15% step per
doubling as the practical bar:

- **meaningful**: the interval excludes zero and the point is at least ±15%;
- **inconclusive**: ±15% sits inside the interval (too wide to separate a real effect from a null);
- **precise null**: the interval includes zero and lies entirely within ±15%.

A precise null is a finding (the effect is bounded small), not a power failure.

## The per-video decomposition

Because total views is, by arithmetic, the video count times the average views per video, the
interesting question is whether the per-video **average** holds up or collapses as a channel posts
more. We decompose the per-video outcome into the **median** video and the **mean** per video. The
median falls modestly with cadence while the mean stays flat (the spread widens: the typical video
dips, the single biggest video grows, the average lands near zero). A flat mean is what makes total
reach scale roughly one-for-one with output.

## The total-views follow-on (post-hoc, labeled)

The total-views elasticity (within-channel, total views per quarter against uploads per quarter) was
prompted by a reviewer's question **after** the main analysis, so it is exploratory, not
pre-registered. It agrees across both the exploration and the held-out sets. We flag it as a
follow-on throughout, and we are explicit that an elasticity near 1.0 contains an arithmetic floor
(total = count × mean per video); the part the data had to show, and did, is the flat mean.

## The shape check (suggestive)

Per-video efficiency across the cadence range is not a straight line: views per day per video rise to
a peak around one upload a week, hold through roughly two to three a week, then fall off at the
highest cadences. This shape was clear on the 80% exploration sample but did **not** fully reproduce
on the smaller sealed holdout, so we report it as suggestive only, held to the same replication bar as
everything else. The fall-off at the highest cadences is the sturdier half (it is not an age artifact:
the highest-cadence videos are the youngest, which would push their views up, yet they sit among the
lowest).

## The holdout

A fixed 20% of channels were set aside by a deterministic whole-channel rule before any exploration,
and the per-video headline was computed on them **once**, with the identical analysis code, after the
spec was frozen on the other 80%. The exploration result is the confirming companion; the held-out
number is the headline.

## Reproducing the statistics

The `aggregates/` directory holds the binned tables behind every chart and the headline decomposition,
and `dataset/` holds a de-identified row dataset (channel and video identifiers replaced by keyed
one-way hashes, so the channel-clustering structure survives but no real YouTube identifier is
republished). Because the dataset carries each video's publish date and channel hash, a replicator can
**rebuild the 60-day lagged cadence window from scratch**, then re-run the within-channel
fixed-effects panel, the clustering, and the bootstrap, and catch a coding or specification error.
They **cannot** independently re-verify our view counts from the dataset (the real identifiers are not
in it). The dataset proves the *method*, not the *measurement*. See `dataset/README.md`.

## Limitations (stated plainly)

- **Observational, and the direction is unknown.** We measure what posting more *goes with*, not what
  would happen if a channel changed its schedule. We do not claim posting more *causes* the view
  patterns, or rule out that something about a channel's situation drives both its posting and its
  views. Read every result as "moves with," never "causes."
- **The total-views result is a follow-on, not pre-registered.** It agrees across exploration and
  holdout but doesn't carry the same locked-in-advance protection as the per-video headline.
- **The sweet-spot shape is suggestive only.** It didn't fully replicate on held-out channels.
- **Sampling frame.** English-language business and creator-economy channels from our own funnel,
  skewed mid-to-large and toward frequent posters. The finding may not transfer to other languages,
  niches, or very small channels.
- **Views, not watch-time or revenue,** captured once; deleted and private videos are unobservable.
