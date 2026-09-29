# BENCHMARKS — Bayan

> انسخ هذا الملف إلى جذر مشروعك باسم `BENCHMARKS.md`، ثم استبدل كل `FILL_ME`.  
> Copy this file to the project root as `BENCHMARKS.md`, then replace every `FILL_ME`.

## 1. Claim boundary | حدود الادعاء

- Artefact role: `Bayan Applied NLP`
- Result label: `MEASURED`
- Task: Multilingual topic text classification
- Decision date: 2026-09-29T12:47:20.587378+00:00
- Author: Manal

لا تستخدم `PROJECT_ARTIFACT` أو `MEASURED` إن كنت ما زلت تشغّل checkpoint مسار Systems Smoke.

## 2. Performance budget — written before candidates

| Constraint | TARGET | Why this matters |
|---|---:|---|
| p95 end-to-end latency |100 ms | Target responsive inference  |
| minimum throughput | 20 items/s | Ensure sufficient CPU serving capacity |
| maximum quality tax | 0.02 macro-F1 | Limit quality degradation after optimisation |
| target device | CPU | Reproducible CPU deployment |

- Commit/time proving budget existed before candidate: 2026-09-29

## 3. Reproduction contract

| Field | Value |
|---|---|
| Colab runtime/Python | Python 3.12.13 |
| Device/provider | CPU |
| CPU/GPU details | Processor: 
Machine: wasm32
CPU count: 1 |
| Library versions | torch = 2.11.0+cpu
transformers = 5.15.1
tokenizers = 0.22.2
scikit-learn = 1.9.0
onnx = NOT INSTALLED
onnxruntime = NOT INSTALLED |
| Model ID/revision/hash | distilbert/distilbert-base-multilingual-cased |
| Preprocessing version | v1.0 |
| Label map version | v1.0 |
| Workload path/hash | data/sample/bayan_day2_classification.csv |
| Split | validation / frozen test: test |
| Examples + AR/EN counts | Total samples: 5
Sample types: Counter({'Arabic': 3, 'English/Other': 2})
Processed texts: 5
Tokenized texts: 5 |
| Length distribution | p50: 12.0,p95: 12.0,
max: 12 |
| Batch size | 4 |
| Padding/max length |  Dynamic padding / MAX_LENGTH=64 |
| Warm-up/repetitions |  10 / 30 |
| Measured boundary | model-only / end-to-end: model-only |
| Memory method | process RSS observed peak |

## 4. Controlled candidates

| ID | Runtime/precision | Only intended change | Artefact hash | Size MiB |
|---|---|---|---|---:|
| A | PyTorch FP32 reference | baseline  |
| B | ONNX Runtime FP32 | runtime/export  |
| C | ONNX Runtime dynamic INT8 | weight quantisation  |

## 5. Parity

| Comparison | max abs logits diff | mean abs diff | prediction agreement | Verdict |
|---|---:|---:|---:|---|
| A vs B | FILL_ME | FILL_ME | FILL_ME | PASS/FAIL: FILL_ME |
| A vs C | FILL_ME | FILL_ME | FILL_ME | PASS/FAIL: FILL_ME |

- Tolerance chosen before inspection: FILL_ME
- Rationale: FILL_ME

## 6. Performance results

| ID | p50 ms | p95 ms | p99 ms | items/s | observed peak RSS MiB | speedup vs A |
|---|---:|---:|---:|---:|---:|---:|
| A | FILL_ME | FILL_ME | FILL_ME | FILL_ME | FILL_ME | 1.00× |
| B | FILL_ME | FILL_ME | FILL_ME | FILL_ME | FILL_ME | FILL_ME |
| C | FILL_ME | FILL_ME | FILL_ME | FILL_ME | FILL_ME | FILL_ME |

## 7. Quality results

- Primary task metric: macro-F1
- Evaluation file/split: frozen test

| ID | Task quality | Quality tax = A − candidate | Small-sample/CI note |
|---|---:|---:|---|
| A | FILL_ME | 0 | FILL_ME |
| B | FILL_ME | FILL_ME | FILL_ME |
| C | FILL_ME | FILL_ME | FILL_ME |

## 8. Budget verdict and decision

| Candidate | latency OK | throughput OK | quality OK | Overall |
|---|---|---|---|---|
| B | FILL_ME | FILL_ME | FILL_ME | FILL_ME |
| C | FILL_ME | FILL_ME | FILL_ME | FILL_ME |

- Selected runtime: FILL_ME
- Decision: **ADOPT / REJECT / KEEP FP32** — FILL_ME
- Evidence-based reason: FILL_ME
- Known limitation/noise source: FILL_ME
- FP32 rollback/reproduction path: FILL_ME
- Generated JSON report: `reports/benchmark_results.json`

## 9. Reproduction commands

pip install onnx onnxruntime
python benchmark.py

python benchmark.py --model A
python benchmark.py --model B
python benchmark.py --model C

## 10. Integrity check

- [ ] Budget predates candidate results.
- [ ] Same workload/device/batch/boundary used.
- [ ] Warm-up excluded.
- [ ] At least 30 measured repetitions or limitation explained.
- [ ] p50/p95/p99 and throughput included.
- [ ] Memory wording matches measurement method.
- [ ] Quality tax uses the same examples.
- [ ] Failed/slower candidates were not hidden.
- [ ] Numbers are `MEASURED`, not copied references.
- [ ] No weights, ONNX artefacts, cache, secrets, or PII committed.
