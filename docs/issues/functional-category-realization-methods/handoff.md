## 概要

---

各機能要件の実現方式調査・検討の引き継ぎメモ。

メインチャットを切った後も、このファイルと関連ファイルを読めば現在の状態から再開できるようにする。

## 現在の作業

---

`機能要件整理` で整理した機能要件カテゴリごとに、どのように実現するかを調査・検討している。

このissueの目的は、各機能要件の実現方式を整理し、次の `機能要件のカテゴライズ見直し` に進める状態にすること。

## 参照ファイル

---

### プロジェクト全体のパス

| 用途 | パス |
| --- | --- |
| atom repo | `/Users/makino.reiji/ghq/github.com/makinoreiji/atom` |
| used-car-ai-automation 作業ディレクトリ | `/Users/makino.reiji/ghq/github.com/makinoreiji/atom/work/projects/used-car-ai-automation` |
| packets | `/Users/makino.reiji/ghq/github.com/makinoreiji/atom/work/projects/used-car-ai-automation/packets` |
| drafts | `/Users/makino.reiji/ghq/github.com/makinoreiji/atom/work/projects/used-car-ai-automation/drafts` |
| 現在のissueディレクトリ | `/Users/makino.reiji/ghq/github.com/makinoreiji/atom/work/projects/used-car-ai-automation/drafts/issue-functional-category-realization-methods` |

### 現在のissueで見るファイル

| 用途 | ファイル |
| --- | --- |
| issue概要 | [issue.md](./issue.md) |
| 機能ごとの検討状況一覧 | [realization-methods.md](./realization-methods.md) |
| API Gateway / Lambda分割方針 | [api-gateway-lambda-splitting-policy.md](./api-gateway-lambda-splitting-policy.md) |
| 後回し論点 | [deferred-considerations.md](./deferred-considerations.md) |
| キャプション生成と詳細分類の社長レビュー用メモ | [image-caption-detail-classification-review-note.md](./image-caption-detail-classification-review-note.md) |

## 現在の検討状況

---

`realization-methods.md` 上では、全機能が `済` になっている。

| 状態 | 機能 | 詳細ファイル |
| --- | --- | --- |
| 済 | 車両画像加工機能 | [vehicle-image-processing-realization.md](./vehicle-image-processing-realization.md) |

## 決定済み事項

---

### API Gateway / Lambda

- API Gateway は 1つにする
- Lambda は責務ごとに分ける
- Lambda 内部も責務ごとにファイル・関数を分け、後から分割しやすくする
- 1つの巨大Lambdaに全処理を詰め込まない

### Lambda分割

初期方針として、以下の責務別Lambdaを置く。

| Lambda | 責務 |
| --- | --- |
| `image-upload-lambda` | 画像の取込 |
| `image-analysis-job-lambda` | 画像解析ジョブの開始 |
| `review-result-lambda` | AI処理結果の確認・修正 |
| `vehicle-data-lambda` | 車両データの管理 |
| `history-lambda` | 処理履歴・出力履歴の確認 |
| `export-lambda` | 出品データの出力 |
| `integration-settings-lambda` | 外部連携設定の管理 |

`notification-lambda` はV0では作らない。

通知表示は、Vercel側のpollingと `history-lambda` によるステータス取得で実現する。

### 画像取込

- `image-upload-lambda` で実現する
- 画像ファイルをS3に保存する
- 取込画像としてDBに記録する

### 画像自動処理

- `image-analysis-job-lambda` は解析ジョブ開始の受付役
- 画像解析本体は `Step Functions + SageMaker Asynchronous Inference` で実行する
- Asynchronous Inference の結果はS3に出力する
- Step Functions はS3をpollingして結果を取得し、後続処理へ分岐する
- 複数画像は Step Functions の Map state で処理する

### 車検証読取 / 出品票読取 / 展開図読取

- 画像自動処理と同じく、`Step Functions + SageMaker Asynchronous Inference + S3 polling` を基本構成とする

### 広告文生成

- Amazon Bedrock を使う
- SageMaker は使わない
- 同期処理でよい

### 出品データ作成

- モデル利用なし
- DBにある確定済み情報を読み取り、出品用データとしてまとめる
- Lambdaによる同期処理でよい

### AI処理結果の確認・修正

- Vercel + API Gateway + `review-result-lambda` + DB/S3
- AI結果を人間が確認・修正・確定する

### 車両データ管理

- `vehicle-data-lambda` による同期処理で実現する
- DB読み書き中心
- 車両一覧、詳細、ステータス、画像、解析結果などを扱う

### 履歴確認

- `history-lambda` として分ける
- 処理履歴と出力履歴の両方を扱う
- Web画面上で確認できる履歴一覧を想定する

### 出品データ出力

- `export-lambda` で実現する
- CSV / JSON / 外部連携用データを生成・保存する

### 連携設定管理

