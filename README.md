# 🏫 東吳怪獸大戰 Soochow Monster Battle

**Soochow-Monster-Battle-ARFoundation** 是一個使用 **Unity、C# 與 AR Foundation** 開發的擴增實境塔防遊戲原型。
專案以東吳大學為主題，結合校園拱門、石頭人、護衛與怪獸模型，透過手機相機偵測現實平面，並以觸控操作建立 AR 戰場。

遊戲系統包含怪獸波次生成、NavMesh 尋路、角色動畫、拱門受傷與碰撞爆炸特效，目前主要設定的建置平台為 **Android／ARCore**。

---

## ✨ 功能特色

### 📱 AR 平面偵測與戰場放置

- 使用 AR Foundation 的平面偵測與射線檢測取得放置位置。
- 點擊偵測到的平面後，建立「東吳遊戲系統」Prefab。
- `ARManager` 透過 `isPlaced` 限制單次執行期間的點擊放置次數。
- 行動裝置 Renderer 已加入 `ARBackgroundRendererFeature`，供 AR 相機背景使用。

### 🏯 東吳校園主題場景

- 以東吳大學拱門作為怪獸進攻的目標。
- 遊戲系統配置四隻石頭人，並加入護衛角色。
- 整合校園招牌、校徽、角色模型與材質，呈現校園主題。

### 👾 怪獸波次與自動尋路

- 遊戲系統啟用時生成第一波怪獸，之後每隔 **5 秒**生成 **3 隻**。
- 以拱門為中心，在指定半徑的水平圓周上生成怪獸。
- 生成時自動指定拱門為目標，再由 `NavMeshAgent` 執行尋路。
- 專案包含 `NavMeshSurface` 與已儲存的地面 NavMesh 資料。

### 🪨 角色動畫與攻擊邏輯

