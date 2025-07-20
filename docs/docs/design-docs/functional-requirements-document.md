---
sidebar_position: 2
---

# 機能要件定義書


## オンライン対戦型ブロック崩しゲーム（Unreal Engine 5）機能要件定義書

---

### 1. 基本機能

- **ゲームモード**
  - オンライン対戦（最大2人）
  - 1人プレイ（ソロプレイ）モード
  - 対戦時は「ぷよぷよ」のように2画面で並列表示

- **勝敗条件**
  - すべてのブロックを先に壊したプレイヤーが勝利
    - 制限時間を設けた方がいいかも
  - ボールが落ちてもゲーム継続可能（復帰までペナルティ待機時間あり）

---

### 2. オンライン・マルチプレイ

- **実現方法**
  - Epic Online Services（EOS）を使用する

- **マッチメイキング**
  - ランダムマッチ
  - フレンド対戦
- **セッション管理**
  - ゲームルームの作成・参加・退出
  - プレイヤーの接続・切断管理
- **ネットワーク同期**
  - プレイヤー操作、ボール、ブロック、パワーアップなどのリアルタイム同期（Unreal Engineのレプリケーション機能利用）

---

### 3. ゲームプレイ

- **ブロック崩し基本システム**
  - パドル（バー）の左右移動
  - ボールの発射・反射・消失処理
  - ブロックの配置・破壊処理
- **パワーアップ・特殊ブロック**
  - パワーアップアイテム出現・取得・効果付与（例：パドル拡大、ボール速度変化、追加ボールなど）
  - 特殊ブロック（例：複数回ヒットで破壊、爆発ブロック等）

---

### 4. ペナルティ・リスポーン

- **ボール落下時の処理**
  - 一定時間の復帰待機（ペナルティタイマー）
  - 復帰後はボール再発射

---

### 5. AI・コンピュータ対戦（追加要件）

- **AIプレイヤー**
  - パドル自動操作（難易度設定可）
  - ゲーム進行・勝敗管理

---

### 6. スコア・ランキング

- **スコア計算**
  - クリアタイムで算出
- **ランキング機能**
  - ゲーム終了時にスコアをREST APIでサーバー送信
  - サーバーからランキングデータ取得・表示

---

### 7. ユーザーインターフェース

- **メインメニュー**
  - プレイモード選択（ソロ/対戦/フレンドマッチ）
  - 設定（言語切替、音量調整など）
- **ゲーム画面**
  - 2分割表示（自分・相手のフィールド）
  - スコア・残りブロック・タイマー・ペナルティ表示
- **リザルト画面**
  - 勝敗・スコア・ランキング表示

---

### 8. 多言語対応

- **日本語・英語切り替え**
  - UIテキスト・メッセージ等のローカライズ対応

---

### 9. 拡張性・今後の課題

- **最大プレイヤー数拡張（2→4人）**
- **AI対戦の高度化**
- **追加パワーアップ・新ルールの導入**

---

### 10. 開発・運用

- **Unreal Engine 5のマルチプレイ開発ガイドラインに準拠**[2][3][4]
- **ネットワークテスト・負荷テストの実施**
- **REST APIの設計・実装・セキュリティ対策**

---

この要件定義書を元に、各機能の優先度を決めて段階的に実装を進めることが推奨されます。特にUnreal Engine 5のマルチプレイ開発では、レプリケーションやセッション管理などのネットワーク設計が重要となります

Citations:
[1] https://www.youtube.com/playlist?list=PLzykqv-wgIQXompUswD5iHllUHxGY7w0q
[2] https://dev.epicgames.com/documentation/en-us/unreal-engine/multiplayer-programming-quick-start-for-unreal-engine
[3] https://www.youtube.com/watch?v=ef6SeknakeU
[4] https://www.youtube.com/watch?v=t0NYKaSh_hY
[5] https://www.udemy.com/course/unreal-engine-5-survival-framework-multiplayer-game-dev/
[6] https://www.youtube.com/watch?v=rvwb8nOWSOk
[7] https://forums.unrealengine.com/t/ue5-making-a-video-game/654524
[8] https://www.reddit.com/r/unrealengine/comments/1bf0cde/where_do_i_find_good_multiplayer_tutorials/

---
Answer from Perplexity: pplx.ai/share