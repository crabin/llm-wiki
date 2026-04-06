# LLM Wiki — AI駆動の個人知識ベース

**言語 / Language / 语言：** [中文](README.md) ｜ [English](README.en.md) ｜ 日本語（デフォルト）

---

## これは何ですか？

LLM Wiki は [Claude Code](https://claude.ai/code) を活用した個人知識管理システムです。ノート、Webクリッピング、論文、日記などの素材を `raw/` に置くだけで、AIが自動的に構造化・相互リンクされた wiki ページとして `wiki/` に整理します。

**核心的な考え方：**
- `raw/` は原本を保存するだけで、絶対に変更しない
- `wiki/` は AI が書き・維持し、ページ同士がリンクでつながる
- 毎回の取り込みで知識ベース全体が豊かになる — 孤立したアーカイブではない

---

## 仕組み

```
あなたのノート / Webページ / 論文
        ↓
   raw/  （読み取り専用の原本レイヤー）
        ↓  Claude Code が処理
   wiki/ （AI が管理する構造化ページ）
        ↓
   index.md（全体ナビ） + log.md（操作履歴）
```

AI は処理中に以下を行います：
1. 重要概念を抽出し、wiki ページを作成・更新
2. `agent-browser` を使って権威ある Web ソースを取得し、内容を補完
3. ページ間に `[[双方向リンク]]` を構築
4. 価値ある分析を `concepts/` に保存

---

## クイックスタート

### 前提条件

- [Claude Code](https://claude.ai/code) CLI
- （任意）[agent-browser](https://github.com/mediar-ai/agent-browser)：Web検索強化に使用
  ```bash
  brew install mediar-ai/agent-browser/agent-browser
  ```

### インストール

```bash
git clone https://github.com/crabin/llm-wiki.git my-wiki
cd my-wiki
```

Claude Code でプロジェクトを開く：
```bash
claude .
```

### 使い方

1. **素材を追加**：ノート、PDF、Webクリッピングを `raw/` の適切なサブディレクトリに配置
2. **取り込み（Ingest）**：Claude Code に「raw/xxx.md を取り込んで」と伝える
3. **クエリ（Query）**：直接質問するだけ — AI が wiki を参照して回答
4. **ヘルスチェック（Lint）**：「wiki を確認して」と言うと、AI がリンク切れや孤立ページを修正

---

## ディレクトリ構造

```
llm-wiki/
├── CLAUDE.md          # AI 動作ルール（中核設定）
├── index.md           # 全体インデックス（AI が管理）
├── log.md             # 操作ログ（AI が追記）
├── raw/               # 原本素材（読み取り専用、git には含めない）
│   ├── Clippings/     # Webクリッピング
│   ├── Notes/         # 学習ノート
│   ├── Personal/      # 個人日記・メモ
│   └── ...
├── wiki/              # AI が管理する wiki ページ
│   ├── topic-a.md
│   └── topic-b.md
├── concepts/          # 生成された分析レポート・回答ページ
└── .claude/
    └── skills/
        └── agent-browser/  # ブラウザ自動化スキル
```

---

## 3つの操作モード

### 取り込み（Ingest）
```
あなた：raw/notes/machine-learning.md を取り込んで
AI：ファイルを読む → Web で補完 → wiki ページを作成・更新 → index 更新 → log に記録
```

### クエリ（Query）
```
あなた：連合学習とは何ですか？プライバシー保護とどう関係していますか？
AI：wiki を参照 → 必要なら Web 検索 → 回答を統合 → 価値ある答えは concepts/ に保存
```

### ヘルスチェック（Lint）
```
あなた：wiki を確認して（または：lint）
AI：リンク切れ・孤立ページ・欠落した相互参照をスキャン → レポート出力 → 修正 → log に記録
```

---

## wiki ページのフォーマット

すべての wiki ページは統一フォーマットに従います：

```markdown
---
title: トピック名
tags: [tag1, tag2]
sources: 3
updated: 2026-04-06
---

# トピック名

## 定義
...

## 重要なポイント
...

## 関連トピック
- [[related-topic-a]] - 説明
- [[related-topic-b]] - 説明

## ソース
- ローカルファイル：raw/xxx/yyy.md
- Web：https://example.com/article
```

---

## 設計思想

**知識の複利効果**：取り込みは単なるアーカイブではなく、新しい知識を既存のネットワークに編み込む作業です。100回目の取り込み時、AI は新しいノートと50の既存ページとのつながりを発見できます。

**原本は不変**：`raw/` は常にそのまま保持されます。wiki は AI の解釈レイヤーであり、原本の代替ではありません。

**検証可能なソース**：すべてのページにソース（ローカルファイルパスまたは URL）を明記し、AI が根拠なく内容を生成することを防ぎます。

---

## ライセンス

MIT
