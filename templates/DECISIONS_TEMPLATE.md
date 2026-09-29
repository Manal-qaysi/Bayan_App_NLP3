# DECISIONS — Bayan

> انسخ القالب إلى `DECISIONS.md`. أضف قرارًا جديدًا لكل تغيير يؤثر في البيانات أو الجودة أو الخدمة.

## Decision D-001 — Dataset split and leakage control

* **Date:** 2026-09-29
* **Gate:** A — Data and evaluation
* **Status:** accepted
* **Owner:** Manal Qaysi

### Context | السياق

The dataset is a small bilingual educational classification dataset with 40 examples. Arabic and English examples can represent the same underlying scenario, so related examples must not be distributed across different splits. The train/validation/test structure remains fixed at 24/8/8 examples.

### Options considered | البدائل

| Option                     | Benefit                                        | Cost/risk                                               | Evidence                     |
| -------------------------- | ---------------------------------------------- | ------------------------------------------------------- | ---------------------------- |
| A — Group-isolated split   | Prevents related examples from crossing splits | Reduces the effective independence of the small dataset | 20 groups; group overlap = 0 |
| B — Row-level random split | Simple and easy to implement                   | May introduce leakage between related examples          | Not selected                 |

### Decision | القرار

Use the fixed train/validation/test split with group isolation. Keep related bilingual examples in the same split and evaluate the frozen test set only after the model configuration is fixed.

### Evidence | الدليل

* Report/test/commit: Day 2 classification notebook
* Metric and result label: `group_overlap = 0`; split = 24 train / 8 validation / 8 test
* Slice or failure considered: Arabic/English paired examples and small test-set size

### Consequences and rollback | الأثر والرجوع

* Positive consequence: Reduces leakage risk and makes the evaluation more controlled.
* Limitation/new risk: The dataset is small, so metric uncertainty remains high.
* Rollback trigger: Discovery of incorrect group assignments or leakage between splits.
* Rollback path: Restore the versioned dataset and regenerate the split before retraining.

---

## Decision D-002 — Tokenizer and maximum sequence length

* **Date:** 2026-09-29
* **Gate:** A — Text preparation
* **Status:** accepted
* **Owner:** Manal Qaysi

### Context | السياق

The NLP pipeline requires consistent tokenization and a fixed maximum sequence length. The project uses a multilingual Transformer tokenizer and a bounded sequence length to control memory and inference cost.

### Options considered | البدائل

| Option                                                | Benefit                                                         | Cost/risk                                  | Evidence                  |
| ----------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------ | ------------------------- |
| A — Multilingual Transformer tokenizer, max length 64 | Supports Arabic and English with a fixed computational boundary | Long inputs may be truncated               | Day 2 model configuration |
| B — Unbounded sequence length                         | Preserves all tokens                                            | Increases memory and latency unpredictably | Not selected              |

### Decision | القرار

Use `distilbert/distilbert-base-multilingual-cased` with `MAX_LENGTH = 64` for the classification experiment.

### Evidence | الدليل

* Report/test/commit: Day 2 classification notebook
* Metric and result label: Transformer test macro-F1 = 0.8667
* Slice or failure considered: Long inputs may require truncation

### Consequences and rollback | الأثر والرجوع

* Positive consequence: Provides a fixed and reproducible input boundary.
* Limitation/new risk: Information beyond the maximum length may be removed.
* Rollback trigger: Evidence that truncation materially harms task quality.
* Rollback path: Change the maximum length, rerun validation and test evaluation, and record a new decision.

---

## Decision D-003 — Arabic preprocessing profile

* **Date:** 2026-09-29
* **Gate:** A — Data preparation
* **Status:** accepted
* **Owner:** Manal Qaysi

### Context | السياق

Arabic text requires Unicode and whitespace normalization while preserving useful linguistic information. Aggressive normalization can remove distinctions that may be useful to a multilingual model.

### Options considered | البدائل

| Option                              | Benefit                                                                     | Cost/risk                           | Evidence                   |
| ----------------------------------- | --------------------------------------------------------------------------- | ----------------------------------- | -------------------------- |
| A — Conservative normalization      | Preserves most original information while handling common formatting issues | Some orthographic variation remains | Day 1 preprocessing checks |
| B — Aggressive Arabic normalization | Reduces more surface variation                                              | May remove meaningful distinctions  | Not selected               |

### Decision | القرار

Use conservative preprocessing with Unicode NFC normalization, HTML unescaping/removal, tatweel removal, whitespace normalization, and optional PII masking. Diacritic removal and alef normalization remain optional rather than mandatory.

### Evidence | الدليل

* Report/test/commit: Day 1 preprocessing notebook
* Metric and result label: Core preprocessing checks = PASS
* Slice or failure considered: Arabic Unicode handling, whitespace, and PII masking

