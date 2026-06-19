# We Analyzed 1.24 Million YouTube Videos to Settle One Argument: Does Posting More Spread Your Views Thin?

*A Hitfactor Labs study of 1,239,151 videos across 16,282 channels. Every number below is an
association measured in observational data: we describe what moves with what, and we do not claim one
thing causes another.*

---

## The argument we set out to settle

A common worry among creators: if you upload a lot more, you don't actually *gain* views, you just
**slice the same audience across more videos**, so each one earns less and your total stays flat.
"Spreading thin."

We can check this at scale. We measured, within each channel over time, how its views moved in the
stretches it posted more versus the stretches it posted less, holding the channel itself fixed (so
this is not "big channels post more"). Here is what the data says.

## Key findings

1. **Posting more does not spread your views thin. Total views scale one-for-one with uploads:**
   within a channel, **doubling how often you post is associated with roughly double the total views**
   (elasticity 1.00, 95% CI 0.97-1.03, holdout). The "spread thin" prediction (total stays flat) is
   not what the data shows.
2. **For 93% of channels, posting more went with more total views.** Only **7%** showed lower total
   views in their higher-posting stretches.
3. **The typical video does earn a bit less the more you post.** The median video's views-per-day
   declines with cadence (about **-15% per doubling** of posting rate). So there is a real per-video
   cost, it is just far smaller than the gain from having more videos.
4. **But the best videos get bigger.** As posting rises, the *spread* of outcomes widens and the
   single biggest video in a stretch grows substantially. The typical video dips, the top video jumps,
   and the **average** per video comes out **flat** (-0.2%, CI -3.3% to +2.7%).
5. **Per-video efficiency has a turning point around weekly.** Views-per-day per video rises with
   cadence up to roughly one upload per week, then declines at the highest cadences (more than one a
   day). This shape was clear in our exploration sample but did not fully replicate on the smaller
   sealed holdout, so we report it as **suggestive, not confirmed**.

The one-line version: **you don't post your way to fewer total views. You trade a slightly lower
typical-video number for more total reach and bigger upside.**

## Methodology (read this before the charts)

**The data.** 1,239,151 long-form, public, organic videos across 16,282 English-language
business/creator-economy YouTube channels in our dataset, each with at least 20 qualifying videos.
This is not a random sample of YouTube; it skews mid-to-large and toward frequent posters (the typical
channel posts about every six days). Views are a single recent snapshot.

**The unit and the comparison.** The strong comparison here is *within a channel over time*: we hold
the channel fixed and ask whether its videos did better or worse in periods when it was posting more
than its own baseline. This cancels out everything stable about a channel (its size, niche, audience,
production quality), so the result cannot be dismissed as "bigger channels just post more." Upload
cadence is measured in a 60-day rolling window around each video; the outcome is views-per-day per
video, or total views per channel-quarter. Every confidence interval treats the **channel** as the
unit (channels are not independent video-by-video).

**What we will and will not claim.** This is observational. We measure associations and report their
direction and size. We do **not** claim a cause, and we do not know the direction of the relationship:
we cannot tell whether posting more leads to these view patterns, or whether something about a
channel's situation drives both its posting and its views. Read every result as "moves with," not
"causes."

**The holdout.** We locked a random 20% of channels away before looking at any view data, tuned every
choice on the other 80%, and read the headline once on the sealed 20%. The total-views elasticity
above was prompted by a reviewer question *after* the main analysis, so it is exploratory (not
pre-registered), though it agrees across both the exploration and holdout sets.

**Why we don't lead with p-values.** With over a million videos, everything is "statistically
significant." So we lead with effect sizes and a practical-significance bar we set in advance, and we
de-emphasize p-values entirely.

---

## Finding 1: Total views scale one-for-one with posting

![Total views climb with cadence](figures/fig2_total_climbs.png)

Within a channel, the relationship between how much it posts in a quarter and its total views that
quarter has an **elasticity of 1.0**: a 1% increase in uploads goes with a 1% increase in total
views. Doubling uploads goes with roughly doubling total views. Across upload deciles, the median
channel-quarter's total views rise monotonically, from a few hundred at the lowest cadence to tens of
thousands at the highest.

A fair caution: total views is, by definition, the number of videos times the average views each,
so *some* rise with more uploads is just arithmetic. The real question is whether the per-video
number holds up or collapses as you add videos. It holds up (Finding 3). That is why the total scales
cleanly rather than flattening.

**Key Takeaway:** Posting more is associated with proportionally more total views, not the same views
sliced thinner. For 93% of channels, more posting went with more total views.

---

## Finding 2: The typical video dips, but the best video jumps

![Decomposition: typical down, best up, mean flat](figures/fig3_decomposition.png)

When a channel posts more, three things move together in the data:

- the **typical** (median) video earns somewhat less per day (down with cadence),
- the **spread** of outcomes widens, and
- the **single biggest** video in the stretch gets substantially bigger.

These offset: the *average* views per video stays essentially flat (-0.2%) even as the *median*
falls. In plain terms, posting more is associated with a less consistent per-video result, a lower
typical video but a higher ceiling, and the same average. None of this is a claim about why creators
post or what causes the spread to widen; it is just the shape of the numbers.

**Key Takeaway:** More posting goes with a lower typical video but a bigger top video, washing out to
a flat average, which is why total reach still grows.

---

## Finding 3: A per-video sweet spot around weekly (suggestive)

![Per-video VPD U-shape](figures/fig1_pervideo_ushape.png)

Looking only at per-video efficiency, the relationship is not a straight line. Views-per-day per
video rises with cadence up to roughly one upload per week, then declines at the highest cadences
(channels posting more than once a day). This inverted-U was clear and replicated within our 80%
exploration sample, but on the smaller sealed holdout it did not fully reproduce, so we report it as
**a pattern the data suggests, not a confirmed result**. The decline at the very highest cadences is
the more robust half (it is not an artifact of how old the videos are: the highest-cadence videos are
the *youngest*, which would push their views up, yet they are among the lowest).

**Key Takeaway:** Per-video efficiency looks best around weekly and softest at the highest cadences,
but this shape is suggestive, not confirmed on held-out data.

---

## What this means

1. **The "spreading thin" worry is not supported.** Within a channel, more posting goes with more
   total views, roughly proportionally, for the large majority of channels.
2. **There is a real per-video tradeoff, and it is small.** The typical video earns a bit less as you
   post more, but the average is flat and the total grows. You are trading consistency for reach and
   upside, not losing views.
3. **This is a per-video-vs-total story, not a recommendation.** It is an association, with the
   direction unknown. It describes what posting more *goes with* in our data, not what would happen if
   a given creator changed their schedule.

---

## Credits & disclosures

- **Data:** Hitfactor proprietary dataset (1,239,151 videos / 16,282 channels). Views are a single
  recent snapshot; deleted/private content is not observed.
- **Method:** within-channel fixed-effects panel; channel-clustered intervals; a pre-registered,
  sealed-holdout design (pre-registration tagged `003-v1-preregistration`). The total-views elasticity
  is an exploratory follow-on, labeled as such.
- **Review:** analysis plan and findings independently reviewed by our statistics reviewer.
- **Limitation:** observational; associational only; not a random sample of YouTube; English-language
  business/creator-economy channels, mid-to-large, frequent posters.

**Suggested citation:** Hitfactor Labs (2026). *Does Posting More Spread Your Views Thin? A
within-channel analysis of upload frequency and views across 1.24M videos.* hitfactorapp.com/labs
