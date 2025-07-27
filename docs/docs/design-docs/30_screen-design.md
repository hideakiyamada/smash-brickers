---
sidebar_position: 3
---

# 画面設計書(UI/UX)

## 1. 画面遷移

- UML -ステートマシン図
```mermaid
stateDiagram-v2
    [*] --> TitleScreen : ゲーム開始

    TitleScreen: タイトル画面
    ModeSelect: プレイモード選択画面
    SinglePlay: １人プレイ画面
    LocalVersusMenu: ローカル２人対戦メニュー画面
    LocalVersus: ローカル２人対戦画面
    RetryScreen: リトライ画面

    TitleScreen --> ModeSelect : 「プレイ」を選択
    TitleScreen --> [*] : ゲーム終了

    ModeSelect --> SinglePlay : 「1人プレイ」を選択
    ModeSelect --> LocalVersusMenu : 「2人対戦」を選択
    ModeSelect --> TitleScreen : 「タイトル画面へ」を選択
    ModeSelect --> [*] : ゲーム終了

    SinglePlay --> RetryScreen : ゲームクリア

    RetryScreen --> ModeSelect : リトライ
    RetryScreen --> [*] : ゲーム終了

    LocalVersusMenu --> LocalVersus : ２人のプレイヤーが「ホスト」、「クライアント」いずれかを選択

    LocalVersus --> RetryScreen : 勝利 or 敗北
```

## 2. 画面レイアウト

### タイトルメニュー画面

![タイトルメニュー画面](./img/gamescreen/gamescreen-title-menu.png)

- **構成要素**
  - ゲームタイトルロゴ
  - プレイボタン
    - Playボタン押下でプレイモード選択画面へ遷移
  - 終了ボタン
    - Quitボタン押下でゲーム終了

- **UXポイント**
  - シンプルなレイアウト、主要ボタンは中央に大きく配置

---

### プレイモード選択画面

![プレイモード選択画面](./img/gamescreen/gamescreen-modeselect-menu.png)

- **構成要素**
  - 1人プレイボタン
    - Single Play(1P)ボタン押下で1人プレイ画面へ遷移
  - 2人対戦ボタン
    - Local Versus(2P)ボタン押下で2人プレイメニュー画面へ遷移
  - タイトルメニューボタン
    - Title Menuボタン押下でタイトルメニュー画面へ遷移
  - 終了ボタン
    - Quitボタン押下でゲーム終了

- **UXポイント**
  - シンプルなレイアウト、主要ボタンは中央に大きく配置

---

### 1人プレイ画面

![1人プレイ画面](./img/gamescreen/gamescreen-1play.png)

- **構成要素**
  - キーボードの左右キーでパドルを移動
  - ボールがパドルの下に落ちると、一定時間経過後、画面中央からリスポーン
  - すべてのブロックを破壊するとゲーム終了
    - リトライ画面を表示

---

### リトライ画面

![リトライ画面](./img/gamescreen/gamescreen-retry.png)

- **構成要素**
  - リトライボタン
    - Retryボタン押下でプレイモード選択画面へ遷移
  - 終了ボタン
    - Quitボタン押下でゲーム終了

---

### 2人プレイメニュー画面

![2人プレイメニュー画面](./img/gamescreen//gamescreen-2play-modemenu.png)

- **構成要素**
  - ホストボタン
    - ホストとなるプレイヤーがHostボタンを押下し2人プレイ画面へ遷移
  - クライアントボタン
    - クライアントとなるプレイヤーがホストのIPアドレスを入力し、Clientボタンを押下し2人プレイ画面へ遷移
  - IPアドレス入力欄
    - クライアントがホストのIPアドレスをここに入力する

- **UXポイント**
  - リッスンサーバー方式でローカルオンライン対戦を実現する
  - Host、Clientの両ボタンが押されることでゲームが開始される

---

### 2人プレイ画面

![2人プレイ画面](./img/gamescreen/gamescreen-2play.png)

- **構成要素**
  - キーボードの左右キーでパドルを移動
  - ボールがパドルの下に落ちると、一定時間経過後、画面中央からリスポーン
  - すべてのブロックを先に破壊した方が勝者
    - リトライ画面を表示