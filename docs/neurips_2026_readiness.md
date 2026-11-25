# NeurIPS 2026 Replication Readiness

Checked on 2026-11-25. **The corpus is available for acquisition; adapting and
validating the experiment is still necessary before launching paid ranking.**
This check did not run any model inference or alter the frozen ICML results.

## Availability

| Track | Unique accepted papers | PDF attachment present | Camera-ready revision recorded | PDFs tested |
| --- | ---: | ---: | ---: | ---: |
| Main | 7,896 | 7,896 | 7,896 | 6/6 |
| Evaluations & Datasets | 970 | 970 | 970 | 3/3 |
| Position Papers | 137 | 137 | 137 | 3/3 |
| Total | 9,003 | 9,003 | 9,003 | 12/12 |

Counts come from authenticated OpenReview API v2 queries for each track's
`content.venueid`. Every retained note was publicly readable (`readers` was
`["everyone"]`). Every record had a nonempty `content.pdf` and an applied
invitation containing `Camera_Ready`. These signals establish metadata
coverage, not byte-level validity of every attachment.

The OpenReview IDs exactly match the deduplicated
[official conference catalog](https://neurips.cc/static/virtual/data/neurips-2026-orals-posters.json)
for all three tracks. Its 9,275 entries are not 9,275 distinct accepted research
papers: 133 core-track entries repeat a paper as a separate presentation and
139 entries belong to other programs. One oral entry without a forum URL can
be linked to its poster through `related_events_ids`.

An anonymous API notes query returned a 403 browser-verification challenge;
that route was stopped without retry. Normal login using the existing locally
configured OpenReview account succeeded. All subsequent probe requests were
sequential, spaced by at least three seconds, with zero automatic retries.
Credentials and tokens were neither printed nor saved in reports.

The PDF sample selected the two lowest SHA-256 hashes of paper IDs within each
main-track presentation tier, and one per tier in each other track. All twelve
official PDFs downloaded, had valid signatures, and produced extractable text.
The existing `parse-pdfs` command, with `--main-paper-max-pages 10`, returned
12 `ok`, zero `needs_review`, and zero failures. Six had a nonfatal
`missing_conclusion` diagnostic; section extraction still needs a larger audit.
Full PDFs ranged from 15 to 108 pages including references and appendices.
This was not a full-corpus download or an anonymization test.

One forum per track was checked with `details=directReplies`, retaining only
public replies for the availability summary. These contained 3, 3, and 4
official reviews respectively, plus a public decision and meta-review in each.
The existing score extractor found overall ratings in the main and E&D samples,
but no canonical numeric overall score in the position-track sample. Do not
treat that as a missing review or mix the tracks' score scales. Full review
coverage and the position-track schema remain to be audited.

## Version Policy

NeurIPS says its [camera-ready upload stage is closed](https://neurips.cc/Conferences/2026/PosterInstructions),
but a further revision window is planned after the conference. Thus these are
the current camera-ready PDFs, not a promise that they will never change.
The [proceedings index](https://proceedings.neurips.cc/) currently stops at 2025;
use OpenReview for this acquisition, not the proceedings archive or arXiv.
Freeze note IDs, PDF attachment paths, retrieval dates, and SHA-256 checksums.
Retain later revisions separately instead of silently replacing scoring inputs.

## Required Adaptations

1. **Define the population.** Start with the 7,896 main-track papers for the
   cleanest primary analysis. E&D can have a separate leaderboard and a pooled
   sensitivity analysis; keep position papers separate because their rubric
   differs. Preserve track and presentation metadata outside model inputs.
2. **Configure the venue, do not clone the pipeline.** Reuse the API client,
   resumable downloader, parser, redaction, ensembles, tournaments, and analysis.
   Replace ICML-specific endpoint defaults, venue allowlists, prompt wording,
   file paths, and report labels through a small experiment configuration.
   The virtual-site adapter currently hardcodes ICML URLs and provenance.
   Metadata/score CLI commands also need an explicit authenticated mode.
3. **Use the correct page policy.** The [main-track handbook](https://neurips.cc/Conferences/2026/MainTrackHandbook)
   permits nine submission pages plus one camera-ready content page. Set
   parsing `--main-paper-max-pages 10`, anonymization `--max-pages 10`, and
   frontier cards `--pdf-excerpt-pages 10`, while retaining reference/appendix
   detection. Do not reuse the ICML nine-page defaults unchanged.
4. **Audit a pilot before paid inference.** Download, parse, and anonymize a
   stratified 100-paper pilot. Verify content preservation, identity removal,
   absence of human outcomes, checksums, and restart behavior. Explicitly map
   review fields and scales per track. Author-provided contribution labels
   are available for main and E&D, but are not the same taxonomy as our AI labels.
5. **Freeze a new experiment.** Preserve the ICML study unchanged. Confirm
   current model availability and prices, pin prompts/models and deterministic
   sampling, then reuse the same staged screening and tournament architecture.
   Retain a strict separation between paper-only model inputs and the human
   metadata joined after ranking. No model or budget changes were made here.

The handbook describes an AI-assisted reviewing experiment. Any comparison
should therefore be framed as AI rankings versus conference outcomes, not
necessarily AI versus wholly unaided humans. Treatment information, if made
available, would support a useful stratified analysis.

## Evidence

The checked-in [audit report](neurips_2026_availability_20261125.json) records
track counts, cross-source reconciliation, sample IDs/checksums, parse outcomes,
and public review availability. Local raw snapshots and probe manifests are in
`data/metadata/neurips_2026_availability_20261125/`; sample PDFs are in
`data/pdfs/neurips_2026_probe/`, and extracted text in
`data/extracted_text/neurips_2026_probe/`. These raw artifacts are not committed.

Only the 12 sample PDFs have been downloaded. The remaining 8,991 PDF bodies
have not yet been tested; metadata availability should not be reported as
9,003 successful downloads.
