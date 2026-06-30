# Shanghainese STT Eval — Data Preparation Docs

How the **Shanghainese (Wu Chinese) STT evaluation set** was built, end to end:
from the raw recordings shared over Google Drive and the original recording
request, through audio preparation and reference assembly, to the published
Hugging Face dataset.

The dataset itself (and its in-repo source) lives at
[`datasets/shanghainese/`](../../datasets/shanghainese/). These docs explain
**how it was made and why**, so the process is auditable and repeatable.

## What was produced

| Artifact | Location |
|----------|----------|
| In-repo dataset source (references, manifest, `build.py`, card) | [`datasets/shanghainese/`](../../datasets/shanghainese/) |
| Published dataset (audio embedded, private) | `lvhoang/shanghainese-stt-eval` on the Hugging Face Hub |
| These provenance/process docs | `docs/shanghainese-stt-eval/` |

## Quick facts

- **58 clips** — 50 single sentences + 8 multi-turn conversations
- **~5.9 minutes** of audio total
- **Language:** Shanghainese / Wu Chinese (`wuu`), references written in Chinese characters
- **Purpose:** *adversarial* eval — every item is chosen so a recognition error
  flips the **meaning** an LLM agent would act on (negation, opposites, numbers,
  times, medical & safety details), not just surface words
- **Ground-truth status:** references are **script-derived, not yet verified
  against the audio**; 8 items are flagged for human review (see below)

## Documents

1. **[Source data & original request](source-data.md)** — where the recordings
   came from (Google Drive), the full recording script with English glosses and
   the substitution notes given to the speaker.
2. **[Data preparation pipeline](data-preparation.md)** — extraction, file→transcript
   mapping, audio conversion to 16 kHz mono PCM, and reference assembly
   (`ground_truth.jsonl` / `metadata.jsonl` / `manifest.csv`).
3. **[Hugging Face dataset](huggingface-dataset.md)** — the enriched schema, how
   it was built and pushed, the dataset card, loading it, and how to update it
   after verification.
4. **[STT provider survey](provider-survey.md)** — desk research on how enterprise
   STT vendors (Azure, iFlytek, Baidu, Tencent, Volcengine) model Shanghainese,
   mapped to the same test-vector categories as the recordings. Vendor-capability
   analysis and stated claims — **hypotheses to validate against** the empirical
   eval set, not measured WER.
5. **[Related datasets & benchmarks](related-datasets.md)** — external public
   resources for Wu Chinese STT, starting with **WenetSpeech-Wu** (~8,000 h corpus
   + manually curated Wu benchmark and models) — large-scale training/eval data
   that complements this set.

## Pipeline at a glance

```
Google Drive (.zip of .m4a)  +  original recording request (50 sentences + 8 dialogues)
        │
        ▼  extract + inventory (58 files)
        ▼  map each file → sample_id + reference transcription
        ▼  afconvert: m4a (48 kHz stereo AAC) → 16 kHz mono 16-bit PCM
        ▼  assemble: ground_truth.jsonl · metadata.jsonl · manifest.csv
        │
        ├──► datasets/shanghainese/   (in-repo source; audio gitignored)
        └──► push_to_hub → lvhoang/shanghainese-stt-eval  (parquet, audio embedded)
```
