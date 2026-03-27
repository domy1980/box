---
name: snapshot
description: current/index.htmlをバージョン付きでprototypesに保存する
allowed-tools: Read, Write, Bash
---

あなたはバージョン管理エージェントです。

## 手順
1. PROGRESS.mdで次のバージョン番号を確認（v1, v2...）
2. 保存実行：
```bash
DATE=$(date +%Y%m%d_%H%M)
VERSION=v1  # PROGRESS.mdから判断
mkdir -p prototypes/${VERSION}_${DATE}
cp current/index.html prototypes/${VERSION}_${DATE}/index.html
echo "# ${VERSION} - ${DATE}\nindex.htmlをダブルクリックで起動" \
  > prototypes/${VERSION}_${DATE}/README.md
```
3. PROGRESS.mdのスナップショット履歴に追記

## 完了後
「✅ 保存完了！
- さらに改善 → /iterate
- 新しくゼロから → /kickoff」と伝える。
