## 概要

---

PJで実施した作業をissue単位で整理する。

各issue配下には、原則として以下を置く。

- `issue.md`：目的、完了条件、成果物
- `summary.md`：やったこと、結論・結果、決定事項、意思決定理由、後続への影響
- `handoff.md`：進行中issueの引き継ぎ

## issue一覧

---

| issue | 状態 | 内容 | 入口 |
| --- | --- | --- | --- |
| PJ前提キャッチアップ | 完了 | 課題、現行repo、現行処理フローの整理 | [project-context-catchup/summary.md](./project-context-catchup/summary.md) |
| 画像分類・アノテーションPoC | 完了 | Label Studio、LLM/VLM仮アノテーション、ResNet50学習のPoC | [classification-and-annotation-poc/summary.md](./classification-and-annotation-poc/summary.md) |
| モデル・手法学習 | 完了 | ResNet、YOLO、YOLO Segmentation、SAM、LaMaなどの学習 | [model-method-learning/summary.md](./model-method-learning/summary.md) |
| MLOps標準フロー整理 | 完了 | 教師データ作成フロー、モデル学習・継続改善フロー、SageMaker整理 | [mlops-standard-flow/summary.md](./mlops-standard-flow/summary.md) |
| 機能要件カテゴリ整理 | 完了 | used-car-ai-systemの機能要件整理、カテゴリ化 | [functional-requirements-categorization/summary.md](./functional-requirements-categorization/summary.md) |
| 機能要件ごとの実現方式調査・検討 | 完了 | 機能ごとの実現方式、AWS基盤要素、Lambda分割方針 | [functional-category-realization-methods/handoff.md](./functional-category-realization-methods/handoff.md) |

## 次に進むissue

---

次は `機能要件のカテゴライズ見直し` に進む。

このissueでは、各機能要件の実現方式調査・検討を踏まえ、現在のカテゴリ分けが適切かどうかを判断する。

