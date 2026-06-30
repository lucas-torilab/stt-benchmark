# Data Preparation Pipeline

This is the exact process that turned the [source recordings and
request](source-data.md) into the in-repo dataset at
[`datasets/shanghainese/`](../../datasets/shanghainese/).

## Outputs

| File / dir | Role | Source-of-truth? |
|------------|------|------------------|
| `metadata.jsonl` | One rich record per clip (transcription, gloss, part, turns, flags) | ✅ yes |
| `audio_m4a/` | Original recordings, renamed `<sample_id>.m4a` | ✅ yes |
| `ground_truth.jsonl` | Repo-native references (header + `{type:"sample"}` rows) | ⚙️ generated |
| `manifest.csv` | Human-readable mapping table | ⚙️ generated |
| `audio_pcm/` | 16 kHz mono 16-bit PCM (`<sample_id>.pcm`) — pipeline streaming format | ⚙️ generated |
| `hf_dataset/` | Packaged Hugging Face dataset (`save_to_disk`) | ⚙️ generated |
| `build.py` | Regenerates every generated artifact from the two sources of truth | — |

`metadata.jsonl` + `audio_m4a/` are the canonical inputs; everything else is
reproducible from them with `build.py`.

## Step 1 — Obtain and extract

The Google Drive archive was downloaded and unzipped:

```bash
unzip "Shanghai Chinese-20260630T093649Z-3-001.zip" -d shanghai_raw
# -> shanghai_raw/Shanghai Chinese/{1..50}.m4a, Conversation 1.m4a, conversation 2-8.m4a
```

`afinfo` confirmed the recorded format: AAC, 2 channels, 48000 Hz.

## Step 2 — Map each file to a sample ID and reference

Every file is assigned a stable, human-readable `sample_id` and paired with the
"Sentence to record" from the [original request](source-data.md):

| Source file | `sample_id` |
|-------------|-------------|
| `1.m4a` … `50.m4a` | `shanghainese-001` … `shanghainese-050` |
| `Conversation 1.m4a` … `conversation 8.m4a` | `shanghainese-conv-1` … `shanghainese-conv-8` |

Each record also carries: English gloss (`meaning_en`), category (`part` /
`part_name`, 1–7), `kind` (`sentence`/`conversation`), `item_no`, the original
filename, and the verification flag/note. For conversations the four speaker
turns are kept structured in `turns` (see Step 4).

## Step 3 — Convert audio to 16 kHz mono PCM

The benchmark pipeline streams **16 kHz, mono, 16-bit little-endian PCM** (see
[`DatasetDownloader.audio_array_to_pcm`](../../src/stt_benchmark/dataset/downloader.py)).
The 48 kHz stereo AAC recordings are converted with macOS `afconvert`:

```bash
afconvert -f WAVE -d LEI16@16000 -c 1  input.m4a  out.wav
```

- `-f WAVE` — WAV container
- `-d LEI16@16000` — little-endian int16 samples at 16000 Hz
- `-c 1` — downmix to mono

`build.py` then reads the WAV with Python's `wave` module and writes the raw
sample bytes (header stripped) to `audio_pcm/<sample_id>.pcm`. **Duration is
recomputed from the PCM** (`len(pcm_bytes) / 2 / 16000`) so it always matches the
actual audio rather than the container's estimate.

> **On Linux** (no `afconvert`), the equivalent is:
> ```bash
> ffmpeg -i input.m4a -ac 1 -ar 16000 -f s16le out.pcm
> ```

## Step 4 — Assemble the references

**Single sentences** use the recording-script text verbatim as the reference.

**Conversations** use the four speaker turns **concatenated with single spaces**
as the `transcription` (a single reference string per file, since each
conversation is one audio file), while the structured per-turn breakdown is
preserved in `metadata.jsonl > turns`, e.g.:

```json
"turns": [
  {"speaker": "A", "text": "侬明朝有辰光伐？"},
  {"speaker": "B", "text": "有个。啥辰光？"},
  ...
]
```

From these records `build.py` writes three views of the same data:

- **`ground_truth.jsonl`** — the repo-native format consumed by
  `stt-benchmark ground-truth import` and `scripts/create_hf_dataset.py`. A
  `header` line (`model: human-script`, `run_id: shanghainese-v1`, language) is
  followed by one `{"type":"sample", sample_id, duration_seconds, transcription, ...}`
  per clip.
- **`metadata.jsonl`** — the full rich record (the source of truth).
- **`manifest.csv`** — a flat, spreadsheet-friendly table for humans.

## Step 5: Flags

8 items carried an explicit "you may substitute a more natural expression" note
in the request, so the recorded audio may diverge from the written reference.
They are marked `needs_verification: true`:

```
shanghainese-014, shanghainese-021, shanghainese-030, shanghainese-032,
shanghainese-038, shanghainese-043, shanghainese-044, shanghainese-050
```

See [Ground-truth status](#ground-truth-status) below.

## Result

```
58 samples  (50 sentences + 8 conversations)
~5.9 minutes of audio total
8 flagged needs_verification
```

## Ground-truth status

**References are derived from the recording script, not yet verified against the
audio by a Shanghainese speaker.** Treat them as a strong starting reference and
verify before publishing any WER number. This matters more than usual here
because Wu Chinese has no single orthographic standard and the 8 flagged items
were explicitly open to substitution.

### Verification workflow

1. Listen to `datasets/shanghainese/audio_m4a/<sample_id>.m4a`.
2. Correct the `transcription` in `metadata.jsonl` and set
   `"verified_by": "human"` on that record.
3. Regenerate and re-publish:
   ```bash
   python3 datasets/shanghainese/build.py            # refresh pcm + ground_truth + manifest
   # then re-push to the Hub — see huggingface-dataset.md
   ```

## Reproduce

```bash
# Regenerate audio_pcm/, ground_truth.jsonl, manifest.csv from
# metadata.jsonl + audio_m4a/  (macOS; uses afconvert)
python3 datasets/shanghainese/build.py
```

> The original one-shot builder (which mapped raw zip filenames → sample IDs and
> embedded the transcript spec) was used once to bootstrap `metadata.jsonl` +
> `audio_m4a/`. From then on, `build.py` is the maintained, self-contained
> regenerator and `metadata.jsonl` is edited directly.
