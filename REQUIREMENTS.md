# 要件定義書：箱男ゲーム (Hako Otoko)

作成日：2026-03-28
担当：Opus（kickoff）

---

## 概要

「箱の中に入った人間が、小さな穴からしか世界を見られない」という制約のもと、
グリッドマップを探索するパズル探索ゲーム。
ウェブカメラの頭の動きで箱（キューブ）を操作し、
**マップ充足率90%以上**でクリア。
クリア・ゲームオーバー問わず、底面に隠れたAI生成画像が「ご褒美」として開示される。

### ハッカソン裏テーマ
> 「箱に閉じこもった人間でも、外の世界を少しずつ認識し、やがて箱から飛び出せる」
> → AIと共生できるか？閉じこもるか？という問いをゲーム体験で表現（安部公房『箱男』オマージュ）

---

## ターゲット

- ハッカソン審査員・観客（デモ前提）
- ブラウザで即プレイ可能、インストール不要
- カメラOKな環境（ノートPC推奨）

---

## ゲームシステム詳細

### マップ仕様
- グリッドサイズ：4×4（デフォルト）〜 8×8（タイトル画面で選択）
- タイルタイプ：
  - `GROUND`：通常タイル（歩ける）
  - `CLIFF`：崖タイル（踏んだらゲームオーバー）
  - `WATER`：水タイル（水着なし→ゲームオーバー、水着あり→通過OK）
  - `ITEM`：アイテムタイル（踏むとアイテム取得、その後GROUND化）
- **底面マップ**：全タイルが1枚の絵として合成（AI生成画像、ゲーム開始時にバックグラウンド取得）
- **クリア条件：マップ充足率90%以上**（タイル踏破数 / 総タイル数）

### キューブ（箱男）の仕様
- 外観：明るい段ボール質感の3D立方体（THREE.BoxGeometry）
- 移動：**ローリング**（スライドではなく転がる）、1タイルずつ
- アニメーション：Quaternionスラープで0.3秒かけて転がる
- 面トラッキング：6面（上下前後左右）の向きを常に管理
  - `currentForwardFace`：現在前方を向いている面
  - `currentBottomFace`：現在底面の面
  - ローリングのたびに更新

### 視界システム（穴メカニクス）
| 状態 | 穴のある面 | 穴のサイズ | 視覚効果 |
|---|---|---|---|
| 初期 | 前面×1 | 直径15% | 暗闇に小さな丸い穴 |
| フルーツ×1 | 前面×1 | 直径30% | 少し広がる |
| フルーツ×2 | 前面＋側面×1 | 直径45% | 横もうっすら見える |
| フルーツ×3 | 3面 | 直径60% | 前後左右がかなり見える |
| 鳥アイテム | — | — | 俯瞰マップ（2D）を入手 |
| スター | — | — | 箱から脱出！全3D視点（10秒間） |

実装：全画面に暗マスク→穴の位置にradial gradientで光を抜く（Canvas 2D overlay）

### アイテム一覧
| アイテム | 表示 | 効果 |
|---|---|---|
| フルーツ（3種類） | 🍎🍊🍋 | 穴が拡大 / 新たな面に穴が追加 |
| 水着 | 🩱 | 水タイル通過可能 |
| スター | ⭐ | 箱から脱出、全3D俯瞰ビュー（10秒） |
| 鳥 | 🐦 | 俯瞰2Dミニマップを常時表示 |

---

## 操作方法

### ウェブカメラ操作（メイン）
| ジェスチャー | 動作 | 検出方法 |
|---|---|---|
| 前方に2回お辞儀 | 向いた方向へ1タイル前進 | 鼻先Yが1秒内に2回下降 |
| 首を左に傾ける | 方向を左に変える | 両目角度 > +15° |
| 首を右に傾ける | 方向を右に変える | 両目角度 < -15° |
| 体の向きを変えてお辞儀 | 後退（方向転換してから前進） | — |

検出：ml5.js `faceMesh`（468ランドマーク）
```javascript
// Pitch（お辞儀）
const noseTipY = landmarks[1].y;
// 1秒内に2回 noseTipY が閾値以上下降したらtrigger

// Roll（傾け）
const leftEye = landmarks[33];
const rightEye = landmarks[263];
const angle = Math.atan2(rightEye.y - leftEye.y, rightEye.x - leftEye.x);
// angle > 0.26rad (+15°) → 左傾き
// angle < -0.26rad (-15°) → 右傾き
```

