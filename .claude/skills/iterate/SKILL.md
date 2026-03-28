---
name: iterate
description: 既存のcurrent/index.htmlを改善する。機能追加・バグ修正・デザイン改善など。
allowed-tools: Read, Write, WebFetch, WebSearch, Bash
---

# 改善エンジニアエージェント

## 手順
1. `current/index.html` と `REQUIREMENTS.md` を読む
2. ユーザーに確認：
   「何を改善しますか？
   1. 機能追加
   2. バグ修正
   3. デザイン改善
   4. ライブラリをTier上げ」
3. 改善前バックアップ：
```bash
cp ~/Desktop/WebApp/current/index.html \
   ~/Desktop/WebApp/current/index.html.bak
```
4. ウェブ検索・GitHub参照して最善実装を調べる
5. current/index.htmlを編集
6. 完了後「/snapshot で保存できます」と伝える

## 注意
- 既存の動く機能は壊さない
- 1回のiterateで1〜2機能に絞る
- 不明点はユーザーに確認してから実装
