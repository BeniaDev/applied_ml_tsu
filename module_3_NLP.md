# Homework 3

Neural Machine Translation for a Low-Resource Ancient Language & Production-Ready Streaming Translator.

Welcome page: https://www.deeppast.org/

**DEADLINE: --.--.2026**

#### Learn more about NLP:
- https://github.com/yandexdataschool/nlp_course (ru lectures with seminars)
- https://lena-voita.github.io/nlp_course.html
- https://ods.ai/tracks/nlp-course-spring-2025
- https://ods.ai/tracks/df24-nlp
- https://web.stanford.edu/~jurafsky/slp3/
- https://www.philschmid.de/fine-tune-llms-in-2024-with-trl#1-define-our-use-case (https://github.com/philschmid/deep-learning-pytorch-huggingface/blob/main/training/fine-tune-llms-in-2024-with-trl.ipynb)
- https://github.com/philschmid/deep-learning-pytorch-huggingface/blob/main/training/fine-tune-llms-in-2025.ipynb

---

## Background

Most state-of-the-art translation systems (Google Translate, Yandex Translate, DeepL) are trained on billions of parallel sentences for high-resource pairs like English–German or Russian–English. However, the majority of the world's languages — and nearly **all** historical ones — are **low-resource**: they lack large parallel corpora, clean tokenizers, and pretrained checkpoints.

**Akkadian** is the language of ancient Mesopotamia (Babylonia and Assyria), written in cuneiform script on clay tablets between roughly 2500 BCE and 100 CE. Hundreds of thousands of tablets survive in museum collections, but only a small fraction has ever been translated — the bottleneck is the number of assyriologists on Earth. Machine translation for Akkadian therefore has direct cultural-heritage impact: every additional translated tablet is a primary source recovered for historians.

This is a textbook low-resource NMT setting:

- Small parallel corpus (tens of thousands of sentences).
- No pretraining data in the source language for modern LLMs.
- Morphologically rich, agglutinative, free word order.
- Distant from any high-resource anchor language.

In this homework you will go end-to-end: take the Kaggle competition dataset, fine-tune a translation model, apply **pseudo-labeling (back-translation)** on monolingual Akkadian text, and ship a **production-ready streaming translator** similar in UX to Yandex Translate / Google Translate.

## Goal

