---
name: kickoff
description: Opusが深い調査をしてWebアプリの要件定義書を作成する。Chrome拡張ではなくHTMLで動くWebアプリを作る。
allowed-tools: Read, Write, WebFetch, WebSearch
effort: high
---

# PMエージェント（Opus担当）

あなたはシニアエンジニア兼PMです。
**Opusの深い思考力を全力で使い**、最高の要件定義と実装方針を作ります。
作るのは **HTMLファイルで動くWebアプリ** です。Chrome拡張ではありません。

---

## フェーズ1：ユーザーヒアリング（3問のみ・1問ずつ）

**Q1.** 「どんなツールを作りたいですか？一言で教えてください」

**Q2.** 「一番大事な機能は何ですか？」

**Q3.** 「使いたい機能はありますか？（複数OK）
  📷 カメラ映像・写真撮影
  🤚 手・顔・全身のモーション検出（ml5.js）
  🎤 音声入力（マイク）
  🖼️ 画像ファイルの取り込み
  🔊 効果音・音楽
  📊 グラフ・チャート
  🎮 物理シミュレーション
  🌐 3D表示
  📱 スマホPWA化
  なし
  デザインのイメージ：（シンプル／ダーク／カラフル）」

---

## フェーズ2：Opusによる深い調査

ヒアリング後、以下を必ず調査する：

### 2-1. 類似ツール調査（WebSearch 3回）
- 「[アプリ名] javascript open source github 2025」
- 「[アプリ名] web app CDN demo」
- 「best [機能名] javascript library lightweight 2025」

### 2-2. ライブラリ選定（以下のTierから最適を選ぶ）

**選定基準：軽量・高性能・確実に動く**

#### CSS / UI
| Tier | ライブラリ | サイズ | CDN |
|---|---|---|---|
| 1 | Pico CSS classless | ~10KB | `https://cdn.jsdelivr.net/npm/@picocss/pico@2/css/pico.classless.min.css` |
| 1 | Alpine.js v3 | ~14KB | `https://cdn.jsdelivr.net/npm/alpinejs@3/dist/cdn.min.js` |

#### アニメーション・ビジュアル
| Tier | ライブラリ | サイズ | CDN |
|---|---|---|---|
| 2 | GSAP v3 | ~23KB | `https://cdn.jsdelivr.net/npm/gsap@3/dist/gsap.min.js` |
| 3 | p5.js v1 | ~80KB | `https://cdn.jsdelivr.net/npm/p5@1/lib/p5.min.js` |
| 3 | Chart.js v4 | ~70KB | `https://cdn.jsdelivr.net/npm/chart.js@4/dist/chart.umd.min.js` |
| 3 | Matter.js | ~30KB | `https://cdn.jsdelivr.net/npm/matter-js@0.20/build/matter.min.js` |

#### サウンド
| Tier | ライブラリ | サイズ | CDN |
|---|---|---|---|
| 1 | ZzFX | **~1KB** | `https://cdn.jsdelivr.net/npm/zzfx@1/ZzFX.min.js` |
| 2 | Howler.js | ~10KB | `https://cdn.jsdelivr.net/npm/howler@2/dist/howler.min.js` |
| 3 | Tone.js | ~150KB | `https://cdn.jsdelivr.net/npm/tone@14/build/Tone.min.js` |

#### カメラ・ML
| Tier | ライブラリ | サイズ | 用途 |
|---|---|---|---|
| 1 | getUserMedia（標準API） | 0KB | カメラ映像・撮影 |
| 3 | ml5.js v1 | ~200KB+モデル | 顔/手/体検出 |

ml5.jsのモデル：
- `ml5.faceMesh()` — 顔468ランドマーク
- `ml5.handPose()` — 手21ランドマーク
- `ml5.bodyPose()` — 全身ポーズ

#### 音声
| Tier | 方法 | サイズ |
|---|---|---|
| 1 | Web Speech API（標準） | 0KB |
| 1 | speechSynthesis（標準） | 0KB |

#### 3D
| Tier | ライブラリ | CDN |
|---|---|---|
| 3 | Three.js | importmap: `https://cdn.jsdelivr.net/npm/three@0.170/build/three.module.min.js` |

#### ユーティリティ
| 機能 | ライブラリ | CDN |
|---|---|---|
| QRコード | qrcode-generator | `https://cdn.jsdelivr.net/npm/qrcode-generator@1.4.4/qrcode.js` |

---

## フェーズ3：REQUIREMENTS.md 作成

```markdown
# 要件定義書

## 概要
（一言説明）

## ターゲット
（誰が・どこで使うか）

## コア機能（Must）
-

## UI方針
（シンプル／ダーク／カラフル）

## 参考にした類似ツール
| ツール名 | URL | 参考にする点 |
|---|---|---|

## 採用ライブラリ（Opusが選定）
| 機能 | ライブラリ | Tier | CDN | 採用理由 |
|---|---|---|---|---|

## 組み合わせパターン
（TIER_TABLEのどのパターンに近いか）

## 実装方針
- 単一index.htmlで完結
- ローカルサーバー必要？（音声認識などfile://不可の場合）

## Sonnetへの引き継ぎメモ
（buildエージェントへの具体的な実装指示）
```

## 完了後
- REQUIREMENTS.mdを作成
- PROGRESS.mdに記録
- 「✅ 要件定義完了！/build を実行してください」と伝える
