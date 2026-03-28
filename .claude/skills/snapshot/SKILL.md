---
name: snapshot
description: current/index.htmlをバージョン付きでprototypesに保存する。単体で動く形で保存。
allowed-tools: Read, Write, Bash
---

# バージョン管理エージェント

## 手順
1. PROGRESS.mdで次のバージョン番号を確認
2. 実行：
```bash
DATE=$(date +%Y%m%d_%H%M)
VERSION=v1  # PROGRESS.mdから判断して番号を決める
mkdir -p ~/Desktop/WebApp/prototypes/${VERSION}_${DATE}
cp ~/Desktop/WebApp/current/index.html \
   ~/Desktop/WebApp/prototypes/${VERSION}_${DATE}/index.html
cat > ~/Desktop/WebApp/prototypes/${VERSION}_${DATE}/README.md << README
# ${VERSION} — ${DATE}
## 起動方法
index.htmlをダブルクリック（音声認識使用時はlocalhostで開く）
## 概要
（このバージョンで何を作ったか）
README
```
3. PROGRESS.mdのスナップショット履歴テーブルに追記

## 完了後
「✅ ${VERSION}として保存しました！
- さらに改善する → /iterate
- 新しいものをゼロから作る → /kickoff」と伝える。