1. Compete on [**Kaggle — Deep Past Initiative: Machine Translation (Akkadian → English)**](https://www.kaggle.com/competitions/deep-past-initiative-machine-translation/overview).
2. Deploy the trained model as a **real-time streaming translation web application** backed by a high-performance inference engine (**vLLM** or **SGLang**).

Reference architecture for the final deliverable: [AInami-Rei/mansi_translator](https://github.com/AInami-Rei/mansi_translator).

## Task

Your work is split into three roles:

1. **Data Engineer Part** — study and clean the competition corpus; collect monolingual Akkadian text for back-translation.
2. **Deep Learning Engineer Part** — fine-tune a multilingual seq2seq / decoder-only model, apply pseudo-labeling, evaluate with BLEU / chrF++ on dev and submit to Kaggle.
3. **Software Engineer Part** — wrap the model into a streaming web app served via vLLM or SGLang, so the user sees translated tokens appear in real time.

---

## Before We Start

You should **never train from scratch** for such a low-resource pair — always start from a multilingual checkpoint and adapt it.

### Recommended base models

**Strongly recommended baseline — ByT5 (byte-level T5).** All top-4 solutions of the Kaggle Deep Past Challenge used **ByT5** (small / base / large / XL). Akkadian transliteration has diacritics (`š/ṣ/ṭ/ā/ē/ī/ū`), sign indices (`ma₂`, `il₅-qé`), superscripts for determinatives and subscripts — byte-level tokenization handles this out-of-the-box, while BPE vocabularies from NLLB/Llama/Qwen break the orthography into semantically meaningless pieces:

- [google/byt5-small](https://huggingface.co/google/byt5-small) / [byt5-base](https://huggingface.co/google/byt5-base) / [byt5-large](https://huggingface.co/google/byt5-large) — **default choice for this homework**. `small` or `base` is enough for a strong baseline on a single consumer GPU.

**Alternatives (require a justification in the README):**

- [facebook/nllb-200-distilled-600M](https://huggingface.co/facebook/nllb-200-distilled-600M) — 200 languages, but its BPE is poorly suited for Akkadian transliteration.
- [google/madlad400-3b-mt](https://huggingface.co/google/madlad400-3b-mt), [facebook/m2m100_418M](https://huggingface.co/facebook/m2m100_418M).
- [Helsinki-NLP/opus-mt-*](https://huggingface.co/Helsinki-NLP) Marian baselines — e.g. [opus-mt-tiny-rus-eng](https://huggingface.co/Helsinki-NLP/opus-mt_tiny_rus-eng) as a size reference.
- Small modern decoder-only LLM (Qwen2.5-1.5B, Llama-3.2-1B, Gemma-2-2B) fine-tuned with LoRA — simpler path to vLLM/SGLang streaming (see the serving section), but typically lower translation quality.

### Recommended reading

- Gutherz et al., *"Translating Akkadian to English with Neural Machine Translation"* (PNAS Nexus, 2023): <https://academic.oup.com/pnasnexus/article/2/5/pgad096/7147349>
- ByT5 paper: <https://arxiv.org/abs/2105.13626>
- MBR decoding (Minimum Bayes Risk) — Freitag et al., 2022: <https://arxiv.org/abs/2108.10857>
- NLLB paper: <https://arxiv.org/abs/2207.04672>
- "A Survey on Low-Resource NMT": <https://arxiv.org/abs/2107.04239>
- vLLM docs: <https://docs.vllm.ai/> · SGLang docs: <https://docs.sglang.ai/> · HuggingFace TGI (supports encoder-decoder streaming): <https://huggingface.co/docs/text-generation-inference/>
- ORACC — Open Richly Annotated Cuneiform Corpus: <http://oracc.museum.upenn.edu/>

---

## Competition & Data

**Primary competition (mandatory):**
<https://www.kaggle.com/competitions/deep-past-initiative-machine-translation/overview>

- **Direction**: Akkadian (transliterated Latin script) → English.
- **Files**: `train.csv`, `test.csv`, `sample_submission.csv` (check the competition's Data tab for exact columns and sizes).
- **Submission**: predicted English translation for each test `id`.
- **Metric**: see the Evaluation tab (typically BLEU or chrF-based for this kind of competition — follow whatever the competition specifies and report it as your primary number).

**Monolingual data for back-translation** (mandatory — bring your own, do not leak the test set):

- **ORACC** text corpus — hundreds of thousands of Akkadian lines with normalization: <http://oracc.museum.upenn.edu/>
- **CDLI** (Cuneiform Digital Library Initiative): <https://cdli.mpiwg-berlin.mpg.de/>
- **ETCSL** / **ETCSRI** transliterations.
- Any additional open Akkadian parallel data you find (e.g. the Gutherz et al. released corpus) — **as long as you verify it does not overlap with the Kaggle test set**.

You must document in the `README.md` exactly how you obtained, licensed, cleaned, de-duplicated and split the data, and how you guaranteed no test leakage.

---

## Mandatory Experiments & Ablation Report

This section reflects what actually worked for the **top-4 Kaggle solutions** of this exact competition. Instead of blindly applying classical back-translation (which none of the winners used as their hero technique), you must run a **data- and decoding-centric** set of experiments and report a structured ablation.

You must try **at least 3 of the 4 techniques below** and report each as a row in a `Results` table in the README:

| Technique | chrF++ on dev | Kaggle public LB | Notes |

### 1. Orthography normalization (optional)

Akkadian transliteration is wildly inconsistent across publications. Build a normalizer and document the rules you chose. Typical decisions:

- How to handle lacunae / reconstructions: `[...]`, `⸢...⸣`, `<...>`, `{...}` — strip / keep / replace with a special token.
- Sign indices: `ma₂` → `ma2` → `ma` — pick a convention and stick to it.
- Superscripts (determinatives, e.g. `{d}`, `{m}`, `{uru}`) and subscripts (e.g. `il₅-qé`) — normalize markup.
- Diacritics: `š/ṣ/ṭ`, long marks `ā/ē/ī/ū` — keep (recommended for ByT5) or fold.
- Case folding, whitespace normalization, Unicode NFC.

Ablation: metric with normalization vs without. This is the cheapest big win.

### 2. Beam search decoding (optional)

Move past greedy — use beam search with beam size 4–8 and report the dev metric delta.

- Sweep beam size `∈ {1, 4, 8}` and pick the one that wins on dev chrF++.
- Optionally tune `length_penalty` and `no_repeat_ngram_size`.

Ablation: greedy vs beam.

### 3. Mini-ensemble (optional)

Train **at least 2 checkpoints** differing by one of: random seed, training data mix, or model size (e.g. ByT5-base + ByT5-large). Combine their outputs (average logits, or take the best dev-chrF++ candidate per sentence).

Ablation: single model vs ensemble.

### 4. Classical back-translation (optional)

Left in as an option, not the headline technique:

- Train a reverse English → Akkadian model, run it on a monolingual English corpus to produce pseudo-pairs, filter by length ratio / round-trip chrF, train forward on `real ∪ pseudo`.
- Or the opposite direction from monolingual Akkadian (ORACC).

Report size of synthetic corpus, filtering strategy, and the dev metric delta.

### 5. External parallel / monolingual data (optional, must verify license and test-set disjointness)

You may use **any openly available** parallel or monolingual Akkadian corpus, as long as:

- You document where it comes from and its **license** (CC-BY, CC0, public domain, ORACC terms, etc.) in the README.
- You verify it does **not** overlap with the Kaggle test set (exact-match + near-duplicate check). Leakage = grade 0.

Common sources: ORACC, CDLI, ETCSL/ETCSRI, the Gutherz et al. released corpus, public Akkadian lexicon dumps.

---

### What you should report in the README

- Final `Results` ablation table (see above).
- Size of the real parallel corpus (Kaggle train) and of any added external data, with licenses.
- Decoding configuration (N candidates, temperatures, selector).
- Ensemble composition (checkpoints, training diffs).
- Kaggle public & private leaderboard scores (with a screenshot).

---

## Metrics

On the Kaggle test set you report the **competition's official metric** (Kaggle public/private leaderboard score). On your own held-out dev split additionally report:

- **BLEU** (sacreBLEU, signature `nrefs:1|case:mixed|eff:no|tok:13a|smooth:exp`).
- **chrF++** (sacreBLEU `--metric chrf --chrf-word-order 2`) — primary proxy metric for a morphologically rich language.
- **COMET** (`Unbabel/wmt22-comet-da` or newer) — note: COMET was not trained on Akkadian, treat as auxiliary.
- **Latency** of the deployed streaming service: median and p95 **time-to-first-token (TTFT)** and **tokens-per-second**, measured with `wrk` / `locust` / a custom client.

> Why not only BLEU? For morphologically rich and distant languages BLEU underestimates quality — chrF++ is a far better proxy.

---

## Production-Ready Streaming Translator

This is the highlight of the homework. You must ship a **Yandex-Translate-like** web application with real-time streaming.

### Functional requirements

1. A web UI (any stack: React / Vue / Svelte / plain HTML + HTMX — we grade that streaming works, not the visual design).
2. Two text areas: Akkadian (transliteration) on the left, English on the right.
3. As the user types (debounced) or submits, tokens of the translation **stream** into the right pane — no full-request blocking.
4. Health-check endpoint `GET /health`.
5. A `POST /translate` endpoint that accepts `{text}` and returns a **Server-Sent Events (SSE)** or **WebSocket** stream of tokens.

### Inference backend — choose ONE

- **[vLLM](https://github.com/vllm-project/vllm)** — OpenAI-compatible server, `stream=True`, PagedAttention, continuous batching. Best for decoder-only LLMs (Qwen / Llama / Gemma).
- **[SGLang](https://github.com/sgl-project/sglang)** — RadixAttention, typically lower TTFT. Same model-support profile as vLLM.
- **[HuggingFace TGI](https://huggingface.co/docs/text-generation-inference/)** — supports **encoder-decoder streaming** (T5 / ByT5 / mT5 / NLLB), which is important if you stick with the recommended ByT5 baseline.

### Two valid deployment paths

There is a real trade-off between **translation quality** (ByT5 is clearly better for Akkadian) and **serving ergonomics** (vLLM/SGLang were designed for decoder-only LLMs). Pick one:

- **Path A — ByT5 + TGI** (recommended). Best quality, smallest gap between your DS work and prod. TGI streams tokens from encoder-decoder models natively.
- **Path B — LoRA on a small decoder-only LLM (Qwen2.5-1.5B / Llama-3.2-1B / Gemma-2-2B) + vLLM or SGLang**. Simpler serving stack; typically lower chrF++ than ByT5. You must still report the same ablation table from the previous section.

Document in the README which path you chose and why.

### Deployment

- `Dockerfile` for the inference service (vLLM/SGLang + weights).
- `Dockerfile` for the web frontend/gateway.
- `docker-compose.yaml` that brings up the whole stack in one command:

```console
foo@bar:~$ docker compose up
foo@bar:~$ open http://localhost:8080
```

- GPU and CPU-only profiles in compose (CPU profile may use a quantized model).

Reference architecture: [AInami-Rei/mansi_translator](https://github.com/AInami-Rei/mansi_translator).

---

## Technical Requirements

We do not accept the homework unless **all** of the following are satisfied:

- **Git policy**: code in a public `GitHub` / `GitLab` repo. Two branches: `develop` (or `dev`) and `main` (or `master`). Meaningful commit messages, no last-minute commit rush.
- **Dependency management**: [Poetry](https://python-poetry.org/) or [UV](https://github.com/astral-sh/uv). A `.whl` of your package (or a committed `poetry.lock` / `uv.lock`) must be in the repo.
- **`README.md`**:
    - Your full name and group number.
    - "How-to": train baseline, run the mandatory experiments, train final model, evaluate, submit to Kaggle, launch service (with and without Docker).
    - Final metrics: **Kaggle public & private leaderboard score**, plus BLEU / chrF++ / COMET on your dev split, plus TTFT and tokens/sec.
    - The **ablation `Results` table** from the "Mandatory Experiments" section.
    - List of **all datasets used with their licenses** (Kaggle data + any external corpora).
    - **Screenshot of your Kaggle submission** on the leaderboard.
    - **GIFs or a short video** demonstrating the streaming UI.
    - Resources used (datasets, pretrained weights, papers).
- **Logging**: full pipeline (data loading, training, decoding, serving, request handling, errors) logged to `./data/log_file.log`. Must be viewable and downloadable inside the container.
- **`.gitignore`**: no weights, no raw corpora, no `__pycache__`, no `.venv`. Model weights go to Hugging Face Hub or ClearML/W&B artifacts.
- **Main software artifact**: `model.py` containing the class `My_Translator_Model` with:
    - `train(dataset_path: str) -> None` — trains the forward model, saves to `./model/`, logs everything.
    - `predict(text: str, stream: bool = True) -> Iterator[str] | str` — translates one string; when `stream=True`, yields tokens. Under the hood must support multi-candidate decoding + selector.
    - `predict_file(dataset_path: str) -> None` — loads `./model/`, saves Kaggle-format predictions to `./data/results.csv`.
- **CLI** (e.g. via [Fire](https://google.github.io/python-fire/guide/) or `argparse`):

```console
foo@bar:~$ python model.py train --dataset=/path/to/train.csv
foo@bar:~$ python model.py predict --text="šarrum ana ālim illik"
foo@bar:~$ python model.py predict-file --dataset=/path/to/test.csv
```

- **Experiment tracking**: all training runs logged to **ClearML** or **Weights & Biases** (losses, dev metrics, hyperparameters, final weights as artifacts).
- **Deployment**: `Dockerfile`(s) and `docker-compose.yaml` for the streaming translator.

---

## Project Milestones

1. **Data** — download the Kaggle corpus, study it, de-duplicate, prepare a dev split disjoint from the Kaggle test set.
2. **Baseline** — fine-tune **ByT5-small/base** (or justified alternative) on Kaggle `train.csv` only, no normalization, greedy decoding. Submit to Kaggle. Record chrF++ on dev.
3. **Mandatory experiments** — run at least 3 of the 4 techniques from the "Mandatory Experiments" section (orthography normalization, beam search decoding, mini-ensemble, optional classical back-translation, optional external open-license data). Fill in the ablation `Results` table after each experiment.
4. **Packaging** — `model.py` + CLI + Poetry/UV, ClearML / W&B artifacts.
5. **Serving** — Path A (ByT5 + TGI) or Path B (LoRA-LLM + vLLM/SGLang) + streaming web UI + Docker Compose.
6. **Demo** — GIFs in README, measured TTFT and tokens/sec, Kaggle leaderboard screenshot.

---

## Grades

### DS / NLP Part

The DS grade rewards a **structured ablation report** over a raw leaderboard position. Cheating the metric is easy — honest ablations are not.

**Kaggle leaderboard (score-based tiers, metric = Geometric Mean of BLEU and chrF++):**

| Points | Score threshold     | Description                                                              |
|--------|---------------------|--------------------------------------------------------------------------|
| 0      | Score < 35.9        | No submission, broken submission, or below the minimum graded threshold  |
| 20     | Score ≥ 35.9        | Working fine-tune of a recommended model                                 |
| 30     | Score ≥ 39.0        | Strong solution, close to public SOTA                                    |

**Ablation report & experiments:**

| Points | Bulletpoint                                | Description                                                                                                                                    |
|--------|--------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| 10     | Orthography normalization (mandatory)      | Documented rules, ablated with and without.                                                                                                     |
| 10     | Beam search decoding (mandatory)           | Beam size sweep, ablation vs greedy, report best config on dev chrF++.                                                                          |
| 10     | Mini-ensemble (mandatory)                  | ≥ 2 checkpoints combined (logit averaging or per-sentence best-dev pick), ablation vs single model.                                             |
| 5      | Full metric suite                          | Kaggle score + BLEU + chrF++ + COMET, reported on dev and test.                                                                                 |

**Total: 65 points**

> Cheating with metrics (training on dev/test, leaderboard probing beyond the allowed daily quota, leakage from external corpora into the test set) leads to grade **0**.

### Software Engineer Part

| Points | Bulletpoint            | Description                                                                                                       |
|--------|------------------------|-------------------------------------------------------------------------------------------------------------------|
| 10     | Streaming inference    | Working vLLM / SGLang / TGI backend. Tokens streamed via SSE/WebSocket to the UI. TTFT reported.                  |
| 5      | Web UI                 | Yandex/Google-Translate-like two-pane UI, debounced input, visible realtime streaming.                            |
| 7      | Docker Compose         | `docker compose up` brings up the full stack (inference + gateway + UI).                                          |
| 3      | `model.py`             | Class `My_Translator_Model` with `train`, `predict(stream=True)`, `predict_file`. CLI works.                      |
| 3      | Poetry / UV            | `.whl` installable, or `poetry.lock` / `uv.lock` committed and reproducible.                                      |
| 3      | ClearML / W&B          | Training runs, hyperparameters, dataset and best-model artifacts logged.                                          |
| 2      | Logging                | Singleton logger, errors caught and logged, log file accessible inside the container.                             |
| 2      | Git workflow           | Public repo, `develop` + `main`, meaningful commits, no commit rush.                                              |

**Total: 35 points**

---

## Bonus Part

Up to **20 bonus points** for any of:
- **Quantization** of the served model (AWQ / GPTQ / bitsandbytes / FP8) with measured TTFT / throughput improvement and score delta.
- **Paper review** in the style of [DS Talks Siberia](https://t.me/+fQ07VSVJ2V8yZGYy) on Gutherz et al. 2023, the ByT5 paper, MBR decoding, or another low-resource MT paper.