### Consequences and rollback | الأثر والرجوع

* Positive consequence: Provides a reproducible Arabic preprocessing profile while preserving the original display text.
* Limitation/new risk: Some Arabic dialect and spelling variation remains.
* Rollback trigger: Evidence that preprocessing reduces validation quality.
* Rollback path: Restore the previous preprocessing version and rerun the evaluation.

---

## Decision D-004 — Classification baseline and Transformer model

* **Date:** 2026-09-29
* **Gate:** B — Model quality
* **Status:** accepted
* **Owner:** Manal Qaysi

### Context | السياق

The project needs a simple baseline and a multilingual Transformer model so that quality can be compared using the same classification task and split.

### Options considered | البدائل

| Option                                   | Benefit                                                   | Cost/risk                       | Evidence                                 |
| ---------------------------------------- | --------------------------------------------------------- | ------------------------------- | ---------------------------------------- |
| A — TF-IDF character n-grams + LinearSVC | Fast and interpretable baseline                           | Lower contextual understanding  | Test macro-F1 = 0.7333                   |
| B — Multilingual DistilBERT fine-tuning  | Captures contextual information across Arabic and English | Higher compute and latency cost | Test macro-F1 = 0.8667; accuracy = 0.875 |

### Decision | القرار

Keep the TF-IDF + LinearSVC pipeline as the reference baseline and use the multilingual DistilBERT model as the Transformer classification candidate.

### Evidence | الدليل

* Report/test/commit: Day 2 classification evaluation
* Metric and result label: Baseline test macro-F1 = 0.7333; Transformer test macro-F1 = 0.8667
* Slice or failure considered: Small bilingual test set

### Consequences and rollback | الأثر والرجوع

* Positive consequence: Provides a measurable quality comparison between a lightweight baseline and a Transformer.
* Limitation/new risk: The dataset is small, so the observed quality difference should not be interpreted as production performance.
* Rollback trigger: New evaluation shows the Transformer does not meet the required quality or performance budget.
* Rollback path: Keep the TF-IDF + LinearSVC baseline and revert the served model configuration.

---

## Decision D-005 — Performance budget

* **Date:** 2026-09-29
* **Gate:** C — Serving performance
* **Status:** proposed
* **Owner:** Manal Qaysi

### Context | السياق

The serving decision requires latency, throughput, memory, and quality measurements on a controlled device and workload. These measurements have not yet been completed for all runtime candidates.

### Options considered | البدائل

| Option                                           | Benefit                                | Cost/risk                                     | Evidence                  |
| ------------------------------------------------ | -------------------------------------- | --------------------------------------------- | ------------------------- |
| A — Establish and measure an explicit CPU budget | Enables reproducible serving decisions | Requires benchmark runs                       | Benchmark results pending |
| B — Select a runtime without measurement         | Faster decision                        | No evidence for latency/throughput trade-offs | Not selected              |

### Decision | القرار

Define the performance budget before accepting an optimized runtime. Do not mark ONNX or INT8 as adopted until controlled measurements are available.

### Evidence | الدليل

* Report/test/commit: `BENCHMARKS.md`
* Metric and result label: p50/p95/p99 latency, throughput, memory, and quality tax are pending measurement
* Slice or failure considered: CPU serving workload and Transformer classification workload

### Consequences and rollback | الأثر والرجوع

* Positive consequence: Prevents an unmeasured runtime optimization from being treated as a verified improvement.
* Limitation/new risk: Runtime adoption remains pending.
* Rollback trigger: Candidate fails latency, throughput, memory, or quality requirements.
* Rollback path: Keep the FP32 PyTorch reference runtime.

---

## Decision D-006 — ONNX / INT8 runtime adoption

* **Date:** 2026-09-29
* **Gate:** D — Runtime optimization
* **Status:** proposed
* **Owner:** Manal Qaysi

### Context | السياق

ONNX Runtime FP32 and dynamic INT8 are candidate serving runtimes. Adoption requires both performance measurements and quality-parity evidence.

### Options considered | البدائل

| Option                     | Benefit                         | Cost/risk                      | Evidence |
| -------------------------- | ------------------------------- | ------------------------------ | -------- |
| A — PyTorch FP32 reference | Simple reference implementation | May have higher latency/memory | Referen  |


---

## قرارات إلزامية قبل Gate E

- [ ] tokenizer + max length.
- [ ] Arabic preprocessing profile.
- [ ] task model/baseline and split.
- [ ] semantic encoder/index/k/threshold.
- [ ] metric/slices/error priorities.
- [ ] performance budget.
- [ ] ONNX/INT8 adopt or reject.
- [ ] served artefact + preprocessing/label versions.
