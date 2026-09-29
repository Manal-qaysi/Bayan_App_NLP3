# بطاقة نموذج بيان | Bayan Model Card

## Model details

* Name/version: `Bayan Topic Classifier v1`
* Base checkpoint: `distilbert/distilbert-base-multilingual-cased`
* Task: `Multilingual Topic Classification`
* License/source: `Educational course project / Hugging Face base checkpoint`
* Commit SHA: `Not recorded`
* Owner/contact role: `Project owner / Applied NLP trainee`

## Intended use

* الاستخدام المقصود: تصنيف النصوص العربية والإنجليزية إلى موضوعات خدمية محددة.
* المستخدمون المقصودون: المتدربون والباحثون في مجال معالجة اللغة الطبيعية لأغراض تعليمية وتجريبية.
* خارج النطاق: الاستخدام الإنتاجي، اتخاذ القرارات بشأن أشخاص حقيقيين، profiling، أو أي قرار عالي المخاطر.

## Data and preprocessing

* Dataset ID/version: `Bayan Day 2 Classification Dataset`
* Languages/variants: `Arabic + English`
* Split strategy: `Train 24 / Validation 8 / Frozen Test 8`, with `20 groups` and `group_overlap = 0`
* PII policy: البيانات التعليمية اصطناعية ولا تتضمن بيانات أشخاص حقيقيين؛ يتم تطبيق masking للبريد الإلكتروني وأرقام الجوال ضمن preprocessing عند الحاجة.
* Preprocessing profile/version/backend: `Conservative Arabic preprocessing / Python preprocessing pipeline`
* Tokenizer/embedding model: `distilbert/distilbert-base-multilingual-cased tokenizer`

## Evaluation

| metric/slice           |  n |                    result | uncertainty                       | evidence file          |
| ---------------------- | -: | ------------------------: | --------------------------------- | ---------------------- |
| Macro-F1 — frozen test |  8 |                **0.8667** | CI not calculated; small test set | `EVALUATION_REPORT.md` |
| Accuracy — frozen test |  8 |                 **0.875** | CI not calculated; small test set | `EVALUATION_REPORT.md` |
| Arabic slice           |  4 | Not separately calculated | Very small slice                  | `EVALUATION_REPORT.md` |
| English slice          |  4 | Not separately calculated | Very small slice                  | `EVALUATION_REPORT.md` |

**Baseline reference:** TF-IDF char_wb + LinearSVC achieved **0.7333 Macro-F1** on the same test split.

## Behavioural checks

| capability                                   |      pass rate | known failure                   |
| -------------------------------------------- | -------------: | ------------------------------- |
| Minimum functionality / core notebook checks |          `1/1` | No known core execution failure |
| Invariance                                   | `Not measured` | Not evaluated                   |
| Directional                                  | `Not measured` | Not evaluated                   |

## Limitations and risks

1. The frozen test set contains only **8 examples**, so the measured result has substantial uncertainty.
2. The dataset is synthetic and educational, so it may not represent real-world service-related text.
3. Arabic dialects and Arabizi are not sufficiently represented, and separate language-slice confidence intervals were not calculated.

## Ethical and privacy notes

* The dataset is intended for educational experimentation and does not represent real people or real cases.
* PII masking is included in the preprocessing workflow for emails and Saudi mobile numbers.
* The model must not be treated as a production decision-making system or used for profiling real individuals.
* Current evaluation results do not constitute a production performance or safety claim.

## Reproduction

1. افتح notebook: `Day 2 Lab 3A — Text Classification`.
2. استخدم runtime/device: `Python 3.12.13 / CPU`.
3. ثبت النسخ: `transformers 5.15.1`, `tokenizers 0.22.2`, `scikit-learn 1.9.0`.
4. شغّل Run all من commit: `Commit SHA not recorded`.
5. قارن النتيجة مع: `Macro-F1 = 0.8667` و`Accuracy = 0.875` على frozen test set.
