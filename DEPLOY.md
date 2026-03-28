# デプロイ手順書

## 構成

```
WebApp/
├── index.html          ← これがゲーム本体（ルートに置く）
├── assets/
│   ├── bgm_game.mp3
│   ├── bgm_clear.mp3
│   ├── bgm_gameover.mp3
│   └── sounds/         ← 効果音 mp3
├── .github/
│   └── workflows/
│       └── pages.yml   ← 自動デプロイ設定（触らなくてOK）
└── current/index.html  ← 開発中ファイル（こっちで作業する）
```

---

## 初回セットアップ（一回だけ）

### 1. GitHubにリポジトリを作る
- https://github.com/new でリポジトリ作成
- リポジトリ名を決める（例: `box`）
- Public にする（GitHub Pages は Public が必要）

### 2. ローカルと紐付け
```bash
git remote add origin https://github.com/ユーザー名/リポジトリ名.git
```

### 3. GitHub Pages を有効化
- https://github.com/ユーザー名/リポジトリ名/settings/pages を開く
- **Source** → `GitHub Actions` を選択 → Save

これで完了。以降は push するだけで自動デプロイされる。

---

## 毎回の更新手順

### current/index.html で開発 → 動作確認 → デプロイ

```bash
# 1. 開発版をルートにコピー（パスを ./assets/ に修正済みなので不要な場合も）
cp current/index.html index.html

# 2. ステージング
git add index.html assets/

# 3. コミット
git commit -m "更新内容のメモ"

# 4. プッシュ → 自動でデプロイ開始
git push
```

### デプロイ完了確認
- https://github.com/ユーザー名/リポジトリ名/actions を開く
- ✅ 緑のチェックがついたら完了
- https://ユーザー名.github.io/リポジトリ名/ でプレイできる

---

## 注意点

| ポイント | 内容 |
|---|---|
| index.html の場所 | **リポジトリのルート**に置く（docs/ や current/ ではない） |
| assets のパス | `./assets/` と書く（`../assets/` だとGitHub Pagesで404になる） |
| リポジトリは Public | Private だと GitHub Pages は使えない（無料プランの場合） |
| デプロイ反映時間 | push 後 1〜2 分かかる |
| 音声・カメラ | HTTPS 必須。GitHub Pages は自動で HTTPS なので OK |

---

## 今回のURL

- リポジトリ: https://github.com/domy1980/box
- ゲームURL: https://domy1980.github.io/box/
- 自動デプロイ設定: `.github/workflows/pages.yml`（push のたびに自動実行）
