## 機能一覧

---

| カテゴリ | 機能 | 概要 | 実現方式検討 | 詳細ファイル |
| --- | --- | --- | --- | --- |
| 画像・書類の取込 | 画像取込機能 | 画像をアップロードし、取込画像として管理する。 | 済 | [image-upload-realization.md](./image-upload-realization.md) |
| 画像・書類の取込 | 画像自動処理機能 | 画像種別を判定し、必要な後続処理まで実行する。 | 済 | [image-auto-processing-realization.md](./image-auto-processing-realization.md) |
| 書類・展開図の読取 | 車検証読取機能 | 車検証画像から車両特定・基本情報を読み取る。 | 済 | [vehicle-inspection-certificate-reading-realization.md](./vehicle-inspection-certificate-reading-realization.md) |
| 書類・展開図の読取 | 出品票読取機能 | 出品票画像から出品・状態・装備情報を読み取る。 | 済 | [auction-listing-sheet-reading-realization.md](./auction-listing-sheet-reading-realization.md) |
| 書類・展開図の読取 | 展開図読取機能 | 展開図画像から損傷の位置・種類を読み取る。 | 済 | [damage-diagram-reading-realization.md](./damage-diagram-reading-realization.md) |
| 車両画像の加工・生成 | 車両画像加工機能 | ナンバー、バナー、他店舗ロゴなどを処理する。 | 済 | [vehicle-image-processing-realization.md](./vehicle-image-processing-realization.md) |
| 車両画像の加工・生成 | 画像キャプション生成機能 | 車両画像ごとの説明文を生成する。 | 済 | [image-caption-generation-realization.md](./image-caption-generation-realization.md) |
| 広告文・出品データ作成 | 広告文生成機能 | 車両データをもとに広告タイトル・本文を生成する。 | 済 | [advertising-copy-generation-realization.md](./advertising-copy-generation-realization.md) |
| 広告文・出品データ作成 | 出品データ作成機能 | 車両基本情報、画像、損傷情報、広告文などを出品用データとしてまとめる。 | 済 | [listing-data-creation-realization.md](./listing-data-creation-realization.md) |
| 確認・管理 | AI処理結果の確認・修正機能 | AIが出した読取結果・加工結果・生成結果を人間が確認し、必要に応じて修正する。 | 済 | [ai-result-review-realization.md](./ai-result-review-realization.md) |
| 確認・管理 | 車両データ管理機能 | 車両データの一覧・詳細・ステータスを扱う。 | 済 | [vehicle-data-management-realization.md](./vehicle-data-management-realization.md) |
| 確認・管理 | 履歴確認機能 | 外部出力の実行結果や、画像読取・加工などの処理結果を確認する。 | 済 | [history-view-realization.md](./history-view-realization.md) |
| 外部連携 | 出品データ出力機能 | 出品データを外部システムやCSVへ出力する。 | 済 | [listing-data-export-realization.md](./listing-data-export-realization.md) |
| 外部連携 | 連携設定管理機能 | 外部連携先や接続キーなどの接続設定を管理する。 | 済 | [integration-settings-management-realization.md](./integration-settings-management-realization.md) |
| 共通UI・画面操作 | 画面ナビゲーション機能 | ユーザーが各画面へ移動できるようにする。 | 済 | [screen-navigation-realization.md](./screen-navigation-realization.md) |
| 共通UI・画面操作 | 画面内操作機能 | 画面上で検索、選択、編集、実行などの操作を行えるようにする。 | 済 | [screen-operation-realization.md](./screen-operation-realization.md) |
| 共通UI・画面操作 | 通知表示機能 | 処理結果、エラー、確認が必要な状態などを画面上に表示する。 | 済 | [notification-display-realization.md](./notification-display-realization.md) |
