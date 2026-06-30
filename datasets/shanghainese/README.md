# Shanghainese (Wu Chinese) STT Eval Dataset

A small, adversarial speech-to-text evaluation set in **Shanghainese (Wu Chinese,
`wuu`)**. 58 recordings — 50 single sentences + 8 multi-turn conversations,
~5.9 minutes total — chosen so that a recognition error would change the
*meaning* an LLM agent acts on (negation, opposites, numbers, times, medical and
safety details), not just the surface words.

> **Why this is hard:** most production STT services have no Shanghainese model.
> They tend to fall back to Mandarin, so this set measures how badly meaning is
> mangled on an unsupported topolect — exactly the failure mode that matters for
> a voice agent. The references below are written in Chinese characters
> representing the intended Shanghainese utterance.

## Layout

| Path | Role | Edit? |
|------|------|-------|
| `metadata.jsonl` | **Source of truth** — one rich record per clip (transcription, English gloss, part, turns, flags) | ✅ edit here |
| `audio_m4a/` | **Source of truth** — original recordings, renamed `<sample_id>.m4a` | ✅ originals |
| `ground_truth.jsonl` | Repo-native references (header + `{type:"sample", ...}` rows) | ⚙️ generated |
| `manifest.csv` | Human-readable mapping table | ⚙️ generated |
| `audio_pcm/` | 16 kHz mono 16-bit PCM (`<sample_id>.pcm`), the form the pipeline streams | ⚙️ generated |
| `hf_dataset/` | Packaged HuggingFace dataset (`load_from_disk`), features: `sample_id, audio, duration_seconds, transcription` | ⚙️ generated |
| `build.py` | Regenerates all generated artifacts from the two sources of truth | — |

## Sample IDs

- Sentences 1–50 → `shanghainese-001` … `shanghainese-050`
- Conversations 1–8 → `shanghainese-conv-1` … `shanghainese-conv-8`
  (reference is the four speaker turns concatenated; per-turn text kept in
  `metadata.jsonl > turns`)

Categories (the `part` field): 1 basic vocabulary · 2 negation/opposites ·
3 actions & states · 4 time/schedules/numbers · 5 ambiguous questions ·
6 medical & safety · 7 multi-turn conversations.

## ⚠️ Ground-truth status — references are script-derived, not yet audio-verified

The transcriptions come from the **recording script**, not from listening to each
clip. They have **not** been verified by a Shanghainese speaker. Treat them as a
strong starting reference, then verify before publishing any WER number.

8 items are flagged `needs_verification: true` because the script explicitly let
the speaker substitute a more natural Shanghainese expression, so the audio may
differ from the written reference:

`shanghainese-014, -021, -030, -032, -038, -043, -044, -050`

To verify/correct, listen to `audio_m4a/<id>.m4a`, edit the `transcription` (and
set `"verified_by": "human"`) in `metadata.jsonl`, then re-run `build.py`.

## Use it in the benchmark

This is **not English**, so the standard `stt-benchmark download` path (which
filters the HF dataset for `language == "eng"`) will not pull it. Load it directly:

```python
from datasets import load_from_disk
ds = load_from_disk("datasets/shanghainese/hf_dataset")
```

Or import the references into a benchmark DB after you have the audio samples
registered (see `stt-benchmark ground-truth import datasets/shanghainese/ground_truth.jsonl`).

## Regenerate

```bash
# 1. PCM + ground_truth.jsonl + manifest.csv  (macOS: uses afconvert)
python3 datasets/shanghainese/build.py

# 2. (re)package the HuggingFace dataset
uv run python scripts/create_hf_dataset.py \
    --ground-truth datasets/shanghainese/ground_truth.jsonl \
    --audio-dir   datasets/shanghainese/audio_pcm \
    --output      datasets/shanghainese/hf_dataset
```

On Linux replace the `afconvert` call in `build.py` with
`ffmpeg -i in.m4a -ac 1 -ar 16000 -f s16le out.pcm`.

> **Note on size / git:** `audio_pcm/` (~11 MB) and `hf_dataset/` (~11 MB) are
> fully regenerable from `audio_m4a/` + `metadata.jsonl`. The repo otherwise keeps
> audio out of git (`stt_benchmark_data/` is gitignored). If you'd rather not
> commit the derived binaries, keep only `audio_m4a/` + `metadata.jsonl` and
> regenerate, or push `hf_dataset/` to the Hugging Face Hub instead.
