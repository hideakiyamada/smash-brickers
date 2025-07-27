---
sidebar_position: 5
---

# 詳細設計書

主にUnreal Engineの設定内容を記載する

## 1. Unreal Engineフォルダ構造

- 📁 Content
  - 📁 Blueprints(Blueprintクラスのオブジェクトを配置)
    - Actor
      - Ball_BP
      - Balldestroyer_BP
      - Brick_BP
    - Widget
      - LocalMultiplayerMenu_WB
      - ModeSelect_WB
      - PlayerHud_WB
      - TitleMenu_WB
      - Win_WB
    - Pawn
      - Paddle_Client_BP
      - Paddle_Host_BP
    - PlayerController
      - PaddleController_BP
    - GameModeBase
      - Smashbrickers_gamemode
      - Smashbrickers_gamemode_1P
    - GameInstance
      - SoundGameInstance
    - BluePrintFunctionLibrary
      - UI_FunctionLibrary
  - 📁 Input
    - Input Action
      - Move_IA
    - Input Mapping Context
      - Smachbrickers_IMC
  - 📁 Maps（レベルのファイルを配置）
    - level_1P
    - level_2P
    - mode_select_menu
    - title_menu
    - twoplayer_mode_menu
  - 📁 Materials
    - Material
      - Glow_Mat
      - Master_Mat
      - Unlit_Mat
    - Material Instance Constant
      - Blueglowing_Mat
      - Green_Inst
      - Red_Inst
      - Yellow_Inst
    - Physical Material
      - Bounce_Mat
  - 📁 Sounds(ゲーム内BGM,SEの音源を配置)
    - Button_Sound_Effect_10.mp3
      - 音源取得元: [効果音ラボ](https://soundeffect-lab.info/sound/button/)
        - 決定ボタンを押す10 ゲームのスキル取得演出
      - メニューなどのボタンクリック音
    - Combat_Sound_Effect_4.mp3
      - ブロックの破壊音
    - Cursor_Move_Sound.mp3
      - ボールが壁やパドルに当たった時の音
    - Future_BGM.mp3
      - タイトルメニュー画面のBGM
    - MusMus-BGM-076.mp3
      - 音源取得元: [フリーBGM・音楽素材MusMus](https://musmus.main.jp/music_game_03.html)
        - ヒーローズ
      - ローカル２人対戦画面のBGM
    - MusMus-BGM-085.mp3
      - 音源取得元: [フリーBGM・音楽素材MusMus](https://musmus.main.jp/music_game_02.html)
        - 「思考」 DBD file No.08
      - １人プレイ画面のBGM
    - MusMus-BGM-115.mp3
      - 音源取得元: [フリーBGM・音楽素材MusMus](https://musmus.main.jp/music_game.html)
        - 隠密ファンク
      - プレイモード選択画面、ローカル２人対戦メニュー画面のBGM

## 2. 各オブジェクトの設定

### Blueprints/Actor/Ball_BP

#### EventGraph

![Components](./img/blueprints/actor/Ball_BP_EventGraph_1.png)

- UML - シーケンス図
```mermaid
sequenceDiagram
    title: OnComponentHit時のサウンド再生処理

    participant OtherActor as 他のアクター
    participant Ball as ボール (Sphereコンポーネント)
    participant AudioEngine as オーディオエンジン

    OtherActor ->> Ball: 衝突 (Hit)

    activate Ball
    Note over Ball: OnComponentHitイベント発生

    Note right of Ball: 衝突情報(Hit Result)から<br>Impact Point (衝突地点) を取得

    Ball ->> AudioEngine: SpawnSoundAtLocation(Cursor_Move_Sound, ImpactPoint)
    deactivate Ball
```

#### Components

![Components](./img/blueprints/actor/Ball_BP_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

- Ball_BP(Self)
![Components](./img/blueprints/actor/Ball_BP_ClassDefaults_Ball_BP(Self)_Details.png)

- Sphere
![Components](./img/blueprints/actor/Ball_BP_ClassDefaults_Sphere_Details_1.png)

![Components](./img/blueprints/actor/Ball_BP_ClassDefaults_Sphere_Details_2.png)

- ProjectileMovement
![Components](./img/blueprints/actor/Ball_BP_ClassDefaults_ProjectileMovement_Details.png)



---

### Blueprints/Actor/Balldestroyer_BP

#### EventGraph

![Components](./img/blueprints/actor/Balldestroyer_BP_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "ボールリスポーン処理"
        A(開始) --> B{"衝突したアクターは<br>Ball_BPか？"};
        B -- No --> F(終了);
        B -- Yes --> C[Get Game Mode];
        C --> D{"Game Modeの<br>種類は？"};
        D -- "Smashbrickers_gamemode" --> E1["On Ball Out Of Bounds<br>を呼び出す"];
        D -- "Smashbrickers_gamemode_1P" --> E2["On Ball Out Of Bounds<br>を呼び出す"];
        D -- "その他" --> F;
        E1 --> F;
        E2 --> F;
    end
```

#### Components

![Components](./img/blueprints/actor/Balldestroyer_BP_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

- Box

![Components](./img/blueprints/actor/Balldestroyer_BP_ClassDefaults_Box_Details.png)

---

### Blueprints/Actor/Brick_BP

#### EventGraph

![Components](./img/blueprints/actor/Brick_BP_EventGraph_1.png)

![Components](./img/blueprints/actor/Brick_BP_EventGraph_2.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "ブロックに衝突した時の処理"
        A(開始: Event Hitイベント) --> B{"衝突相手は<br>ボールか？"};
        B -- No --> Z(終了);
        B -- Yes --> C{"Breakstrengthは<br>0か？"};
        C -- No --> D[updatebrickcolor<br>ブロックの色を更新];
        D --> E;
        C -- Yes --> E[破壊エフェクトを再生];
        E --> F[破壊サウンドを再生];
        F --> G["Destroy Actor<br>自身を破壊する"];
        G --> H[レベル内の残りブロック数を数える];
        H --> I{"残りブロック数 == 0 ?"};
        I -- No --> Z;
        I -- Yes --> J[勝利Widget Win_WBを作成];
        J --> K[Widgetを画面に表示];
        K --> Z;
    end
```
- updatebrickcolor

![Components](./img/blueprints/actor/Brick_BP_EventGraph_3.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "ブロックの色を更新"
        A(開始: updatebrickcolor) --> B{"Brickstrengthの値は？"};
        B -- 1 --> C["マテリアルを「Green_Inst」に設定"];
        B -- 2 --> D["マテリアルを「Yellow_Inst」に設定"];
        B -- 3 --> E["マテリアルを「Red_Inst」に設定"];
        B -- "Default" --> F(終了);
        C --> F;
        D --> F;
        E --> F;
    end
```

#### Components

![Components](./img/blueprints/actor/Brick_BP_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

- Brick_BP(Self)

![Components](./img/blueprints/actor/Brick_BP_ClassDefaults_Brick_BP(Self)_Details.png)

- Brick_BP

![Components](./img/blueprints/actor/Brick_BP_ClassDefaults_Cube_Details.png)

---

### Blueprints/Widget/LocalMultiplayerMenu_WB

#### Designer

![Components](./img/blueprints/widget/LocalMultiplayerMenu_WB_Designer.png)

#### EventGraph

![Components](./img/blueprints/widget/LocalMultiplayerMenu_WB_EventGraph_1.png)

![Components](./img/blueprints/widget/LocalMultiplayerMenu_WB_EventGraph_2.png)

```mermaid
graph TD
    subgraph "ホストとして参加"
        A(Hostボタンクリック) --> B[クリック音を再生];
        B --> C[0.8秒待機];
        C --> D["level_2P を<br>リッスンサーバーとして開く"];
        D --> E(処理終了);
    end

    subgraph "クライアントとして参加"
        F(Clientボタンクリック) --> G[クリック音を再生];
        G --> H[0.8秒待機];
        H --> I[IPアドレス入力欄から<br>テキストを取得];
        I --> J["取得したIPアドレスに接続する"];
        J --> K(処理終了);
    end
```


---

### Blueprints/Widget/ModeSelect_WB

#### Designer

![Components](./img/blueprints/widget/ModeSelect_WB_Designer.png)

#### EventGraph

![Components](./img/blueprints/widget/ModeSelect_WB_EventGraph_1.png)

![Components](./img/blueprints/widget/ModeSelect_WB_EventGraph_2.png)

```mermaid
graph TD
    subgraph "Widget生成時の初期化処理"
        A(Event Construct) --> B[マウスカーソルを表示];
        B --> C[インプットモードをUIのみに設定];
    end

    subgraph "1Pボタンクリック時の処理"
        D(On Clicked: oneplayerbutton) --> E[クリック音を再生];
        E --> F[0.8秒待機];
        F --> G[level_1Pを開く];
    end

    subgraph "2Pボタンクリック時の処理"
        H(On Clicked: twoplayerbutton) --> I[クリック音を再生];
        I --> J[0.8秒待機];
        J --> K[twoplayermodeを開く];
    end

    subgraph "MainMenuボタンクリック時の処理"
        L(On Clicked: mainmenubutton) --> M[クリック音を再生];
        M --> N[0.8秒待機];
        N --> O[title_menuを開く];
    end

    subgraph "Quitボタンクリック時の処理"
        P(On Clicked: quitbutton) --> Q[クリック音を再生];
        Q --> R[0.8秒待機];
        R --> S[ゲームを終了する];
    end
```

---

### Blueprints/Widget/PlayerHud_WB

#### Designer

![Components](./img/blueprints/widget/PlayerHub_WB_Designer.png)

#### EventGraph

設定なし

---

### Blueprints/Widget/TitleMenu_WB

#### Designer

![Components](./img/blueprints/widget/TitleMenu_WB_Designer.png)

#### EventGraph

![Components](./img/blueprints/widget/TitleMenu_WB_EventGraph_1.png)

![Components](./img/blueprints/widget/TitleMenu_WB_EventGraph_2.png)

```mermaid
graph TD
    subgraph "Widget生成時の初期化処理"
        A(Event Construct) --> B[マウスカーソルを表示];
        B --> C[インプットモードをUIのみに設定];
    end

    subgraph "Playボタンクリック時処理"
        D(On Clicked: playbutton) --> E[クリック音を再生];
        E --> F[0.8秒待機];
        F --> G[mode_select_menuレベルを開く];
    end

    subgraph "Quitボタンクリック時処理"
        H(On Clicked: quitbutton) --> I[クリック音を再生];
        I --> J[0.8秒待機];
        J --> K[ゲームを終了する];
    end
```

---

### Blueprints/Widget/Win_WB

#### Designer

![Components](./img/blueprints/widget/Win_WB_Designer.png)

#### EventGraph

![Components](./img/blueprints/widget/Win_WB_EventGraph.png)

```mermaid
graph TD
    subgraph "Widget生成時の初期化処理(Event Construct)"
        A(開始) --> B[Fadeアニメーションを再生];
        B --> C[アニメーション終了まで待機];
        C --> D[ゲームをポーズ];
        D --> E[マウスカーソルを表示];
        E --> F[インプットモードをUIのみに設定];
    end

    subgraph "Playボタンクリック時処理"
        G(On Clicked: playbutton) --> H[クリック音を再生];
        H --> I["待機 (Delay)"];
        I --> J[mode_select_menuレベルを開く];
    end

    subgraph "Quitボタンクリック時処理"
        K(On Clicked: quitbutton) --> L[クリック音を再生];
        L --> M["待機 (Delay)"];
        M --> N[ゲームを終了する];
    end
```

---

### Blueprints/Pawn/Paddle_Client_BP

#### EventGraph

![EventGraph](./img/blueprints/pawn/Paddle_Client_WB_EventGraph.png)

- UML - アクティビティ図
```mermaid
graph TD
    subgraph "初期化処理 (Event BeginPlay)"
        A(開始) --> B[Get Controller];
        B --> C[Cast To PlayerController];
        C --> D{"ローカルのコントローラーか？"};
        D -- True --> E[Enhanced Input Subsystemを取得];
        E --> F[Input Mapping Context<br>Smachbrickers_IMCを追加];
        F --> G(完了);
        D -- False --> G;
    end

    subgraph "パドル移動処理 (Input Action Move_IA)"
        H(入力イベント発生) --> I["移動量を計算<br>(入力値 * Paddlespeed)"];
        I --> J["Add Local Offsetで<br>Paddleを移動"];
        J --> K(完了);
    end
```

#### Components

![EventGraph](./img/blueprints/pawn/Paddle_Client_BP_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

![EventGraph](./img/blueprints/pawn/Paddle_Client_BP_ClassDefaults_Paddle_Details.png)

---

### Blueprints/Pawn/Paddle_Host_BP


#### EventGraph

![EventGraph](./img/blueprints/pawn/Paddle_Host_WB_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "初期化処理 (Event BeginPlay)"
        A(開始) --> B[Get Controller & Cast];
        B --> C{"ローカルのコントローラーか？"};
        C -- True --> D[Input Mapping Context<br>Smachbrickers_IMCを追加];
        D --> E[Player HUD Widgetを作成 & 画面に表示];
        E --> F(完了);
        C -- False --> F;
    end

    subgraph "パドル移動処理 (Input Action Move_IA)"
        G(入力イベント発生) --> H["移動量を計算<br>(入力値 * Paddlespeed)"];
        H --> I["Add Local Offsetで<br>Paddleを移動"];
        I --> J(完了);
    end
```

#### Components

![EventGraph](./img/blueprints/pawn/Paddle_Host_BP_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

![EventGraph](./img/blueprints/pawn/Paddle_Host_BP_ClassDefaults_Paddle_Details.png)

---

### Blueprints/PlayerController/PaddleController_BP

#### EventGraph

![EventGraph](./img/blueprints/PlayerController/PaddleController_BP_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "BeginPlay時のカメラ設定処理"
        A(開始: Event BeginPlay) --> B[Player Controllerを取得];
        B --> C["インプットモードを<br>Game And UIに設定"];
        C --> D[レベル内の全Gamemodeアクターを取得];
        D --> E[配列から最初のアクターを取得];
        E --> F{"取得したアクターは有効か？<br>(IsValid)"};
        F -- No --> H(終了);
        F -- Yes --> G["Player Controllerの<br>View Targetを<br>取得したアクターに設定"];
        G --> H;
    end
```

---

### Blueprints/GameModeBase/Smashbrickers_gamemode

#### EventGraph

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_EvenetGraph_1.png)

- UML - シーケンス図

```mermaid
sequenceDiagram
    title: BeginPlay時のスポーン地点設定処理
    participant BP as 実行中のブループリント
    participant World as ワールド

    activate BP
    Note over BP: Event BeginPlay 発生

    BP->>World: GetActorsWithTag("TargetPoint", "HostBallSpawn")
    World-->>BP: [HostSpawnActor]（アクターの配列）
    Note right of BP: 配列の最初の要素を変数<br>「Host Spawn Point」にセット

    BP->>World: GetActorsWithTag("TargetPoint", "ClientBallSpawn")
    World-->>BP: [ClientSpawnActor]（アクターの配列）
    Note right of BP: 配列の最初の要素を変数<br>「Client Spawn Point」にセット
    
    deactivate BP
```

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_EvenetGraph_2.png)

- UML - シーケンス図

```mermaid
sequenceDiagram
    title: OnPostLogin時のプレイヤーセットアップ処理
    participant PlayerController as 新規プレイヤー
    participant GameMode as ゲームモード
    participant World as ワールド
    participant HostPaddle as Host用パドル
    participant ClientPaddle as Client用パドル

    PlayerController->>GameMode: プレイヤー接続 (OnPostLogin)
    activate GameMode

    alt IsHostがTrueの場合 (ホスト側の処理)
        GameMode->>World: HostBallSpawnアクターを取得
        World-->>GameMode: スポーン位置を返す
        GameMode->>HostPaddle: create() (指定位置にスポーン)
        GameMode->>PlayerController: Possess(HostPaddle) (操作権限を渡す)
    else IsHostがFalseの場合 (クライアント側の処理)
        GameMode->>World: ClientBallSpawnアクターを取得
        World-->>GameMode: スポーン位置を返す
        GameMode->>ClientPaddle: create() (指定位置にスポーン)
        GameMode->>PlayerController: Possess(ClientPaddle) (操作権限を渡す)
        GameMode->>GameMode: StartMatchHostを実行
        GameMode->>GameMode: StartMatchClientを実行
    end

    deactivate GameMode
```

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_EvenetGraph_3.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "StartMatchHost"
        A(開始) --> B["Host Spawn Pointの<br>Transformを取得"];
        B --> C["取得したTransformで<br>Ball BPをスポーン"];
        C --> D["スポーンしたボールを<br>Current Ball変数に保存"];
        D --> E[ボールに力を加えて発射];
        E --> F(処理終了);
    end
```

```mermaid
graph TD
    subgraph "StartMatchClient"
        A(開始) --> B["Client Spawn Pointの<br>Transformを取得"];
        B --> C["取得したTransformで<br>Ball BPをスポーン"];
        C --> D["スポーンしたボールを<br>Current Ball変数に保存"];
        D --> E[ボールに力を加えて発射];
        E --> F(処理終了);
    end
```

```mermaid
graph TD
    subgraph "OnBallOutOfBounds"
        A(開始) --> B{"ボールは有効か？ (IsValid)"};
        B -- No --> Z(終了);
        B -- Yes --> C[ボールを破壊];
        C --> D["3秒待機 (Delay)"];
        D --> E{"ホスト側のゴールか？"};
    end

    subgraph "StartMatchHost"
        F["Hostスポーン地点に<br>ボールをスポーン"];
        G[ボールに力を加える];
        F --> G;
    end

    subgraph "StartMatchClient"
        H["Clientスポーン地点に<br>ボールをスポーン"];
        I[ボールに力を加える];
        H --> I;
    end

    E -- Yes --> F;
    E -- No --> H;
    G --> Z;
    I --> Z;
```

#### Components

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_ClassDefaults_Smashbrickers_gamemode(Self)_Details.png)

---

### Blueprints/GameModeBase/Smashbrickers_gamemode_1P

#### EventGraph

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_1P_EvenetGraph_1.png)

- UML - シーケンス図

```mermaid
sequenceDiagram
    title: OnPostLogin時のプレイヤーセットアップ処理
    participant PlayerController as 新規プレイヤー
    participant GameMode as ゲームモード
    participant World as ワールド
    participant Paddle as Paddle Host BP

    PlayerController->>GameMode: プレイヤー接続 (OnPostLogin)
    activate GameMode

    GameMode->>World: Get Actor with Tag("HostBallSpawn")
    World-->>GameMode: スポーン地点アクターを返す

    GameMode->>Paddle: create() (指定位置にスポーン)
    Note right of GameMode: スポーンしたPaddleを<br>プレイヤーにPossessさせる

    GameMode->>PlayerController: Possess(Paddle)

    GameMode->>GameMode: StartMatchHostを実行

    deactivate GameMode
```

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_1P_EvenetGraph_2.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "StartMatchHost"
        A(開始) --> B["Host Spawn Pointの<br>Transformを取得"];
        B --> C["取得したTransformで<br>Ball BPをスポーン"];
        C --> D["スポーンしたボールを<br>Current Ball変数に保存"];
        D --> E[ボールに力を加えて発射];
        E --> F(処理終了);
    end
```

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "OnBallOutOfBounds"
        A(開始: OnBallOutOfBounds) --> B{"Current Ballは有効か？<br>(IsValid)"};
        B -- No --> G(終了);
        B -- Yes --> C[Current Ballを破壊];
        C --> D["3秒待機 (Delay)"];
        D --> E{"ホスト側のゴールか？"};
        E -- False --> G;
        E -- True --> F[Start Match Hostイベントを呼び出す];
        F --> G;
    end
```

#### Components

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_1P_Components.png)

#### Class Defauls - Details - Show Only Modified Properties

![EventGraph](./img/blueprints/GameModeBase/Smashbrickers_gamemode_1P_ClassDefaults_Smashbrickers_gamemode(Self)_Details.png)


---

### Blueprints/GameInstance/SoundGameInstance

#### Functions

![EventGraph](./img/blueprints/GameInstance/SoundGameInstance_Functions.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "PlayButtonSound 関数の処理フロー"
        A(開始) --> B["Play Sound 2D<br>(Button_Sound_xxxを再生)"];
        B --> C(終了);
    end
```

---

### Blueprints/BluePrintFunctionLibrary/UI_FunctionLibrary

#### Functions

![EventGraph](./img/blueprints/BluePrintFunctionLibrary/UI_FunctionLibrary_Functions.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "PlayButtonClickSound 関数の処理フロー"
        A(開始) --> B[Get Game Instance];
        B --> C{"Cast To SoundGameInstance"};
        C -- 成功 --> D["SoundGameInstanceの<br>Play Button Sound関数を呼び出す"];
        C -- 失敗 --> E(終了);
        D --> E;
    end
```

---

### Input/Input Action/Move_IA

#### Details

![EventGraph](./img/Input/Input%20Action/Move_IA_Details.png)

---

### Input/Input Mapping Context/Smachbrickers_IMC

#### Details

![EventGraph](./img/Input/Input%20Mapping%20Context/Smachbrickers_IMC_Details.png)

---

### Maps/level_1P

#### Item

![EventGraph](./img/maps/level_1P_Item.png)

#### EventGraph

![EventGraph](./img/maps/level_1P_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "BeginPlay時のBGM再生処理"
        A(開始: Event BeginPlay) --> B["Play Sound 2D<br>(MusMus-BGM-xxxを再生)"];
        B --> C(終了);
    end
```


---

### Maps/level_2P

#### Item

![EventGraph](./img/maps/level_2P_Item.png)

#### EventGraph

![EventGraph](./img/maps/level_2P_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "BeginPlay時のBGM再生処理"
        A(開始: Event BeginPlay) --> B["Play Sound 2D<br>(MusMus-BGM-xxxを再生)"];
        B --> C(終了);
    end
```

---

### Maps/mode_select_menu

#### EventGraph

![EventGraph](./img/maps/mode_select_menu_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "BeginPlay時の初期化処理"
        A(開始: Event BeginPlay) --> B[BGMを再生];
        B --> C[Mode Select Widgetを作成];
        C --> D[作成したWidgetを画面に表示];
        D --> E(処理終了);
    end
```

---

### Maps/title_menu

#### EventGraph

![EventGraph](./img/maps/title_menu_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "BeginPlay時の初期化処理"
        A(開始: Event BeginPlay) --> B[BGMを再生];
        B --> C[Title Menu Widgetを作成];
        C --> D[作成したWidgetを画面に表示];
        D --> E(処理終了);
    end
```

---

### Maps/twoplayer_mode_menu

#### EventGraph

![EventGraph](./img/maps/twoplayer_mode_menu_EventGraph.png)

- UML - アクティビティ図

```mermaid
graph TD
    subgraph "BeginPlay時の初期化処理"
        A(開始: Event BeginPlay) --> B[BGMを再生];
        B --> C[Player Controllerを取得];
        C --> D{"Player Controllerは<br>有効か？ (IsValid)"};
        D -- No --> H(終了);
        D -- Yes --> E[マウスカーソルを表示];
        E --> F["Local Multiplayer Menu<br>Widgetを作成"];
        F --> G[作成したWidgetを画面に表示];
        G --> H;
    end
```

---

### Materials/Material/Glow_Mat

#### Parameters

![Parameters](./img/materials/Glow_Mat_Parameters.png)

### MaterialGraph

![MaterialGraph](./img/materials/Glow_Mat_MaterialGraph.png)

---

### Materials/Material/Master_Mat

#### Parameters

![Parameters](./img/materials/Master_Mat_Parameters.png)

### MaterialGraph

![MaterialGraph](./img/materials/Master_Mat_MaterialGraph.png)

---

### Materials/Material/Unlit_Mat

#### Parameters

![Parameters](./img/materials/Unlit_Mat_Parameters.png)

### MaterialGraph

![MaterialGraph](./img/materials/Unlit_Mat_MaterialGraph.png)

---

### Materials/Material Instance Constant/Blueglowing_Mat

#### Details

![Details](./img/materials/Blueglowing_Mat_Details.png)

---

### Materials/Material Instance Constant/Green_Inst

#### Details

![Details](./img/materials/Green_Inst_details.png)

---

### Materials/Material Instance Constant/Red_Inst

#### Details

![Details](./img/materials/Red_Inst_Details.png)

---

### Materials/Material Instance Constant/Yellow_Inst

#### Details

![Details](./img/materials/Yellow_Inst_Details.png)

---

### Materials/Physical Material/Bounce_Mat

#### Details

![Details](./img/materials/Bounce_Mat_Details.png)
