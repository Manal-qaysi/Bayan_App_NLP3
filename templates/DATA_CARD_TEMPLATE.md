# DATA CARD — Bayan

## Dataset identity

* Name/version: Bayan Day 2 Classification Dataset
* Source/creator: Bayan Applied NLP course dataset
* License/permission: Educational use only; no separate public license specified
* Data hash or immutable revision: Course dataset revision used in the notebook; exact hash not recorded
* Intended educational task: Arabic/English text classification by topic

## Composition

| Split       | Rows | Arabic | English | Groups | Notes                          |
| ----------- | ---: | -----: | ------: | -----: | ------------------------------ |
| train       |   24 |     12 |      12 |     12 | Used for model training        |
| validation  |    8 |      4 |       4 |      4 | Used for model/epoch selection |
| frozen test |    8 |      4 |       4 |      4 | Opened once after freeze       |

**Total:** 40 rows, 20 groups.

## Fields and labels

| Field/label  | Meaning                                                | Allowed values                                     | Missing-value rule |
| ------------ | ------------------------------------------------------ | -------------------------------------------------- | ------------------ |
| `example_id` | Unique example identifier                              | `F-001` … `F-040`                                  | Required           |
| `group_id`   | Group used to prevent related examples crossing splits | Dataset group IDs                                  | Required           |
| `split`      | Dataset partition                                      | `train`, `validation`, `test`                      | Required           |
| `language`   | Text language                                          | `ar`, `en`                                         | Required           |
| `text`       | Input text                                             | Arabic or English text                             | Required           |
| `topic`      | Main classification target                             | `digital_service`, `health`, `permit`, `transport` | Required           |
| `sentiment`  | Secondary label retained in the dataset                | `positive`, `negative`, `neutral`                  | Required           |

## Collection/generation

The dataset was created as a small synthetic educational dataset for the Bayan Applied NLP course. The examples represent general service-related scenarios across digital services, permits, health, and transport. The dataset does not contain real people, real cases, or real personal information. The notebook uses the same dataset structure whether the course file is downloaded or the embedded fallback is used.

## Cleaning and preprocessing

* Display copy rule: Preserve the original `text` value for display and evaluation; preprocessing is applied only when required by the model pipeline.
* PII masking rule: Mask detected email addresses and Saudi mobile-number patterns before model-side preprocessing.
* Arabic profile/version: Conservative Arabic preprocessing using Unicode NFC normalization, tatweel removal, optional diacritic removal, optional alef normalization, and whitespace normalization.
* Deduplication/grouping: Examples are assigned to `group_id`; related examples are kept within the same split to prevent group leakage.
* Filtering/exclusions: No real-person or real-case data; synthetic examples are retained for the educational classification experiment.

## Split and leakage controls

* Split method/seed: Fixed dataset split with `train`, `validation`, and `test`; random seed `42` is used for the experiment.
* Group isolation evidence: 20 groups with `group_overlap = 0`; all groups remain isolated to a single split.
* Near-duplicate audit: Paired Arabic/English examples are grouped using `group_id` so bilingual equivalents do not cross dataset splits.
* Frozen-test access date and commit: Test was evaluated after training/validation configuration was fixed; exact Git commit was not recorded in the dataset card.

## Known gaps and risks

* Dialects/Arabizi: The dataset does not provide broad coverage of Saudi dialects or Arabizi.
* Class balance: Topic classes are balanced overall, with 10 examples per topic across 40 rows.
* Synthetic-to-real gap: The dataset is very small and synthetic, so results are not an estimate of production performance.
* Annotation ambiguity: Short synthetic examples may have limited ambiguity compared with real user queries.
* Small slices/uncertainty: The validation set contains only 8 examples and the test set only 8 examples; metrics therefore have high uncertainty.
* Misuse/privacy risk: The dataset is intended for educational experimentation, not for decisions about real people or real cases.

## Permitted and prohibited use

* Permitted educational use: NLP preprocessing, tokenization, text classification, model comparison, evaluation, and experimentation.
* Prohibited/high-risk use: Production decision-making, profiling real people, sensitive-person classification, or deployment as evidence of real-world model performance.
* Human review: Human review is required before treating model outputs as reliable for real-world use.

## Maintenance

* Owner/contact through GitHub: Manal Qaysi / Bayan Applied NLP repository
* Change/version policy: Keep dataset structure, labels, group assignments, and split definitions versioned; document any changes before re-running experiments.
* Index/model rebuild triggers: Rebuild preprocessing artifacts, label mappings, or model outputs whenever the dataset, labels, grouping, preprocessing version, or model checkpoint changes.
