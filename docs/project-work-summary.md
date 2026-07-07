## 概要

---

これまで実施した作業を、後から判断の流れまで追えるように整理する。

各作業について、以下を揃える。

- やったこと
- 結論・結果
- 決定事項
- 意思決定理由
- 参照ファイル
- 残課題・次に見ること

## 作業サマリー

---

| No | やったこと | 結論・結果 | 決定事項 | 意思決定理由 | 参照ファイル | 残課題・次に見ること |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | PJの課題・現状整理 | 課題は、要件・TOBE未確定、AI継続改善フロー未整備、モデル処理フロー改善余地、コミュニケーション導線未整備に整理した。後にコミュニケーション導線は解消済みになった。 | PJ課題を「PJ全体」「システム」「個人課題」に分けて扱う。 | 課題の種類が混ざると、待つべきものと自分が進めるべきものが分からなくなるため。 | [project-issues-and-current-status.md](./migrated-from-atom/packets/project-issues-and-current-status.md) | TOBEは渡辺さん側の文章化を待つ。 |
| 2 | 現行repo / 現行処理フローのキャッチアップ | 現行処理は、分類、検知、セグメンテーション、マスク処理、インペイント、ナンバー処理の流れとして整理した。 | repoの内容を優先して現行フローを整理する。 | MTGメモ由来の情報だけでは不確実なため、repo実装を正とする必要があるため。 | [model-processing-flow-catchup.md](./migrated-from-atom/packets/model-processing-flow-catchup.md), [current-processing-and-annotation-flow.md](./migrated-from-atom/packets/current-processing-and-annotation-flow.md), [model-processing-flow-overview.drawio](./migrated-from-atom/packets/model-processing-flow-overview.drawio) | 車両画像加工機能の実現方式検討で再利用する。 |
| 3 | 画像分類PoC | Small CNN、ResNet、YOLOをepoch別に比較し、少量データでは結果が不安定であることを確認した。 | PoC結果はモデル選定の結論ではなく、検証方法の理解材料として扱う。 | データ数が少なく、評価結果が分割やデータ内容に強く影響されるため。 | [resnet-yolo-classification-validation.md](./migrated-from-atom/packets/resnet-yolo-classification-validation.md), [epoch-demo-validation.md](./migrated-from-atom/packets/epoch-demo-validation.md) | 本格評価ではデータ量、評価指標、testデータを整理する。 |
| 4 | Label Studio / アノテーションフロー検証 | Label Studioで画像分類の手動レビューとexportまで1周回せた。LLM/VLMの仮アノテーション結果も扱えることを確認した。 | アノテーションフローでは、AI仮判定と人間レビュー結果を比較できる形を重視する。 | 継続改善には、AIの仮判定と人間の確定ラベルの差分を後から見られる必要があるため。 | [annotation-workflow-tool-selection-proposal.md](./migrated-from-atom/packets/annotation-workflow-tool-selection-proposal.md), [annotation-tool-comparison.md](./migrated-from-atom/packets/annotation-tool-comparison.md) | 本番のアノテーションツール選定は別途継続。 |
| 5 | アノテーションツール比較 | Label Studio、CVAT、Roboflowなどを比較し、分類・検知・セグメンテーション・インペイントごとに必要なアノテーションを整理した。 | ツール選定は、作業者が迷わず作業できることと、pre-annotationを扱えることを重視する。 | 課題は人手アノテーションの限界であり、作業者が作業だけに集中できる環境が必要なため。 | [annotation-tool-comparison.md](./migrated-from-atom/packets/annotation-tool-comparison.md), [image-processing-annotation-flow.md](./migrated-from-atom/packets/image-processing-annotation-flow.md) | 実運用時のツールは、モデル処理方針と合わせて再評価する。 |
| 6 | AI継続改善の標準フロー整理 | 生データ取得〜教師データ作成、モデル学習・継続改善の2系統で標準フローを整理した。 | 教師データ作成フローとモデル学習フローは分けて考える。 | 人間レビューを含む教師データ作成と、SageMakerで回すモデル学習は性質が異なるため。 | [mlops-standard-flows-proposal.md](./migrated-from-atom/packets/mlops-standard-flows-proposal.md), [mlops-standard-flows.drawio](./migrated-from-atom/packets/mlops-standard-flows.drawio), [raw-data-to-training-data-proposal.md](./migrated-from-atom/packets/raw-data-to-training-data-proposal.md) | 詳細設計では、教師データ管理とモデル評価基準を決める。 |
| 7 | MLOps / SageMaker調査 | MLOpsの考え方、SageMakerの学習・評価・登録・デプロイ、推論方式、コストを整理した。 | モデル学習・評価・登録・デプロイはSageMakerを第一候補にする。 | 学習パイプライン、モデル管理、デプロイをManagedに扱いやすいため。 | [sagemaker-spec.md](./migrated-from-atom/packets/sagemaker-spec.md), [mlops-with-sagemaker-summary.md](./migrated-from-atom/packets/mlops-with-sagemaker-summary.md), [sagemaker-proposal.md](./migrated-from-atom/packets/sagemaker-proposal.md), [sagemaker-cost-summary.md](./migrated-from-atom/packets/sagemaker-cost-summary.md) | SageMaker環境の切り方、パイプライン単位、コスト詳細は後続設計で決める。 |
| 8 | 画像認識モデルの学習 | ResNet、YOLO、YOLO Segmentation、SAM、DeepLab、Mask R-CNN、LaMaなどの論文・事例を整理した。 | 分類、検知、セグメンテーション、インペイントの代表手法を、PJで説明できる材料として整理する。 | モデル処理フローの改善提案には、手法の特徴と事例を説明できる状態が必要なため。 | [image-recognition-paper-learning-order.md](./migrated-from-atom/packets/image-recognition-paper-learning-order.md), [practical-learning-notes.md](./migrated-from-atom/packets/practical-learning-notes.md), [image-recognition-case-links.md](./migrated-from-atom/packets/image-recognition-case-links.md) | 車両画像加工機能の検討で、どの手法を組み合わせるか判断する。 |
| 9 | 各手法を使う理由の整理 | ResNet50、YOLO、YOLO Segmentation、LaMaを使う理由を資料化した。 | 分類はResNet50をベースライン、検知はYOLO、セグメンテーションはYOLO Segmentation、インペイントはLaMaを候補にする。 | 実用例が多く、PoCや説明の基準にしやすいため。 | [classification-resnet50-deepforest-rationale.md](./migrated-from-atom/packets/classification-resnet50-deepforest-rationale.md), [object-detection-yolo-rationale.md](./migrated-from-atom/packets/object-detection-yolo-rationale.md), [segmentation-yolo-rationale.md](./migrated-from-atom/packets/segmentation-yolo-rationale.md), [inpainting-lama-glama-mask-optimization-rationale.md](./migrated-from-atom/packets/inpainting-lama-glama-mask-optimization-rationale.md) | 実プロダクトで使う最終モデルは、機能要件と既存処理の妥当性を見て決める。 |
| 10 | API設計の学習 | Google AIP、Microsoft API Design、Zalando REST Guidelinesを整理し、API設計の進め方を学習した。 | APIは機能名から直接切るのではなく、利用者が扱いたい対象、関係、データ構造、操作を整理して考える。 | 機能要件だけでAPIを切ると、リソースや操作の粒度が曖昧になりやすいため。 | [api-design-rulebook.md](./migrated-from-atom/packets/api-design-rulebook.md), [google-aip-121-resource-oriented-design-summary.md](./migrated-from-atom/packets/google-aip-121-resource-oriented-design-summary.md), [google-aip-130-methods-summary.md](./migrated-from-atom/packets/google-aip-130-methods-summary.md), [microsoft-api-design-best-practices-summary.md](./migrated-from-atom/packets/microsoft-api-design-best-practices-summary.md), [zalando-restful-api-guidelines-summary.md](./migrated-from-atom/packets/zalando-restful-api-guidelines-summary.md) | API詳細設計時に、このルールブックを参照する。 |
| 11 | 機能要件の洗い出し・カテゴリ整理 | used-car-ai-systemの画面・機能から、機能要件をカテゴリ単位に整理した。 | 機能要件は、モデル利用の有無ではなく、利用者がやりたいことのカテゴリで整理する。 | 先にやりたいことを整理しないと、モデルやAPIの単位を判断できないため。 | [used-car-ai-system-functional-requirements.md](./migrated-from-atom/packets/used-car-ai-system-functional-requirements.md), [functional-requirements-organized.md](./migrated-from-atom/packets/functional-requirements-organized.md), [functional-requirements-category-summary.md](./migrated-from-atom/packets/functional-requirements-category-summary.md) | 実現方式検討後に、カテゴリが妥当か見直す。 |
| 12 | API / 機能の切り方検討 | API候補ではなく、機能カテゴリとして整理したうえで、実現方式検討から統合・分割を判断する方針に変えた。 | 今の資料は「API切り方の確定案」ではなく「機能要件をカテゴリ化した叩き台」として扱う。 | 実現方式を見ないと、機能の統合・分割が適切か判断できないため。 | [used-car-ai-system-api-requirements.md](./migrated-from-atom/packets/used-car-ai-system-api-requirements.md), [functional-requirements-category-summary.md](./migrated-from-atom/packets/functional-requirements-category-summary.md) | 次issueで機能要件のカテゴライズ見直しを行う。 |
| 13 | 機能要件ごとの実現方式検討 | 各機能について、入力・出力、モデル利用有無、処理内容、必要なAWS基盤要素を整理した。車両画像加工機能も、詳細分類、検知、セグメンテーション、加工可否判定、インペイント、ナンバー処理、結果保存の流れとして整理した。 | 全機能カテゴリの実現方式検討は完了。 | 全体設計に進むためには、各機能がどの基盤要素で実現されるかを把握する必要があるため。 | [issue-functional-category-realization-methods](./migrated-from-atom/drafts/issue-functional-category-realization-methods), [realization-methods.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/realization-methods.md), [handoff.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/handoff.md), [vehicle-image-processing-realization.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/vehicle-image-processing-realization.md) | 次に、機能要件のカテゴライズ見直しへ進む。 |
| 14 | API Gateway / Lambda分割方針の決定 | API Gatewayは1つ、Lambdaは責務ごとに分ける方針を整理した。`history-lambda` は処理履歴・出力履歴確認用に置き、`notification-lambda` はV0では作らない方針に整理した。 | 1つのAPI Gateway + 責務別Lambdaで進める。V0の通知表示は Vercel polling + `history-lambda` で実現する。 | 入口を統一しながら、処理責務・権限・変更影響を分けられるため。通知はV0では即時pushよりも実装の単純さとコスト抑制を優先できるため。 | [api-gateway-lambda-splitting-policy.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/api-gateway-lambda-splitting-policy.md), [notification-display-realization.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/notification-display-realization.md) | リアルタイム通知が必要になったらWebSocket APIや通知専用Lambdaを検討する。 |
| 15 | 通知表示方式の検討 | 画面内即時フィードバックと非同期処理完了通知を分けた。 | V0では、通知専用LambdaやWebSocketは使わず、Vercel polling + `history-lambda` で進める。 | V0では即時push通知よりも実装の単純さとコスト抑制を優先できるため。 | [notification-display-realization.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/notification-display-realization.md) | リアルタイム通知が必要になったらWebSocket APIを検討する。 |
| 16 | 画像キャプション生成方式の検討 | キャプション生成は、画像そのものではなく画像解析結果という構造データを入力にする方針にした。 | Amazon Bedrockを使い、LLMで画像キャプションを生成する。 | VLMに毎回画像を読ませるより、軽く・安く・安定した生成に寄せやすいため。 | [image-caption-generation-realization.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/image-caption-generation-realization.md) | どの粒度の詳細分類を持つかは後回し。 |
| 17 | 詳細分類を提案する材料整理 | キャプション生成を軽く・安定させるため、前段で画像内容を構造化する必要があると整理した。 | 詳細分類は、画像分類精度改善だけでなく、後続機能を軽く・安く・安定させる前段構造化処理として提案できる。 | 画像解析結果をキャプション生成、画像加工、出品データ作成、確認画面で使い回せるため。 | [image-caption-detail-classification-review-note.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/image-caption-detail-classification-review-note.md) | 社長レビューで方針確認する。 |
| 18 | 認証・認可の後回し整理 | Cognito / Clerk / organization管理は、全体設計を止める論点ではなく後続の横断設計事項として整理した。 | 全体設計では共通認証・認可レイヤーを仮置きする。 | 認証方式が未確定でも、各機能の実現方式と全体構成は前に進められるため。 | [deferred-considerations.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/deferred-considerations.md) | 詳細設計で認証方式、組織管理、権限モデルを決める。 |
| 19 | メインチャット運用の見直し | メインチャット肥大化を避けるため、メインチャットはオーケストレーション、issueごとの作業はサイドチャットで進める方針にした。 | サイドチャットで決めたことは必ずファイルに残す。 | 会話履歴に依存すると、速度低下とコンテキスト喪失が起きやすいため。 | [handoff.md](./migrated-from-atom/drafts/issue-functional-category-realization-methods/handoff.md) | 今後のissueごとに同じ運用を徹底する。 |
| 20 | atom配下からproject repoへの移行 | atom配下のPJファイルを `used-car-ai-automation/docs/migrated-from-atom` にコピーした。 | まず全部移行し、その後コミット対象と非コミット対象を仕分ける。 | 最初から選別すると漏れやすく、まず全量退避した方が安全なため。 | [migrated-from-atom](./migrated-from-atom) | `tmp/` は原則コミット対象外。必要情報がないか確認してから除外する。 |

## 今後の整理方針

---

### コミット対象候補

- `drafts/` のissue単位成果物
- `packets/` の調査資料、提案資料、学習メモ
- 今後正本化する `docs/` 直下の整理ファイル

### コミット対象外候補

- `tmp/`
- PDFレンダリング画像
- cloneしたrepo
- 論文PDFなどのraw artifact
- Label Studio exportなどの生データ
- API keyや環境変数を含む可能性があるファイル

### 次に整理すること

1. `tmp/` の中に正本化すべき情報がないか確認する
2. `drafts/` と `packets/` を今後のディレクトリ構成に合わせて整理する
3. `decisions.md` に決定事項だけを集約する
4. コミット対象を確定する
