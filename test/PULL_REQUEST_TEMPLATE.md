<!--
このPRは「GCPでのA/Bテスト向けリリース作業」を安全に行うための“関所付き”テンプレートです。
背景：A/Bテストにて資源修正ミスにより誤った資源を本番反映してしまった → デプロイごとにPR必須とし、PRテンプレにて手順・証跡・承認を一元管理する。

 ルール（最低限）
- TODOは必ず全て埋める。"N/A" なら N/A と明記
- 証跡（スクショ/ログ/実施時刻/実施者）は PRコメントに貼付（本文に貼らない）
- 作業者 ≠ レビュアの 2名確認
-->

#  A/Bテスト デプロイPR（GUI: Model Registry + Vertex AI Endpoint）

##  概要（必須）
- **テストID / 名称**: <!-- TODO: 例) ABTEST_00X -->
- **モデルバージョン**: <!-- TODO: 例) tipsv1.8.0 --> 
- **環境 (GCPプロジェクト)**: <!-- TODO: 例) dev-recommend-engine -->
- **リージョン**: <!-- TODO: 例) asia-northeast1 (東京) -->
- **変更種類**: <!-- TODO: Model登録 / Endpoint新規 / モデル差し替え / トラフィック配分変更 など -->
- 
- **作業予定日**: <!-- TODO: 例) 2025-09-14 14:00-15:00 JST -->
- **影響範囲（利用者/下流）**: <!-- TODO -->
- **事前/事後連絡先（Slack？）**: <!-- TODO -->

##  共通パラメータ（Key-Value / 必須）　★ここの記載はあとで見直す。いるかも含めて
- `abtest_id`: <!-- TODO -->
- `endpoint_name`: <!-- TODO: 例) assets_recommend_gen_user_vector_v1_8 -->
- `model_names`（複数なら改行）:<!-- TODO: 例 assets_query_tower_v1_8 assets_candidate_tower_v1_8 -->

- `artifact_registry_repo`: <!-- TODO: 例) asia-northeast1-docker.pkg.dev/dev-recommend-engine/starts-two-tower -->
- `gcs_model_artifacts`: <!-- TODO: 例) gs://model_develop/pipeline/assets/v_1_8_0/DATA/MODEL -->
- `service_account`: <!-- TODO: 例) vertex-infer@<project>.iam.gserviceaccount.com（defaultは不可） -->
- `console_links.model_registry`: <!-- TODO: URL -->
- `console_links.endpoints`: <!-- TODO: URL -->

---

##  事前準備（本番時）
- [ ] **ステージングで同手順を完了しOK**（証跡コメントURL: ） <!-- TODO -->
- [ ] **セルシス様、acaii向け事前連絡**

---

#  手順１：Model Registryへのモデル登録
> GUIでの実施手順をテンプレ化。**実際に入力する値**を入力する。スクショはPRコメントへ。

### 手順１-実施チェックリスト
- [ ] (1)「インポート」選択
- [ ] (2)「新しいモデルとしてのインポート」選択
- [ ] (3) **モデル名** 入力（1行1件・必須）:　<!-- TODO: 例 assets_query_tower_v1_8 assets_candidate_tower_v1_8 -->

- [ ] (4) **リージョン = asia-northeast1(東京)**（画面表記を転記）: <!-- TODO -->
- [ ] (5)「Continue」押下: <!-- TODO -->
- [ ] (6)「既存のカスタムコンテナをインポート」を選択: <!-- TODO -->
- [ ] (7) **Artifact Registry のイメージ**  
- プロジェクト: <!-- TODO -->
- レポジトリ/パス: <!-- TODO -->
- **タグ or ダイジェスト**: <!-- TODO（latestなら理由も） -->
- [ ] (8) **コマンド = なし** を確認: <!-- TODO -->
- [ ] (9) **GCSアーティファクト**: <!-- TODO: パス -->
- （query/candidate 同一フォルダなら明記）: <!-- TODO -->
- [ ] (10) **予測ルート** `/predict` 入力: <!-- TODO -->
- [ ] (11) **ヘルスルート** `/health` 入力: <!-- TODO -->
- [ ] (12) 「インポート」押下 → 完了（完了画面スクショはPRコメントへ）

### 手順１-登録後の最小確認
- [ ] モデル一覧に **入力名** が出現
- [ ] 詳細に **選択コンテナ** & **Routes** が反映
- [ ] **GCS パス** が意図どおり

### 手順１-テスト手順
<!-- TODO テストスクリプトもしくは稼働確認方法があればあとで記載する-->  
<!-- TODO テスト結果はPRコメントに記載する-->  

---

#  手順２：Vertex AI Endpoint　作成＋デプロイ
> GUIでのVertexAIエンドポイント作成&デプロイ

### 手順２-実施チェックリスト
- [ ] (1) 「エンドポイント」画面へ移動（`console_links.endpoints`）  
- [ ] (2) 「✙作成」押下 → **リージョン = asia-northeast1**（画面表記を転記）: <!-- TODO -->
- [ ] (3) **エンドポイント名** 入力: <!-- TODO -->
- [ ] (4) **モデル設定**：「新しいモデル」→ **最新の query モデル** 選択  
- モデル名: <!-- TODO: 例) assets_query_tower_v1_8 -->
- [ ] (5) **バージョン = 最新**（単一なら v1）: <!-- TODO -->
- [ ] (6) **コンピューティング ノード**（数値はどちらも必須）  
- min_nodes = <!-- TODO --> / max_nodes = <!-- TODO -->
- [ ] (7) **マシンタイプ**: <!-- TODO: 例) n1-standard-2 -->
- [ ] (8) **サービスアカウント**: <!-- TODO（default禁止） -->
- [ ] (9) 「作成」押下（完了まで待機）。完了スクショはPRコメントへ。

### 手順２-登録後の最小確認
- [ ] エンドポイント一覧に **入力名** が表示
- [ ] デプロイ済みモデル/バージョンが **意図通り**
- [ ] **min/maxノード**・**マシンタイプ**・**SA** が設定どおり

### 手順２-テスト手順
<!-- TODO テストスクリプトもしくは稼働確認方法があればあとで記載する-->
<!-- TODO テスト結果はPRコメントに記載する-->  

