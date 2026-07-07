## 概要

---

プロダクト実行基盤における API Gateway と Lambda の分割方針を整理する。

本資料では、最初は API Gateway を 1 つにし、Lambda は責務ごとに分ける方針とする理由をまとめる。

## 結論

---

API Gateway は 1 つにする。

Lambda は責務ごとに分ける。

ただし、Lambda を細かく分けすぎるのではなく、まずは以下の単位で分ける。

| Lambda | 責務 | 主な処理 | 主な処理先 |
| --- | --- | --- | --- |
| `image-upload-lambda` | 画像の取込 | 画像ファイルを受け取り、保存先を決め、取込画像として記録する | S3 / DB |
| `image-analysis-job-lambda` | 画像解析ジョブの開始 | 解析ジョブを作成し、画像解析フローを起動する | DB / Step Functions |
| `review-result-lambda` | AI処理結果の確認・修正 | AI解析結果を取得し、人間の修正・確定結果を保存する | DB / S3 |
| `vehicle-data-lambda` | 車両データの管理 | 車両データ、画像、解析結果を参照・登録・更新する | DB / S3 |
| `history-lambda` | 処理履歴・出力履歴の確認 | 画像解析、画像加工、出力などの履歴とステータスを取得する | DB / S3 |
| `export-lambda` | 出品データの出力 | 確定済みデータを取得し、CSV / JSON / 外部連携用データを生成・保存する | DB / S3 / 外部連携先 |
| `integration-settings-lambda` | 外部連携設定の管理 | 連携先設定を保存し、認証情報を安全に管理する | DB / AWS Secrets Manager |

V0では `notification-lambda` は作らない。

画面内の即時フィードバックは Vercel 側で表示し、非同期処理の完了通知は Vercel の polling と `history-lambda` によるステータス取得で実現する。WebSocket API や通知専用 Lambda は、リアルタイム通知が必要になった段階で追加検討する。

## API Gateway を1つにした理由

---

初期段階では、API Gateway を機能ごとに分けるよりも、1 つの API Gateway に集約した方が構成をシンプルにできる。

API Gateway 内でパスを分ければ、機能ごとに異なる Lambda へルーティングできる。

```text
API Gateway
  ├─ /images/*
  ├─ /analysis-jobs/*
  ├─ /review-results/*
  ├─ /vehicles/*
  ├─ /histories/*
  ├─ /exports/*
  └─ /integration-settings/*
```

この構成であれば、外部から見た入口は 1 つにしつつ、内部処理は責務ごとの Lambda に分けられる。

## Lambda の分割単位の決定理由

---

Lambda は、実行する処理そのものではなく、責務ごとに分ける。

| 分割単位 | 決定理由 |
| --- | --- |
| 画像取込 | ファイルを受け取り S3 に保存する責務であり、画像解析や車両データ管理とは変更理由が異なるため |
| 画像解析ジョブ開始 | Step Functions を起動する受付・準備処理であり、画像解析そのものとは責務が異なるため |
| AI処理結果の確認・修正 | 人間レビュー後の確定結果を扱う責務であり、車両データ管理や画像取込とは操作対象が異なるため |
| 車両データ管理 | 車両データの参照・登録・更新を扱う中心的な業務データ管理であるため |
| 履歴確認 | 非同期処理や外部出力の状態確認は画面pollingでも使われるため、業務データ更新とは分けて扱いやすくするため |
| 出品データ出力 | 出力形式や外部連携先ごとの変更が発生しやすく、他の業務処理と分けた方が影響範囲を抑えやすいため |
| 外部連携設定管理 | Secrets Manager など機密情報に触れるため、他の Lambda と権限を分けた方が安全であるため |

通知専用Lambdaは、初期分割対象には含めない。通知方式は変更が発生しやすいが、V0の要求は即時pushではなく、非同期処理の状態確認と完了表示で満たせるためである。

分割基準は以下とする。

- 扱うリソースが違うか
- 必要な権限が違うか
- 変更される理由が違うか
- 障害時に巻き込みたくない範囲が違うか
- 後から外部公開・再利用しやすい単位か

## 詳細

---

### API Gateway は入口をまとめる

API Gateway は、外部または Vercel からのリクエストを受け取る入口として扱う。

初期段階では API Gateway を 1 つにし、パスごとに処理先 Lambda を分ける。

これにより、以下を実現できる。

- API の入口を統一できる
- 認証・ログ・監視の設計をまとめやすい
- パス単位で Lambda を分けられる
- 後から一部の機能だけ外部公開しやすい

### Lambda は責務単位で分ける

Lambda は、処理のまとまりを責務単位で分ける。

例えば、画像アップロードと画像解析ジョブ開始はどちらも画像に関係するが、責務は異なる。

```text
画像アップロード
  → 画像ファイルを受け取り、S3に保存し、取込画像として記録する

画像解析ジョブ開始
  → 取込済み画像を対象に、解析ジョブを作成し、Step Functionsを起動する
```

この 2 つを分けることで、アップロードだけ行うケースと、取込済み画像をまとめて解析するケースを分離できる。

### image-analysis-job-lambda は解析処理本体ではない

`image-analysis-job-lambda` は、画像解析そのものを実行する Lambda ではない。

役割は、解析ジョブを開始することである。

```text
Vercel
  ↓
API Gateway
  ↓
image-analysis-job-lambda
  ↓
Step Functions
  ↓
SageMaker Asynchronous Inference
```

Asynchronous Inference の実行、結果取得、後続処理への分岐は、Step Functions 側に寄せる。

### Lambda 内部も責務ごとに分ける

Lambda を責務ごとに分けても、1 つの Lambda 内に処理をベタ書きすると後から分割しづらくなる。

そのため、Lambda 内部でも以下のように責務ごとにファイル・関数を分ける。

```text
vehicle-data-lambda/
  handler.ts
  services/
    vehicleService.ts
    imageService.ts
    analysisResultService.ts
  repositories/
    vehicleRepository.ts
    imageRepository.ts
    analysisResultRepository.ts
```

この形にしておけば、後から `imageService` や `analysisResultService` を別 Lambda に切り出しやすい。

### 1つの巨大Lambdaにはしない

API Gateway の Lambda proxy integration を使えば、1 つの Lambda で複数パスを処理すること自体は可能である。

ただし、その場合は Lambda 側でパス、HTTPメソッド、入力値を見て処理を振り分ける必要がある。

この構成は以下の問題が出やすい。

- 1 つの Lambda に権限が集まりやすい
- 変更影響が広がりやすい
- ログやエラーの原因を追いづらい
- 後から一部機能だけ切り出しづらい

そのため、今回は 1 つの巨大 Lambda ではなく、責務ごとに分けた複数 Lambda とする。

### 後から分割・統合できる余地を残す

初期段階では、Lambda を細かく分けすぎない。

ただし、以下の状態になった場合は追加分割を検討する。

- 1 つの Lambda の責務が増えすぎた
- 必要な権限が大きく異なる処理が混ざってきた
- 一部の処理だけ変更頻度が高くなった
- 一部の処理だけ障害影響を分離したくなった
- 一部の処理だけ外部公開したくなった

逆に、初期段階で分けすぎて運用が重い場合は、近い責務の Lambda を統合する。

ただし、統合する場合でもコード内部は責務ごとに分けておく。