### キーボード操作（フォールバック・デモ用）
| キー | 動作 |
|---|---|
| ↑ / W | 前進 |
| ← / A | 方向を左に変える |
| → / D | 方向を右に変える |
| ↓ / S | 方向を後ろに変える |
| Space | カメラのお辞儀代替（前進） |

> **重要**：カメラなし環境でもデモができるようキーボード操作は必須実装

---

## ゲームフロー

```
1. タイトル画面
   └── マップサイズ選択（4×4 / 6×6 / 8×8）
   └── 「はじめる」ボタン

2. インストラクション画面（30秒 or スキップ）
   ├── カメラ許可リクエスト → ml5.js モデルロード
   ├── ジェスチャー説明アニメーション（お辞儀×2=前進 etc.）
   └── 「スキップ」ボタン（目立つ位置）

3. ゲームプレイ
   ├── 左上：カメラ小窓 + ジェスチャー検出インジケーター
   ├── 右上：マップ充足率バー（%）
   ├── 右下：ミニマップ（鳥アイテム取得後に表示）
   └── 中央：3Dゲームシーン

4a. クリア（充足率90%以上）
   └── 5. ご褒美画面へ

4b. ゲームオーバー（崖落下 or 水没）
   └── コミカル演出（少し間を置く）
   └── 5. ご褒美画面へ（ゲームオーバーでも表示）

5. ご褒美画面
   ├── 底面マップ全開示（AI生成画像がアニメーションで現れる）
   ├── ダウンロードボタン（PNG保存）
   └── リトライ / 新マップ選択
```

---

## UI・デザイン方針

- **外の世界**：明るくポップ（空は水色、草は緑、太陽あり）パステル系
- **箱の中のビュー**：暗い（穴からだけ光が差し込む）→ **ギャップが面白さ**
- フォント：丸めのサンセリフ（システムフォントOK）
- 箱の質感：段ボール茶色（#D2B48C系）

---

## サウンド設計

### BGM（Howler.js + mp3）
| シーン | ファイル | 雰囲気 |
|---|---|---|
| ゲームプレイ中 | `assets/bgm_game.mp3` | 明るくリズミカル（ループ） |
| クリア | `assets/bgm_clear.mp3` | ファンファーレ・感動系 |
| ゲームオーバー | `assets/bgm_gameover.mp3` | コミカル |

> mp3ファイルがない場合はWeb Audio APIでシンプルなビープ音を代替再生

### 効果音（ZzFX）
| イベント | 音の方向性 |
|---|---|
| キューブが転がる | コロコロ（低音のタップ） |
| アイテム取得（フルーツ） | ポップ・キラキラ |
| 水に入る（水着あり） | シャポン |
| 崖から落ちる | ドスン |
| 水没（水着なし） | ブクブク→ゲームオーバー音 |
| マップ充足率更新 | チン（小さく） |
| スター取得 | 大ファンファーレ |
| 穴が広がる | シュワシュワ |
| ご褒美画像開示 | ジャジャーン |

---

## 底面ご褒美システム

### 生成タイミング
ゲーム開始時に非同期でバックグラウンド取得（プレイ中に準備完了）

### APIエンドポイント
```
https://image.pollinations.ai/prompt/{encoded_prompt}?width=512&height=512&nologo=true
```

### プロンプトバリエーション（ランダム選択）
```javascript
const prompts = [
  "cute pixel art treasure map, colorful, 8bit style, top down view",
  "abstract geometric pattern made of cubes, vibrant colors, flat design",
  "retro game world map, bright colors, top down view, cheerful",
  "japanese landscape from above, pixel art, cheerful, cute",
  "secret garden maze from bird's eye view, colorful, whimsical",
  "city map from above, pixel art style, colorful buildings",
  "fantasy dungeon map, top view, vibrant, illustrated style",
  "ocean map with islands, cute pixel art, treasure hunt",
];
```

### フォールバック
Pollinations.aiが失敗した場合→Canvas APIでジェネレーティブアート（カラフルな幾何学模様）を代替表示

### 保存機能
```javascript
// Canvas合成 → PNG保存
const link = document.createElement('a');
link.download = `hakoOtoko_${Date.now()}.png`;
link.href = canvas.toDataURL('image/png');
link.click();
```

