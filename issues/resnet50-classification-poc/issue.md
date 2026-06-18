# ResNet50分類PoC検証

## 背景

中古車画像の分類において、まずはResNet50を分類モデルのベースラインとして使えるか確認したい。

## 目的

ResNet50を使って、外装・内装・広告などの車両画像分類がPoCサイズで検証できる状態にする。

## 関連repo

- [used-car-image-classification](../..)

## やること

- [ ] 学習用データを用意する
- [ ] ResNet50固定 + 分類層差し替えで学習する
- [ ] ResNet50一部fine-tuningで学習する
- [ ] accuracy / precision / recall / F1-score / validation loss を確認する
- [ ] 結果を比較し、次にDeepForestを検証する必要があるか判断する

## 完了条件

- [ ] ResNet50固定とfine-tuningの比較結果が出ている
- [ ] 評価指標と誤分類例が確認できている
- [ ] 次の検証方針が整理されている