- `integration-settings-lambda` で実現する
- 外部連携設定や認証情報を管理する
- シークレット管理は一旦 AWS Secrets Manager を前提とする
- ローカル開発では Infisical を使い、最初は手動で Secrets Manager と揃える方針

### 画面ナビゲーション

- 画面遷移そのものは Vercel のみで実現する
- 画面ナビゲーション機能の要件を満たすだけなら Lambda は不要
- 遷移先画面でデータ取得・更新が必要な場合は、該当する責務別Lambdaを呼ぶ

### 画面内操作

- 基本は Vercel で実現する
- 保存・更新・実行が必要な操作は、操作対象に応じて既存の責務別Lambdaに寄せる
- 画面内操作専用のLambdaは作らない

### 通知表示

- 画面内の即時フィードバックは Vercel のみで実現する
- 非同期処理の完了通知は、V0では polling + `history-lambda` で実現する
- Vercel側に polling ロジックを実装し、DB上のジョブステータスをAPI経由で確認する
- WebSocket API や `notification-lambda` は、リアルタイム通知が必要になった段階で検討する

### 画像キャプション生成

- 入力は画像そのものではなく、前段処理で確定した画像解析結果という構造データのみ
- Amazon Bedrock を使い、LLMで画像キャプションを生成する
- 画像そのものをVLMに渡す方式は第一候補にしない
- 詳細分類の粒度は後回し

### 車両画像加工

- 画像自動処理の後続として、車両画像加工ジョブを非同期実行する
- 構成は `Step Functions + SageMaker Asynchronous Inference + S3 polling` を基本とする
- 処理は、詳細分類、加工対象検知、セグメンテーション、加工可否判定、インペイント、ナンバー処理、結果保存に分ける
- ナンバープレートは OpenCV のぼかし / 塗りつぶしを第一候補にする
- バナー、他店舗ロゴ、文字入れは LaMa などのインペイントを第一候補にする
- 車体に重なる、信頼度が低い、加工領域が大きいなどのケースは自動加工せず、レビューまたは掲載不可候補に回す
- 詳細分類結果は、車両画像加工だけでなく、画像キャプション生成、出品データ作成、確認画面、学習データ作成でも使う前段の構造化データとして扱う
- 初期方針では車両画像加工専用Lambdaは作らず、ジョブ開始は `image-analysis-job-lambda`、結果確認は `review-result-lambda`、車両データ参照は `vehicle-data-lambda` に寄せる

## 後回し論点

---

### 認証・認可

認証・認可は後続検討事項として残す。

全体設計では、共通認証・認可レイヤーがある前提で仮置きする。

検討事項は以下。

- Cognitoを使うか
- Clerkなどの認証SaaSを使うか
- Organization / member / role / permission をどこで持つか
- API Gateway Authorizerでどこまで制御するか
- Lambda内認可をどこまで行うか

詳細は [deferred-considerations.md](./deferred-considerations.md) を参照する。

### 詳細分類の粒度

画像キャプション生成では、画像解析結果という構造データを入力にする方針。

ただし、どの粒度の詳細分類を行うかは後回しにする。

詳細分類は、画像キャプション生成を軽く・安定させるための材料にもなる。

社長レビュー用の論点は [image-caption-detail-classification-review-note.md](./image-caption-detail-classification-review-note.md) を参照する。

## 注意点

---

`api-gateway-lambda-splitting-policy.md` は、最新方針に合わせて修正済み。

現時点の最新方針では、V0では `notification-lambda` は作らない。リアルタイム通知が必要になった段階で、WebSocket API や通知専用Lambdaを追加検討する。

## 次にやること

---

### 1. 機能要件のカテゴライズ見直しへ進む

車両画像加工機能の検討が終わったら、このissueの次アクションとして `機能要件のカテゴライズ見直し` に進む。

その際、画面内操作機能の一部要件が他機能に寄っている点も見直す。

### 2. 詳細設計で残す論点を切り出す

このissueでは、実現方式の大枠までを決めた。

詳細設計では以下を別途決める。

- 詳細分類の最小カテゴリ
- 画像加工ジョブのDB schema
- 加工済み画像と中間出力のS3 key設計
- 自動加工 / レビュー / 掲載不可候補の判定閾値
- SageMaker Asynchronous Inference で完結させる処理と、ECS / AWS Batch に切り出す処理の境界

## サイドチャット運用方針

---

今後は、メインチャットをオーケストレーション用に使い、issueごとの検討・編集はサイドチャットで進める。

メインチャットでは以下を行う。

- 次に扱うissueの決定
- 優先順位の確認
- issue間の依存関係整理
- サイドチャットの成果受け取り

サイドチャットでは以下を行う。

- issue単位の調査
- issue単位の設計
- issue単位のファイル編集
- 完了条件の確認

サイドチャットで決めたことは、必ずファイルに残す。
