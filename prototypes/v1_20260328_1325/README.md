# v1 — 20260328_1325

## 起動方法
```
cd /Users/doieisuke/Desktop/WebApp
python3 -m http.server 8080
→ http://localhost:8080/prototypes/v1_20260328_1325/index.html
```

## 概要
箱男ゲーム 初回動作版（音なし）

- Three.js 3Dシーン（立方体 + グリッドマップ）
- Quaternionスラープによるローリングアニメーション
- 6面トラッキング（面ごとの穴メカニクス）
- のぞき穴オーバーレイ（Canvas 2D暗マスク）
- キーボード操作（WASD / 矢印キー）
- アイテム：フルーツ（穴拡大）・水着・スター・鳥
- マップ：GROUND / CLIFF / WATER / ITEM
- ゲームオーバー判定（崖・水）
- 90%探索クリア
- ご褒美画像（Pollinations.ai）
- ミニマップ（鳥アイテム取得後）

## 未実装（次のiterate候補）
- ウェブカメラ / ml5.js 頭部トラッキング
- BGM・効果音
- カメラ向き変更アニメーション改善