> **注意**：`<img>` タグでPollinations画像を表示した場合、CORSのためcanvas.toDataURLでのキャプチャが失敗する可能性あり
> 対策：`img.crossOrigin = 'anonymous'` を設定する

---

## 採用ライブラリ

| 機能 | ライブラリ | Tier | CDN |
|---|---|---|---|
| 3Dレンダリング | Three.js r170 | 3 | importmap: `https://cdn.jsdelivr.net/npm/three@0.170/build/three.module.min.js` |
| 頭部トラッキング | ml5.js v1 | 3 | `https://unpkg.com/ml5@1/dist/ml5.min.js` |
| 効果音 | ZzFX | 1 | `https://cdn.jsdelivr.net/npm/zzfx@1/ZzFX.min.js` |
| BGM | Howler.js | 2 | `https://cdn.jsdelivr.net/npm/howler@2/dist/howler.min.js` |
| AI画像生成 | Pollinations.ai | — | `https://image.pollinations.ai/prompt/...` |

---

## 実装方針

- **単一 `current/index.html`** で完結（CSS・JS全インライン）
- ローカルサーバー必須（カメラはfile://不可）
  ```bash
  cd /Users/doieisuke/Desktop/WebApp
  python3 -m http.server 8080
  # → http://localhost:8080/current/index.html
  ```
- Three.jsはimportmap経由でESModule読み込み
- ml5.jsはscriptタグでグローバル読み込み
- フレームワーク不使用（Vanilla JS）

---

## Sonnetへの引き継ぎメモ（/build 向け）

### 優先実装順
1. **Three.jsシーン基盤**：グリッド地面 + 立方体 + カメラ（斜め三人称視点）
2. **キューブローリング**：Quaternionスラープ、面トラッキング（6面の法線管理）
3. **視界エフェクト**：Canvas 2D overlay で暗マスク + radial gradient穴
4. **マップシステム**：タイルタイプ定義、visited管理、充足率計算
5. **アイテムシステム**：取得→穴拡大ロジック
6. **ゲームオーバー判定**：崖・水チェック
7. **キーボード操作**（先に実装してゲームを確認できるようにする）
8. **ml5.js頭部トラッキング**：faceMesh + Pitch/Roll検出
9. **サウンド**：ZzFX効果音 + Howler.js BGM
10. **インストラクション画面**（30秒タイマー + スキップ）
11. **ご褒美画面**：Pollinations.ai + PNG保存

### キューブ面トラッキングの実装メモ
```javascript
// 6面を定義（法線ベクトル）
const faces = {
  front:  new THREE.Vector3(0, 0, 1),
  back:   new THREE.Vector3(0, 0, -1),
  top:    new THREE.Vector3(0, 1, 0),
  bottom: new THREE.Vector3(0, -1, 0),
  left:   new THREE.Vector3(-1, 0, 0),
  right:  new THREE.Vector3(1, 0, 0),
};

// ロール時に面の向きを更新
// 前方ロール（z+方向に転がる）: front→bottom, bottom→back, back→top, top→front
```

### 落とし穴まとめ
- ml5.jsのモデルDLは初回10〜20秒かかる → 「カメラ準備中...」ローディング表示必須
- Pollinations.aiへのリクエストは `img.crossOrigin = 'anonymous'` をセットしてからsrcを設定
- BGM mp3がない場合のフォールバックとして `AudioContext` でシンプルなメロディを生成
- ジェスチャー検出にはデバウンス必須（1回の動きで複数triggerしない）
- スター取得後の「箱から脱出」ビューは10秒でリセット→箱に戻る演出

### BGMファイルの追加方法（後で）
以下のパスに mp3 を配置してください：
```
assets/bgm_game.mp3      ← ゲームプレイ中BGM（ループ再生）
assets/bgm_clear.mp3     ← クリア時
assets/bgm_gameover.mp3  ← ゲームオーバー時
```

### ハッカソンデモのコツ
- インストラクションの「スキップ」ボタンを目立つ位置に（審査時間短縮）
- キーボードでも映えるデモができるよう両対応
- ゲーム開始と同時にPollinations画像をバックグラウンド取得開始
- 4×4マップが最も映えるデモサイズ（30秒〜2分でクリア可能）
