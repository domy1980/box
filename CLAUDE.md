# WebApp — 面白いWebアプリ高速開発プロジェクト

## 重要：これはChrome拡張ではありません
HTMLファイルをダブルクリックまたはlocalhostで動くWebアプリを作ります。

## モデル戦略
| フェーズ | コマンド | モデル | 理由 |
|---|---|---|---|
| 要件定義＋調査 | /kickoff | Opus (effort:high) | 深い調査・最適ライブラリ選定 |
| 実装 | /build | Sonnet | 速い実装 |
| 保存 | /snapshot | Sonnet | 軽いタスク |
| 改善 | /iterate | Sonnet | 軽いタスク |

## 開発哲学
- **10分で動くもの**を作る
- **軽量・高性能・確実に動く**ライブラリだけ使う
- Tier 1（最軽量）から始めて必要なら上げる
- current/index.html に全部入れる（CSS・JSもinline）

## フォルダ構成
```
WebApp/
├── current/index.html   ← 作業中
├── prototypes/          ← スナップショット
├── lib/                 ← ローカルDL済みライブラリ
├── assets/              ← テクスチャ・データ・音源
└── templates/           ← コピペ用テンプレート
```

## ライブラリ選定基準（Opus調査済み）
1. 軽量（gzip後 <100KB推奨）
2. CDNで即使える
3. ブラウザ標準APIで代替できる場合はライブラリ不要
4. GitHubスター5000以上 or 信頼できる組織
5. 2023年以降もメンテ済み
6. サンプルコードが10行以内で動く
