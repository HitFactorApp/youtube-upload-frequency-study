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

1. **Posting more does not spread your views thin. Total views scale roughly one-for-one with
   uploads:** within a channel, **doubling how often you post is associated with roughly double the
   total views** (elasticity ~1.0, 95% CI 0.97-1.03, holdout). The result stays near one-for-one after
   dropping each channel's top 1% and top 5% of videos, so it is not carried by a few viral hits. The
   "spread thin" prediction (total stays flat) is not what the data shows.
2. **The extra reach comes from volume, not a per-video lift.** A video published during a busy
   stretch does not do better; per video the slope is a small dip (**-10% per doubling**, CI -12% to
   -8.5%), real but too small to act on. More posting buys more shots on goal, not better shots.
3. **No cannibalization.** The typical (median) video's views-per-day declines only modestly with
   cadence (about **-15% per doubling**), and the rest of the slate holds, so the extra videos add to
   the total instead of splitting a fixed audience. The single biggest video in a stretch actually
   grows. There is no per-video collapse to cancel out the higher count, which is why the total
   climbs.
4. **Per-video efficiency may peak around weekly (unconfirmed).** Views-per-day per video rises with
   cadence up to roughly one upload per week, then declines at the highest cadences (more than one a
   day). This shape was clear in our exploration sample but did not fully replicate on the smaller
   sealed holdout, so we report it as **suggestive, not confirmed**, and are running a larger follow-up
   to settle it.

The one-line version: **posting more grows your total reach, roughly one-for-one, but through volume,
not by making any single video do better.** Whether posting drives the reach or a strong run drives
both is unestablished; read it as a strong pattern, not a proven lever.

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

Two cautions. First, total views is, by definition, the number of videos times the average views
each, so *some* rise with more uploads is just arithmetic. The real question is whether per-video
performance holds up or collapses as you add videos. It holds up (Finding 2), which is why the total
scales cleanly rather than flattening. Second, the result is robust to outliers: re-running the
elasticity after dropping each channel's top 1% and top 5% of videos leaves it near one-for-one, so it
is not carried by a handful of viral hits. Robustness to trimming tells us the association is real, it
does not tell us the direction (see Methodology, reverse causality).

**Key Takeaway:** Posting more is associated with proportionally more total views, not the same views
sliced thinner, and the association survives trimming the biggest videos.

---

## Finding 2: The typical video dips, but the best video jumps

![Decomposition: typical video down, biggest video up, no per-video collapse](figures/fig3_decomposition.png)

When a channel posts more, the per-video distribution doesn't collapse, it spreads:

- the **typical** (median) video earns somewhat less per day (about -15% per doubling),
- the **spread** of outcomes widens, and
- the **single biggest** video in the stretch gets substantially bigger.

So the median dips a little while the rest of the slate holds and the ceiling rises. There is no
across-the-board per-video collapse to cancel out the higher video count, which is why the totals add
up rather than treading water. (We note the average per video moves little, but the average is the
least stable of these measures and we don't lean on it; the load-bearing facts are the modest median
dip and the absence of a collapse.) None of this is a claim about why a channel posts or what causes
the spread; it is the shape of the numbers.

**Key Takeaway:** More posting goes with a slightly lower typical video but no per-video collapse, so
the extra videos add reach instead of cannibalizing the slate.

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

1. **The "spreading thin" worry is not supported.** Within a channel, more posting goes with
   proportionally more total views, and that holds after dropping the biggest videos.
2. **The reach comes from volume, not a per-video lift.** Each video doesn't do better when you post
   more (a small -10% dip per doubling, too small to act on). You reach more people by putting up more
   videos, while each one stays about as good, not by making any single video win more.
3. **This is an association, not a recommendation, and the direction is unknown.** It describes what
   posting more *goes with* in our data, not what would happen if a channel changed its schedule. A
   channel on a strong run may both post more and pull more views, so we do not claim posting causes
   the reach.

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
