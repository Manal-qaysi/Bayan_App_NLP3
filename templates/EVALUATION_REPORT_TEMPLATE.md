# تقرير تقييم بيان | Bayan Evaluation Report

## 1. نطاق التقرير

* تاريخ التشغيل: `2026-09-29`
* commit SHA: `Not recorded`
* runtime/device: `Python 3.12.13 / CPU`
* data version/hash: `Bayan Day 2 Classification Dataset / exact hash not recorded`
* preprocessing profile/version/backend: `Conservative Arabic preprocessing / project preprocessing pipeline / Python`
* model/checkpoint IDs: `TF-IDF + LinearSVC baseline; distilbert/distilbert-base-multilingual-cased`
* نوع الأرقام: `MEASURED_SMOKE`

  > النتائج الحالية مقاسة على تشغيل تجريبي للمشروع وليست benchmark إنتاجيًا شاملاً.

## 2. العقود قبل القياس

| العقد                                             | الدليل                                                                                       | الحالة |
| ------------------------------------------------- | -------------------------------------------------------------------------------------------- | ------ |
| لا PII حقيقية                                     | Dataset uses synthetic educational examples; PII masking is included in preprocessing        | PASS   |
| train/validation/test بلا leakage                 | 24 train / 8 validation / 8 test; 20 groups; `group_overlap = 0`                             | PASS   |
| tokenizer/model متطابقان                          | Multilingual DistilBERT tokenizer used with the corresponding model                          | PASS   |
| Arabic profile متطابقة في train/index/query/serve | Classification experiment uses the same preprocessing pipeline for the evaluated text inputs | PASS   |
| corpus/query embeddings مطبعة L2                  | Not applicable to the implemented classification task                                        | N/A    |
| frozen test لم يستخدم في tuning                   | Test set used for final evaluation after model selection                                     | PASS   |

## 3. نتائج المهام

| المهمة         | المقياس الرئيس    |    النتيجة | CI/تكرار       | مجموعة القياس        |
| -------------- | ----------------- | ---------: | -------------- | -------------------- |
| Classification | Macro-F1          | **0.8667** | Not calculated | Frozen test set, n=8 |
| NER            | strict entity F1  |        N/A | N/A            | Not implemented      |
| QA             | EM/F1 + no-answer |        N/A | N/A            | Not implemented      |
| Retrieval      | Recall@k / MRR@k  |        N/A | N/A            | Not implemented      |

**Additional classification result:** Transformer accuracy = **0.875**.

**Baseline:** TF-IDF character n-grams + LinearSVC achieved **0.7333 Macro-F1** on the same test split.

## 4. شرائح التقييم

| المهمة         | الشريحة        |   n |   metric | 95% CI         | التحذير/التفسير                                  |
| -------------- | -------------- | --: | -------: | -------------- | ------------------------------------------------ |
| Classification | `language=ar`  |   4 | Macro-F1 | Not calculated | Very small slice                                 |
| Classification | `language=en`  |   4 | Macro-F1 | Not calculated | Very small slice                                 |
| Classification | `variant=Gulf` | N/A |      N/A | N/A            | Gulf-specific slice was not separately annotated |
| Classification | `length=long`  | N/A |      N/A | N/A            | Long-text slice was not formally defined         |

## 5. مقارنة الإصدارات

* Model A: `TF-IDF char_wb + LinearSVC`
* Model B: `distilbert/distilbert-base-multilingual-cased`
* observed difference B−A: **+0.1334 Macro-F1**
* paired 95% CI: `Not calculated`
* القرار المهني: `The observed test-set difference supports a measured improvement in this experiment, but the small test size (n=8) means the result is not sufficient to establish a general production-level performance claim.`

## 6. Behavioural tests

| النوع                 | passed/total | pass rate | فشل مهم                           |
| --------------------- | -----------: | --------: | --------------------------------- |
| invariance            | Not measured |       N/A | Not evaluated                     |
| directional           | Not measured |       N/A | Not evaluated                     |
| minimum functionality |         PASS |       1/1 | Core Day 2 notebook checks passed |

## 7. تحليل الأخطاء

* المصدر: validation + behavioural failures only.
* عدد الأخطاء المقروءة يدويًا: `Not formally recorded`
* رابط worksheet داخل المستودع: `Not available`

| taxonomy tag           | count | مثال آمن مختصر                                         | الفرضية                                     |
| ---------------------- | ----: | ------------------------------------------------------ | ------------------------------------------- |
| `small-test-sample`    |     1 | Test set contains only 8 examples                      | Results have high uncertainty               |
| `cross-language`       |     1 | Arabic/English examples are evaluated in the same task | More language-specific testing is needed    |
| `synthetic-domain-gap` |     1 | Educational service-related examples                   | Real-world text may differ from the dataset |

## 8. الإصلاحات الثلاثة ذات الأولوية

| الأولوية | الدليل                               | الإجراء                                                                           | metric/slice المتوقع             | الكلفة | اختبار عدم الرجوع               |
| -------: | ------------------------------------ | --------------------------------------------------------------------------------- | -------------------------------- | ------ | ------------------------------- |
|        1 | Test set has only 8 examples         | Add a larger held-out evaluation set                                              | Macro-F1 + Arabic/English slices | Medium | Re-run frozen evaluation        |
|        2 | Arabic/English slices are very small | Expand balanced Arabic and English examples                                       | Per-language Macro-F1            | Medium | Language-slice regression test  |
|        3 | Synthetic dataset                    | Evaluate on a broader real-world or representative benchmark after privacy review | Macro-F1 by domain/language      | High   | Compare against frozen baseline |

## 9. ما الذي لا تثبته النتائج؟

* حجم الاختبار صغير جدًا (`n=8`) ولا يكفي لتعميم الأداء على بيانات إنتاجية.
* لا توجد حاليًا قياسات رسمية لـ latency أو throughput أو memory.
* لا توجد NER أو QA أو Retrieval experiments في النسخة الحالية.
* لا توجد بيانات كافية لبناء استنتاج قوي عن اللهجات العربية أو Arabizi.
* البيانات المستخدمة تعليمية/اصطناعية، لذلك قد لا تمثل تنوع النصوص الواقعية.
* لا توجد حاليًا confidence intervals أو repeated-run statistical estimates.
* نتيجة Transformer المقاسة تثبت أداءه على مجموعة الاختبار المستخدمة، ولا تثبت تفوقًا عامًا على جميع البيانات أو البيئات.

## 10. خلاصة للإدارة

تم تنفيذ تجربة تصنيف ثنائية اللغة باستخدام baseline من TF-IDF + LinearSVC ونموذج multilingual DistilBERT. على مجموعة الاختبار المجمدة، حقق الـ baseline **0.7333 Macro-F1** بينما حقق Transformer **0.8667 Macro-F1** و**0.875 Accuracy**. كما اجتازت فحوصات الفصل الأساسية، مع عدم وجود تداخل بين مجموعات train/validation/test.
