# v2 — 20260328_1432

## 起動方法
```
cd /Users/doieisuke/Desktop/WebApp
python3 -m http.server 8080
→ http://localhost:8080/prototypes/v2_20260328_1432/index.html
```

## 概要
箱男ゲーム フルUI版（音楽・効果音・イントロ対応）

- 一人称視点（穴は常に中央表示）
- Howler.js による BGM/SE mp3 再生
- ZzFX フォールバック効果音
- リンゴ→マップサイズ表示、バナナ→探索率表示
- スタータイマーバー
- タイトル・説明画面の大型テキスト
- ストーリーイントロ文追加
- 水タイル水色・崖タイル暗黒断崖・木ブロック
- ミニマップ 200px

## 未実装
- ウェブカメラ / ml5.js 頭部トラッキング
