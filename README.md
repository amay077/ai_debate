# AI ディベートシステム

2つの AI エージェントが肯定派・否定派に分かれて討論を行い、オーケストレータが進行・判定するシステム。

## ディベート一覧

| テーマ | ディレクトリ | 状態 |
|--------|-------------|------|
| 論理削除（削除フラグ）の是非 | [20251218_logical-deletion/](20251218_logical-deletion/) | 完了 |

## 使い方

### 新規ディベートの作成

1. `_template/` ディレクトリをコピーして `YYYYMMDD_テーマ名` でリネーム
   ```bash
   cp -r _template/ 20251225_new-topic-name/
   ```

2. 各ファイルのプレースホルダーを置換
   - `{{TOPIC}}`: テーマ名
   - `{{TOPIC_DESCRIPTION}}`: テーマの説明
   - `{{POINT_1}}` 〜 `{{POINT_4}}`: 討論の論点

3. `positions/` 内の事前調査を実施

4. ディベートを実行

### ディベート実行手順

1. **事前準備**: 両派の主張を `positions/` に記載
2. **実行**: AI エージェントに `plan.md` を読み込ませて討論を開始
3. **記録**: 討論内容は `debate_log.md` に自動記録
4. **判定**: オーケストレータが総合判定を下す

## 討論形式

| フェーズ | 内容 | 形式 |
|----------|------|------|
| オープニング | 各派の基本主張 | 並列実行 |
| 論点 1〜4 | 主張 → 反論 → 再反論（2-3往復） | 交互実行 |
| クロージング | 最終主張 | 並列実行 |
| 判定 | オーケストレータによる判定 | 優勢な派を決定 |

## ディレクトリ構成

```
.
├── README.md                      # このファイル
├── _template/                     # テンプレート
│   ├── README.md
│   ├── plan.md
│   ├── debate_log.md
│   ├── result.md
│   └── positions/
│       ├── affirmative.md
│       └── negative.md
│
└── YYYYMMDD_{topic-name}/         # 各ディベート
    ├── plan.md
    ├── debate_log.md
    ├── result.md
    └── positions/
        ├── affirmative.md
        └── negative.md
```
