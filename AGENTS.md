## 概要

---

used-car-ai-automation PJ を進めるための運用ルールを整理する。

本PJでは、会話ログではなく GitHub 上のファイルを正本として扱う。

## 基本方針

---

- PJ固有の情報は `used-car-ai-automation` repo に残す
- atom repo にはPJ固有の詳細を残さない
- 会話で決めたことは、必ずファイルに反映する
- 新しいセッションでは、まず `docs/project-work-summary.md` を読む
- issueごとの作業は、専用の作業ディレクトリを切って進める

## セッション運用

---

### メインチャット

メインチャットは、PJ全体のオーケストレーションに使う。

扱う内容。

- 次に進めるissueの決定
- 優先順位の確認
- issue間の依存関係整理
- サイドチャットの成果確認
- 全体方針の軽い意思決定

扱わない内容。

- 長い調査
- 個別ファイルの大量編集
- issue内の細かい検討

### サイドチャット

サイドチャットは、issue単位の実作業に使う。

扱う内容。

- issue単位の調査
- issue単位の設計
- issue単位のファイル編集
- 完了条件の確認

サイドチャットで決めたことは、必ずファイルに残す。

## 新セッション開始時に読むファイル

---

最初に読むファイル。

[project-work-summary.md](./docs/project-work-summary.md)

必要に応じて読むファイル。

| 用途 | ファイル |
| --- | --- |
| 現在のPJ状況 | [project-status.md](./docs/project-status.md) |
| 現在の進捗 | [current-status.md](./docs/current-status.md) |
| 決定事項 | [decisions.md](./docs/decisions.md) |
| repo構成 | [repo-map.md](./docs/repo-map.md) |
| issue一覧 | [README.md](./docs/issues/README.md) |
| API設計ルール | [api-design-rules.md](./docs/guides/api-design-rules.md) |

## ファイル管理ルール

---

### ファイルの役割

| ファイル種別 | 役割 |
| --- | --- |
| `issue.md` | issueの目的、完了条件、ネクストアクションを書く |
| `handoff.md` | issueの現在地、残課題、次にやることを書く |
| `decisions.md` | 決定事項だけを集約する |
| `*-realization.md` | 機能ごとの実現方式を書く |
| `*-summary.md` | 調査結果や学習内容を要約する |
| `*-proposal.md` | 提案資料の元情報を書く |
| `notes/` | 検討メモを置く |
| `tmp/` | 一時ファイルを置く。原則コミットしない |

### 書く場所のルール

- 作業中のissue情報は `docs/issues/<issue-name>/` に置く
- 決定事項は `docs/decisions.md` または issue配下の `decisions.md` に集約する
- 一時ファイル、raw artifact、PDFレンダリング結果、cloneしたrepoは `tmp/` に置く
- `tmp/` は原則コミットしない
- 調査結果は、必要な情報だけdocsに蒸留する

## コミット対象

---

コミット対象にするもの。

- 要件整理
- 設計メモ
- 実現方式検討
- 意思決定理由
- 提案資料の元ファイル
- drawioなどの正本図
- handoff / summary / decision

コミット対象外にするもの。

- `tmp/`
- PDFレンダリング画像
- cloneした外部repo
- 論文PDFなどのraw artifact
- Label Studio exportなどの生データ
- API key、環境変数、認証情報を含む可能性があるファイル

## issueの進め方

---

issueを開始するときに作るもの。

```text
docs/issues/<issue-name>/
  issue.md
  handoff.md
```

必要に応じて追加するもの。

```text
docs/issues/<issue-name>/
  decisions.md
  notes/
  realization/
  proposal/
```

issue完了時に確認すること。

- `issue.md` の完了条件を満たしている
- 決定事項がファイルに残っている
- 次のissueで見るべきファイルが明示されている
- `handoff.md` が更新されている
- コミット対象外ファイルが混ざっていない

## 現在の主要作業

---

現在は、以下の流れで進めている。

1. 機能要件ごとの実現方式調査・検討
2. 機能要件のカテゴライズ見直し
3. プロダクト実行基盤の全体設計
4. MLOps側のモデル選定
5. 各コンポーネント設計
6. 実装・テスト

現時点の進捗確認は、以下を見る。

[project-work-summary.md](./docs/project-work-summary.md)

## 運用上の注意

---

- 会話だけで意思決定を終えない
- 詳細を1ファイルに詰め込みすぎない
- handoffは詳細資料ではなく現在地を示すファイルにする
- 新しいセッションでは、まず正本ファイルを読む
- サイドチャットで進めた内容は、メインチャットへ要約だけ戻す
- 正本ファイルと会話内容がズレた場合は、正本ファイルを優先する