- **Attack** 按鈕透過 `AnimationSystem` 觸發石頭人的攻擊動畫。
- **Dead** 按鈕觸發石頭人的死亡動畫。
- `PlayerAttack` 包含 **0.5 秒**攻擊判定視窗，可於有效期間透過碰撞消滅 `Enemy`。
- `AttackAllButton` 提供呼叫場上角色攻擊的方法；目前按鈕綁定狀態請參閱[目前版本說明](#目前版本說明)。

### 🚪 拱門生命值與碰撞處理

- 拱門初始生命值為 **100**。
- 怪獸進入 `Gate` 的 Trigger 時，主要進入事件會造成 **10 點**傷害，接著自毀。
- 拱門生命值歸零時，停用拱門的 Collider。
- 怪獸碰到 `Player` 標記的角色時，可產生爆炸特效並消失。

### 🎵 音樂與特效

- 遊戲系統的 AudioSource 配置東吳大學校歌，並設定為啟用時循環播放。
- 怪獸自毀時使用 Cartoon FX Remaster 爆炸煙霧特效。
- 自毀特效預設於 **2 秒**後清除。

---

## 🎮 操作方式

| 操作 | 說明 |
| --- | --- |
| 開啟應用程式 | 允許相機權限，開始 AR 追蹤 |
| 移動手機 | 掃描光線充足、具有紋理的平面，等待偵測結果 |
| 點擊平面 | 建立「東吳遊戲系統」，每次執行可透過點擊放置一次 |
| 按下 Attack | 播放已綁定石頭人的攻擊動畫 |
| 按下 Dead | 播放已綁定石頭人的死亡動畫 |
| 觀察怪獸 | 查看波次生成、朝拱門移動、碰撞自毀與爆炸特效 |

目前 `SampleScene` 也保留一個啟用中的「東吳遊戲系統」實例。若要測試純 AR 點擊放置流程，請先在 **Hierarchy** 停用這個場景實例，保留 `ARManager` 指向的 Prefab 資產，避免重複建立遊戲系統。

---

## 🎨 專案視覺素材

以下為 repository 內使用的校園圖片素材。

| 東吳大學招牌 | 東吳大學校徽 |
| --- | --- |
| ![東吳大學招牌素材](Assets/圖片/東吳.png) | ![東吳大學校徽素材](Assets/圖片/校徽.jpg) |

---

## 🛠 技術架構

### 開發環境與主要套件

| 技術／套件 | 版本 | 用途 |
| --- | --- | --- |
| Unity Editor | `6000.0.34f1`（Unity 6） | 場景編輯、遊戲執行與平台建置 |
| C# | Unity 腳本 | AR 放置、怪獸 AI、攻擊與碰撞邏輯 |
| AR Foundation | `6.0.6` | AR Session、平面偵測與射線檢測 |
| Google ARCore XR Plug-in | `6.0.6` | Android AR 功能提供者 |
| Apple ARKit XR Plug-in | `6.0.6` | 已列入相依套件，iOS 建置仍需另行設定 |
| Universal Render Pipeline | `17.0.3` | 場景渲染與 AR 相機背景整合 |
| AI Navigation | `2.0.5` | NavMeshSurface 與怪獸尋路 |
| Input System | `1.11.2` | 輸入與 UI 事件 |
| Unity UI（uGUI） | `2.0.0` | 按鈕與 UI 元件 |
| Animator／TextMesh Pro／AudioSource | Unity 元件 | 角色動畫、介面文字與背景音樂 |

Unity 版本依據 [`ProjectVersion.txt`](ProjectSettings/ProjectVersion.txt)，套件版本依據 [`manifest.json`](Packages/manifest.json)。

### 主要遊戲元件

| 元件 | 職責 |
| --- | --- |
| AR Session／XR Origin | 管理 AR 追蹤與相機座標 |
| AR Plane Manager／AR Raycast Manager | 偵測平面，取得點擊位置 |
| 東吳遊戲系統 Prefab | 組合拱門、角色、生成器、UI、音樂與地面 |
| 怪獸 Prefab | 整合 NavMeshAgent、進攻與自毀邏輯 |
| NavMeshSurface | 提供遊戲地面的可行走區域 |
| Animator | 控制石頭人攻擊、死亡與角色動畫狀態 |

---

## 📁 專案結構

| 路徑 | 內容 |
| --- | --- |
| [`Assets/Script/`](Assets/Script) | 專案自訂 C# 腳本 |
| [`Assets/場景/SampleScene.unity`](Assets/場景/SampleScene.unity) | 主要場景，已加入建置場景清單 |
| [`Assets/場景/SampleScene/`](Assets/場景/SampleScene) | 地面 NavMesh 資料 |
| [`Assets/預置物/`](Assets/預置物) | 遊戲系統、怪獸、石頭人、AR 地板與爆炸特效 Prefab |
| [`Assets/動畫/`](Assets/動畫) | 怪獸、石頭人與護衛 Animator Controller |
| [`Assets/怪獸/`](Assets/怪獸)、[`Assets/石頭人/`](Assets/石頭人)、[`Assets/護衛/`](Assets/護衛) | 角色模型、動畫與素材 |
| [`Assets/大門/`](Assets/大門) | 拱門模型與相關素材 |
| [`Assets/圖片/`](Assets/圖片) | 東吳大學招牌與校徽圖片 |
| [`Assets/音樂/`](Assets/音樂) | 東吳大學校歌 |
| [`Assets/Settings/`](Assets/Settings) | URP、Renderer 與場景渲染設定 |
| [`Assets/XR/`](Assets/XR) | ARCore／ARKit Loader 與 XR 設定 |
| [`Packages/`](Packages) | Unity 相依套件清單 |
| [`ProjectSettings/`](ProjectSettings) | Unity 版本、Player、Tag 與建置設定 |

---

## 🧩 核心腳本

| 腳本 | 功能 |
| --- | --- |
| [`ARManager.cs`](Assets/Script/ARManager.cs) | 對偵測平面執行 Raycast，建立遊戲系統並限制重複放置 |
| [`MonsterSpawner.cs`](Assets/Script/MonsterSpawner.cs) | 持續生成怪獸波次，配置出生位置與拱門目標 |
| [`MonsterAI.cs`](Assets/Script/MonsterAI.cs) | 透過 NavMeshAgent 朝目標移動，提供怪獸銷毀方法 |
| [`MonsterAttack.cs`](Assets/Script/MonsterAttack.cs) | 處理怪獸與 Gate 的 Trigger 接觸、傷害與自毀 |
| [`MonsterSelfDestruct.cs`](Assets/Script/MonsterSelfDestruct.cs) | 偵測與 Player 的碰撞或 Trigger 接觸，產生並清除爆炸特效 |
| [`GateHealth.cs`](Assets/Script/GateHealth.cs) | 管理拱門生命值、可選血條及生命值歸零後的 Collider 狀態 |
| [`AnimationSystem.cs`](Assets/Script/AnimationSystem.cs) | 將 Attack／Dead 按鈕連接至角色 Animator Trigger |
| [`PlayerAttack.cs`](Assets/Script/PlayerAttack.cs) | 啟動攻擊協程，於攻擊視窗內處理 Enemy 碰撞 |
| [`AttackAllButton.cs`](Assets/Script/AttackAllButton.cs) | 搜尋場上的 PlayerAttack，依序呼叫攻擊方法 |
| [`GolemGroupController.cs`](Assets/Script/GolemGroupController.cs) | 提供對指定 Animator 陣列設定 Attack Trigger 的方法 |

### 目前 Prefab 參數

以下數值依據「東吳遊戲系統」與「怪獸」Prefab 的序列化設定整理。

| 設定 | 數值 | 說明 |
| --- | --- | --- |
| 每波怪獸數量 `count` | `3` | 生成器每波建立的怪獸數量 |
| 波次間隔 `interval` | `5` 秒 | 第一波立即生成，後續依間隔持續生成 |
| 生成半徑 `radius` | `80` | 程式另加 `1` 單位緩衝，實際生成距離為 `81` Unity 座標單位 |
| 拱門生命值 `maxHP` | `100` | 拱門初始生命值 |
| 怪獸傷害欄位 `dps` | `10` | 主要 OnTriggerEnter 事件的一次傷害量 |
| 攻擊視窗 `attackDuration` | `0.5` 秒 | PlayerAttack 的有效攻擊期間 |
| 特效存留 `effectLifetime` | `2` 秒 | 自毀特效的清除時間 |

調整戰場尺寸、位置或生成半徑時，請同步確認 NavMesh 覆蓋範圍與怪獸出生位置。

---

## 🚀 安裝與執行

### 1. 準備環境

- 安裝 Unity Hub 與 **Unity `6000.0.34f1`**。
- 在該 Editor 版本加入 **Android Build Support**，包含 **Android SDK & NDK Tools** 與 **OpenJDK**。
- 準備支援 ARCore 的 Android 裝置；可查閱 [Google 官方支援裝置清單](https://developers.google.com/ar/devices)。

### 2. 下載專案

```bash
git clone https://github.com/johnnychan0523/Soochow-Monster-Battle-ARFoundation.git
cd Soochow-Monster-Battle-ARFoundation
```

在 Unity Hub 選擇 **Add project from disk**，加入含有 `Assets`、`Packages` 與 `ProjectSettings` 的專案根目錄。
使用指定 Editor 開啟，等待資產匯入與 Package Manager 還原套件。

### 3. 開啟主要場景

1. 開啟 `Assets/場景/SampleScene.unity`。
2. 在 Hierarchy 檢查 AR Session、擴增實境攝影機與遊戲系統。
3. 若要以手機測試點擊放置，先停用場景中既有的「東吳遊戲系統」實例。
4. 確認 `ARManager` 的「塔防物件」仍指向 `Assets/預置物/東吳遊戲系統.prefab`。

Editor 可用於檢查場景、動畫與欄位設定；AR 相機追蹤及平面放置請以 Android 實機驗證。
目前 Standalone 的 XR Loader 清單為空，XR Simulation 尚未啟用。

### 4. 確認 Android 設定

開啟 **Edit → Project Settings → XR Plug-in Management**，在 Android 分頁確認已啟用 **ARCore**，並檢查 **Project Validation**。

| 項目 | 專案設定／檢查值 |
| --- | --- |
| Package Name | `com.Johnny.ARTD` |
| Minimum API Level | `27`，仍需通過目前 Editor 與 ARCore 的 Project Validation |
| Scripting Backend | `IL2CPP` |
| Target Architectures | `ARM64` |
| Graphics API | `OpenGLES3` |
| Active Input Handling | `Both`，專案同時使用舊版 Input API 與 Input System |
| 行動裝置 Renderer | `Assets/Settings/Mobile_Renderer.asset` 已配置 `ARBackgroundRendererFeature` |

確認 Android 使用的 URP Asset／Quality 設定指向帶有 AR 背景功能的 Renderer。
設定細節可參閱 [ARCore 專案設定](https://docs.unity3d.com/Packages/com.unity.xr.arcore@6.0/manual/project-configuration-arcore.html)與 [AR Foundation 的 URP 設定](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@6.0/manual/project-setup/universal-render-pipeline.html)。

### 5. 建置至手機

1. 開啟 **File → Build Profiles**。
2. 新增 Android Build Profile，並選擇 **Switch Profile**。
3. 確認 Scene List 已勾選 `Assets/場景/SampleScene.unity`。
4. 關閉 **Export Project**；若輸出測試用 APK，保持 **Build App Bundle (Google Play)** 關閉。
5. 手機啟用 USB 偵錯並連接電腦，選擇對應的 **Run Device**。
6. 選擇 **Build and Run**，完成後允許應用程式使用相機。

---

<a id="目前版本說明"></a>

## 📌 目前版本說明

- **攻擊動畫與攻擊判定：** Attack／Dead 的動畫已由 `AnimationSystem` 在執行時註冊。按鈕的 Inspector 持續事件清單目前為空；若要啟用群體攻擊判定，需另外將 `UIHandler` 的 `AttackAllButton.AttackAllGolems()` 綁至 Attack 按鈕。
- **Animator 參照：** 四隻石頭人的 `PlayerAttack.animator` 目前指向 Prefab 資產參照；啟用攻擊判定前，請確認改綁場景實例自身的 Animator，並檢查 `Attack` Trigger 的動畫轉場。
- **接觸自毀：** `MonsterSelfDestruct` 在怪獸碰到 Player 時就會觸發，自毀條件不依賴 `PlayerAttack` 的攻擊視窗。
- **血條與結算：** `GateHealth` 已提供 Slider 更新邏輯，但目前 Prefab 的 `hpBar` 尚未指定。生命值歸零只會停用 Collider；勝敗結算與重新開始介面可再擴充。Dead 按鈕目前用於動畫展示。
- **Tag 設定：** 怪獸 Prefab 使用 `Enemy`，防守角色使用 `Player`，拱門使用 `Gate`。`GateHealth` 另有偵測 `Monster` 的分支，擴充碰撞邏輯時需確認標記一致。
- **iOS：** 專案已包含 ARKit 套件，但目前啟用的行動平台 XR Provider 為 Android ARCore；iOS 的 XR Provider、相機用途說明與 Xcode 建置設定仍需另行完成。

---

## 👤 專案維護者

| GitHub | 專案 |
| --- | --- |
| [johnnychan0523](https://github.com/johnnychan0523) | [Soochow-Monster-Battle-ARFoundation](https://github.com/johnnychan0523/Soochow-Monster-Battle-ARFoundation) |

