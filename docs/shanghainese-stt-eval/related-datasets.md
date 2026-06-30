# Related Datasets & Benchmarks

External, public resources relevant to Shanghainese / Wu Chinese STT. These
complement our small, purpose-built eval set ([source data](source-data.md)) with
large-scale training data and broader benchmarks.

---

## WenetSpeech-Wu

> 🤗 [`ASLP-lab/WenetSpeech-Wu`](https://huggingface.co/datasets/ASLP-lab/WenetSpeech-Wu)
> · 🧪 Benchmark: [`ASLP-lab/WenetSpeech-Wu-Bench`](https://huggingface.co/datasets/ASLP-lab/WenetSpeech-Wu-Bench)
> · 🐙 [GitHub](https://github.com/ASLP-lab/WenetSpeech-Wu-Repo)
> · 📑 [Paper (arXiv:2601.11027)](https://arxiv.org/abs/2601.11027)
> · 🎤 [Demo](https://hujingbin1.github.io/WenetSpeechWu-Demo-Page-Public/)

**The first large-scale Wu dialect speech corpus with multi-dimensional
annotations**, from ASLP@NPU (Northwestern Polytechnical University) with AISHELL
and the WeNet community.

| | |
|---|---|
| **Size** | ~8,000 hours of speech, diverse domains |
| **Coverage** | 8 Wu sub-dialects (includes **Shanghainese**, Suzhounese, …) |
| **Annotations** | Transcriptions with confidence scores, Wu→Mandarin translations, domain & sub-dialect labels, speaker attributes (gender/age), emotion, audio-quality measures |
| **Quality** | Task-specific data-quality grading (different tasks draw different quality tiers) |
| **License** | Apache-2.0 |
| **HF language tag** | `zh` (corpus is Wu-dialect Chinese) |
| **Released** | 2026 (Wang et al.) |

The project also ships fine-tuned **ASR/understanding models**
([WSWu-Understanding](https://huggingface.co/ASLP-lab/WenetSpeech-Wu-Speech-Understanding))
and **TTS/instruct-TTS models**
([WSWu-Generation](https://huggingface.co/ASLP-lab/WenetSpeech-Wu-Speech-Generation)).

### WenetSpeech-Wu-Bench

The first publicly available, manually curated benchmark for Wu dialect speech:

| Task | Data | Metric |
|------|------|--------|
| **ASR** | 9.75 h — Shanghainese, Suzhounese, and Mandarin code-mixed | CER |
| **Wu→Mandarin AST** | 3k utterances (4.4 h) | BLEU |
| **Speaker & Emotion** | gender / age prediction, emotion recognition | accuracy |
| **TTS** | 242 sentences, 12 speakers | SIM, CER, MOS |
| **Instruct TTS** | prosody / emotion control | accuracy, MOS |

### Why this matters here

Their published ASR leaderboard (CER %, lower is better) on the **WS-Wu-Bench ASR**
set is strong corroboration for this project's premise — that generic and even
frontier general-purpose ASR degrades badly on Wu, while Wu-specialized models
close most of the gap:

| Model | WS-Wu-Bench ASR (CER %) |
|-------|------------------------:|
| Gemini-2.5-pro | 89.99 |
| Whisper-medium | 78.24 |
| Paraformer | 64.92 |
| SenseVoice-small | 46.85 |
| Qwen3-ASR | 29.31 |
| Tencent-Cloud-ASR | 29.48 |
| Step-Audio2-mini | 26.72 |
| Conformer-U2pp-Wu ⭐ (their FT) | 15.14 |
| Whisper-medium-Wu ⭐ (their FT) | 14.33 |
| Step-Audio2-Wu-ASR ⭐ (their FT) | **12.85** |

*(Subset of their table; ⭐ = models fine-tuned on WenetSpeech-Wu. Numbers from
the WenetSpeech-Wu paper, not measured by this repo.)*

### How we can use it

- **Bigger / independent eval:** WS-Wu-Bench ASR is a larger, manually curated
  Shanghainese-inclusive test set. Note differences from our set: it is **CER**-scored
  (character-level, the right metric for Chinese) and **code-mixed** with Suzhounese
  and Mandarin, whereas ours is pure-Shanghainese, sentence/conversation-level,
  with English glosses and a meaning-flip (negation/numbers/medical) focus.
- **Training data:** the ~8,000 h corpus is a candidate fine-tuning source if we
  ever train or adapt a Shanghainese model.
- **Baselines to run against our set:** their open models (Whisper-medium-Wu,
  Conformer-U2pp-Wu, Step-Audio2-Wu-ASR) are natural Wu-specialized baselines to
  transcribe our clips and compare against the generic services.
- **Metric note:** for Chinese-character references, prefer **CER** over word-level
  WER; our [semantic-WER](../analysis.md) approach still applies for measuring
  meaning-changing errors.

```python
from datasets import load_dataset
ws_wu_bench = load_dataset("ASLP-lab/WenetSpeech-Wu-Bench")  # broader Wu ASR benchmark
```

**Citation**

```bibtex
@misc{wang2026wenetspeechwudatasetsbenchmarksmodels,
  title  = {WenetSpeech-Wu: Datasets, Benchmarks, and Models for a Unified Chinese Wu Dialect Speech Processing Ecosystem},
  author = {Chengyou Wang and Mingchen Shao and Jingbin Hu and Zeyu Zhu and Hongfei Xue and Bingshen Mu and Xin Xu and Xingyi Duan and Binbin Zhang and Pengcheng Zhu and Chuang Ding and Xiaojun Zhang and Hui Bu and Lei Xie},
  year   = {2026},
  eprint = {2601.11027},
  archivePrefix = {arXiv},
  primaryClass  = {cs.SD},
  url    = {https://arxiv.org/abs/2601.11027}
}
```
