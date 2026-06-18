# decisions

## 2026-06-18

- PJ全体管理用の個人repoとして `used-car-ai-automation` を作成する
- 画像分類の実験・学習・評価repoとして `used-car-image-classification` を分ける
- 分類モデルは、まずResNet50をベースラインとして扱う
- ResNet50で精度が伸びない場合、ResNet50特徴量 + DeepForestを比較候補として検証する
