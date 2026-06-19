# Upload Frequency vs. Views

**Question:** Within a channel over time, when it posts more than its own baseline, do its videos
earn more, fewer, or about the same views, and does its total reach rise or stay flat?

"Post more" is advice you hear constantly, and the common counter-worry is that extra uploads just
slice the same audience across more videos ("spreading thin"). This study takes each channel as given
and asks how its views move with its own upload cadence, holding the channel itself fixed.

## Cohort and measurement

The cohort is English-language business and creator-economy YouTube channels with at least 20
qualifying videos (public + organic + long-form, published at least 90 days before measurement), held
in the study's frozen data store. **1,191,937 videos across 16,267 channels.** For each video, local
cadence is the channel's count of qualifying uploads in a 60-day rolling window ending just before
that video's publish date (a lag). Views are a single recent snapshot, looked at per video (per day,
to hold age constant) and as the channel's total per quarter.

## Headline spec (locked in the pre-reg)

Within-channel (channel fixed effects) and net of platform-wide era (calendar-quarter fixed effects),
the age-controlled slope of `log(views per day)` on `log(1 + local cadence)`, on the channels that
varied their cadence, reported as **% change in views per day per doubling of cadence**, with
channel-clustered inference, computed on the holdout once.

See [`design-spec.md`](./design-spec.md) and [`preregistration.md`](./preregistration.md). The
pre-reg was tagged (`003-v1-preregistration`) before any frequency-vs-views query ran.

## Result

**A precise null per video: about −10% change in views per day per doubling of cadence (95% CI
roughly −12% to −9%), held-out.** Within a channel, videos published when it was posting more earn
about the same views per day, far too small a move to be the "post more, grow faster" lift the advice
promises, and the interval sits inside the ±15% practical bar set in advance.

Total reach still rises: because the per-video **mean** stays flat while the count grows, total views
scale roughly one-for-one with output ("bigger pie, same slices"). That total-views view is a
post-hoc follow-on, labeled as such; the per-video slope is the pre-registered, sealed-holdout
headline.

Read the [`report.md`](./report.md), the [`methodology.md`](./methodology.md), and reproduce the
headline yourself from the public dataset, rebuilding the cadence window from scratch, with
[`dataset/replicate.qmd`](./dataset/replicate.qmd).

## Status

Published. Pre-registration tagged `003-v1-preregistration`; the headline was read once off the
sealed holdout, per the labs protocol. Everything here is a correlation, with the direction unknown;
nothing was randomized.
