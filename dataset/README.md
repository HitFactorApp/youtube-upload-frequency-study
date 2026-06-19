# De-identified dataset: Upload Frequency vs. Views

This is a **methods-reproduction** dataset, not a measurement-verification one. It lets a
replicator rebuild our cadence predictor and re-run our statistics (the within-channel
fixed-effects panel, the channel clustering, the bootstrap) and catch a coding or specification
error. It does **not** let anyone re-verify our view counts, because the real YouTube identifiers
are not in it. The dataset proves the *method*; the *measurement* is the single view capture
described in the methodology.

## File

`upload_frequency_vpd_deidentified.csv`: one row per qualifying video (long-form, public, organic,
at least 90 days old, on a channel with at least 20 qualifying videos). **1,191,937 rows across
16,267 channels.** This is the analysis frame: the same rows the headline runs on, and the same rows
that define each channel's upload cadence.

| Column | Meaning |
|---|---|
| `channel_hash` | One-way keyed hash of the channel id (see "De-identification"). Rows sharing a channel share this value: the channel-clustering structure, and each channel's full upload timeline, are preserved. |
| `video_hash` | One-way keyed hash of the video id. |
| `publish_date` | The video's publish date, `YYYY-MM-DD`. Day resolution, because the cadence predictor is a 60-day rolling window that straddles month boundaries. This is the column that lets you rebuild the predictor. |
| `view_capture_date` | The date the view count was captured (almost the entire cohort falls in a short window). |
| `views` | Total views at the capture date. |
| `age_days` | Days between publish and the view capture. Views per day = `views / age_days`. |
| `duration_seconds` | Video length, in seconds (the duration robustness covariate). |
| `holdout` | 1 if the row is in the held-out 20% of channels (the headline is computed on these), 0 if exploration. Assigned by a deterministic hash of the raw channel identifier (one fixed bucket in five) *before* that identifier was replaced with `channel_hash`. It ships as a column because, with the real identifier gone, you cannot recompute that assignment yourself. |

## Reproducing the headline

The headline is the within-channel **per-video** view effect of upload cadence, on the holdout,
reported two ways (pooled fixed effects and one-channel-one-vote).

1. **Build the cadence predictor.** Within each `channel_hash`, sort rows by `publish_date`. For each
   video, count the channel's other qualifying uploads (other rows for that channel) whose
   `publish_date` falls in the **60 days strictly before** this video's `publish_date`. That count is
   `window_uploads`; the predictor is `ln(1 + window_uploads)`. (Every row in this file is a
   qualifying upload, so the file alone is sufficient; nothing external is needed.)
2. **Build the outcome.** `ln_vpd = ln(views / age_days)`. Add `ln_age = ln(age_days)` as a covariate
   and a calendar-quarter label derived from `publish_date` (calendar quarter of publish).
3. **Fit the panel.** Regress `ln_vpd` on the cadence predictor plus `ln_age`, with **channel** and
   **calendar-quarter** fixed effects (the pooled estimator). Separately, compute each channel's own
   within-channel slope (channel-demeaned) and take the median across channels (the
   one-channel-one-vote estimator).
4. **Cluster.** Bootstrap by resampling **channels** (not videos) with replacement.
5. **Filter to `holdout = 1`** for the held-out headline; use `holdout = 0` for the exploration set.

The per-video effect is a small, precisely-estimated dip (about −10% per doubling, CI excluding zero
but inside the ±15% bar: present but practically trivial, not a null); the binned curves and the
median/mean decomposition behind every chart are in `../aggregates/`.

## De-identification

`channel_hash` and `video_hash` are `HMAC-SHA256(secret_salt, id)`, truncated, with a secret salt we
**do not publish**. (A plain hash of a YouTube id would be reversible, since the ids are short and
enumerable, so we key the hash with an unpublished salt: it's stable, so which-rows-share-a-channel
survives for replicators, but it isn't reversible back to a real id.)

No raw YouTube data (real ids, titles, descriptions, thumbnails) is in this file. A bare
`(publish_date, views)` pair with no real id is **not** a YouTube lookup key: you cannot query
YouTube by date and view count to recover a video. With the ids hashed and the raw fields gone, each
row is a point in a distribution rather than "the view count *for video X*": the same category as the
published aggregate tables.

## Suggested citation

> Hitfactor (2026). *Does posting more get you more views? A within-channel analysis of upload
> frequency and views across 1.24M videos.* hitfactorapp.com/labs/2026-upload-frequency/
