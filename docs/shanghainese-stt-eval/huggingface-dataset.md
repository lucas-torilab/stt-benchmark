# Hugging Face Dataset

The prepared data is published to the Hub as
**`lvhoang/shanghainese-stt-eval`** (private). This page covers the schema, how
it was built and pushed, the dataset card, loading it, and updating it.

## Two packaged forms

There are two ways the audio + references get packaged. They produce the same
data; pick by use case.

### a) Repo-native via `create_hf_dataset.py` (4 columns)

The repo's own [`scripts/create_hf_dataset.py`](../../scripts/create_hf_dataset.py)
reads `ground_truth.jsonl` + the `.pcm` files and emits the minimal schema the
benchmark expects (`sample_id`, `audio`, `duration_seconds`, `transcription`).
This is what writes the local `datasets/shanghainese/hf_dataset/`:

```bash
uv run python scripts/create_hf_dataset.py \
    --ground-truth datasets/shanghainese/ground_truth.jsonl \
    --audio-dir   datasets/shanghainese/audio_pcm \
    --output      datasets/shanghainese/hf_dataset
```

### b) Enriched dataset pushed to the Hub (13 columns)

For the published dataset the schema is **enriched** so the gloss, category, and
verification flags are queryable in the Hub viewer instead of buried in side
files. It is built directly from `metadata.jsonl` + `audio_pcm/` and pushed with
`Dataset.push_to_hub(...)` (parquet, audio embedded).

| Column | Type | Description |
|--------|------|-------------|
| `sample_id` | string | `shanghainese-001`…`-050`, `shanghainese-conv-1`…`-conv-8` |
| `audio` | Audio(16 kHz) | Decoded waveform + sampling rate |
| `transcription` | string | Reference text (conversations: 4 turns joined by spaces) |
| `duration_seconds` | float64 | Clip length, computed from PCM |
| `language` | string | `wuu` |
| `meaning_en` | string | English gloss of the intended meaning |
| `part` | int32 | Category 1–7 |
| `part_name` | string | Category name |
| `kind` | string | `sentence` or `conversation` |
| `item_no` | int32 | Number within its category |
| `needs_verification` | bool | `true` for the 8 flagged items |
| `note` | string | Why an item is flagged (empty otherwise) |
| `turns_json` | string | JSON array of `{speaker, text}` for conversations (empty for sentences) |

Build + push (PCM → float32 in `[-1, 1]`, then `push_to_hub`):

```python
import json
from pathlib import Path
import numpy as np
from datasets import Audio, Dataset, Features, Value

DS = Path("datasets/shanghainese"); PCM = DS / "audio_pcm"; RATE = 16000
rows = [json.loads(l) for l in (DS / "metadata.jsonl").read_text("utf-8").splitlines() if l.strip()]
rows.sort(key=lambda r: (r["kind"] != "sentence", r["item_no"]))

data = {k: [] for k in ["sample_id","audio","transcription","duration_seconds","language",
                        "meaning_en","part","part_name","kind","item_no",
                        "needs_verification","note","turns_json"]}
for r in rows:
    arr = np.frombuffer((PCM / f"{r['sample_id']}.pcm").read_bytes(), np.int16).astype(np.float32) / 32768.0
    data["sample_id"].append(r["sample_id"])
    data["audio"].append({"array": arr, "sampling_rate": RATE})
    data["transcription"].append(r["transcription"])
    data["duration_seconds"].append(float(r["duration_seconds"]))
    data["language"].append(r["language"]); data["meaning_en"].append(r["meaning_en"])
    data["part"].append(int(r["part"])); data["part_name"].append(r["part_name"])
    data["kind"].append(r["kind"]); data["item_no"].append(int(r["item_no"]))
    data["needs_verification"].append(bool(r["needs_verification"]))
    data["note"].append(r.get("note") or "")
    data["turns_json"].append(json.dumps(r["turns"], ensure_ascii=False) if r.get("turns") else "")

features = Features({
    "sample_id": Value("string"), "audio": Audio(sampling_rate=RATE),
    "transcription": Value("string"), "duration_seconds": Value("float64"),
    "language": Value("string"), "meaning_en": Value("string"),
    "part": Value("int32"), "part_name": Value("string"), "kind": Value("string"),
    "item_no": Value("int32"), "needs_verification": Value("bool"),
    "note": Value("string"), "turns_json": Value("string"),
})
Dataset.from_dict(data, features=features).push_to_hub("lvhoang/shanghainese-stt-eval", private=True)
```

## Dataset card

`push_to_hub` auto-generates a `README.md` whose YAML frontmatter holds the
`dataset_info` (features, splits, sizes). The card was then enriched **without
discarding that block**: the existing frontmatter was loaded, the tags below were
merged in, and a documentation body appended, then re-uploaded with
`HfApi.upload_file`.

Frontmatter added:

```yaml
language: [wuu]
task_categories: [automatic-speech-recognition]
pretty_name: Shanghainese STT Evaluation Set
size_categories: [n<1K]
tags: [speech-to-text, asr, shanghainese, wu-chinese, evaluation, negation, voice-agents]
```

The body documents the columns, categories, the ground-truth caveat (8 flagged
items), a usage snippet, and a "license not yet specified" note (the recordings
belong to the dataset owner).

## Load it

```python
from datasets import load_dataset
ds = load_dataset("lvhoang/shanghainese-stt-eval", split="train")
print(ds[0]["transcription"], "—", ds[0]["meaning_en"])

# Slice to the meaning-flip-sensitive negation cases:
negation = ds.filter(lambda r: r["part"] == 2)

# Items still needing human verification:
todo = ds.filter(lambda r: r["needs_verification"])
```

> This is **not English**, so the standard `stt-benchmark download` path (which
> filters its HF source for `language == "eng"`) will not pull it. Load it
> directly as above, or import `ground_truth.jsonl` into a benchmark DB.

## Repository state in git

Only the **reproducible source** is committed to this repo
([`datasets/shanghainese/`](../../datasets/shanghainese/)): `metadata.jsonl`,
`ground_truth.jsonl`, `manifest.csv`, `build.py`, the dataset card, and a
`.gitignore`. The audio binaries (`audio_m4a/`, `audio_pcm/`) and the local
`hf_dataset/` are **gitignored** — the audio of record lives on the Hub.

## Update after verification

```bash
# 1. edit references in datasets/shanghainese/metadata.jsonl (set verified_by: human)
# 2. regenerate the derived artifacts
python3 datasets/shanghainese/build.py
# 3. re-run the enriched build+push snippet above (push_to_hub overwrites in place)
```
