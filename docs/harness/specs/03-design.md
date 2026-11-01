# 設計書: agent-harness-template

- 工程: 3. 設計
- 状態: 確認済み
- 更新日: 2026-11-01
- 入力: [要求文書](01-requirements.md)、[要件定義書](02-system-requirements.md)

## 1. 設計方針

- **正本を 1 か所にする:** ツールごとに異なるのは「入口」だけとし、内容は共通のファイルに置く。
- **所有者でファイルを分ける:** テンプレート管理のファイルと利用者のファイルを分け、更新時に衝突させない。
- **生成処理を持たない:** ビルド、同期スクリプト、シンボリックリンクを使わない。

## 2. ファイル構成

```text
.
├── AGENTS.md                         [T] 共通の指示の正本
├── PROJECT.md                        [U] プロジェクト固有の指示
├── README.md                         [U] プロジェクトの説明
├── .claude/
│   ├── agents/ai-reviewer.md         [T] Claude Code 用レビューエージェント
│   └── skills/                       [T] スキルの正本(利用者のスキルも同居可)
│       ├── elicit-requirements/SKILL.md
│       ├── define-requirements/SKILL.md
│       ├── design-system/SKILL.md
│       ├── implement/SKILL.md
│       ├── run-tests/SKILL.md
│       ├── ai-review/SKILL.md
│       ├── commit-and-pr/SKILL.md
│       └── setup-project/SKILL.md
├── .github/
│   ├── agents/ai-reviewer.agent.md   [T] Copilot 用レビューエージェント
│   └── pull_request_template.md      [T] PR テンプレート
└── docs/
    ├── specs/                        [U] 各変更の要求、要件、設計
    └── harness/                      [T]
        ├── getting-started.md        利用開始の手順と初期設定
        ├── updating.md               テンプレート更新の取り込み手順
        ├── managed-paths.txt         テンプレート管理のパス一覧
        ├── development-process.md    7 工程と区分の詳細
        ├── git-workflow.md           ブランチ、コミット、PR、マージ
        ├── adding-skills.md          スキルの追加手順
        ├── review-criteria.md        AI レビューの観点(正本)
        ├── permissions-and-hooks.md  権限とフックの参考情報
        └── specs/                    本テンプレート自身の要求、要件、設計
```

`[T]` はテンプレート管理で、利用者は編集しない。`[U]` は利用者のファイルで、テンプレートの更新では変更されない。

## 3. 指示の構成

| ファイル | 内容 |
|---|---|
| `AGENTS.md` | 振る舞い、7 工程の概要、区分の判定基準、人間の確認点、git の基本ルール、詳細文書への案内 |
| `PROJECT.md` | 技術スタック、テストとビルドのコマンド、プロジェクト固有のルール |

- `AGENTS.md` の冒頭に、作業の前に `PROJECT.md` を読むよう明記する(SR-12)。
- `AGENTS.md` は常に読み込まれるため短く保ち、詳細は `docs/harness/` に置く。
- `PROJECT.md` と `AGENTS.md` が矛盾する場合は、`PROJECT.md` を優先する。これにより、利用者は `AGENTS.md` を編集せずにルールを調整できる。

## 4. スキルの構成

全スキルを次の形式にする。

```markdown
---
name: <ディレクトリ名と同じ>
description: <何をするか、いつ使うか>
---

## 入力
## 手順
## 成果物
## 完了条件
## 次の工程
```

| スキル | 入力 | 成果物 | 完了後の動作 |
|---|---|---|---|
| `elicit-requirements` | ユーザーの依頼 | `docs/specs/<id>/requirements.md` | 止まって人間の確認を待つ |
| `define-requirements` | 要求文書 | `docs/specs/<id>/system-requirements.md` | 止まって人間の確認を待つ |
| `design-system` | 要件定義書 | `docs/specs/<id>/design.md` | 止まって人間の確認を待つ |
| `implement` | 設計書、または PR の説明欄 | コード | `run-tests` へ進む |
| `run-tests` | 受け入れ条件、`PROJECT.md` のコマンド | テストと結果 | 失敗時は `implement` に戻る。成功時は `commit-and-pr` へ進む |
| `commit-and-pr` | 変更内容 | コミット、PR | `ai-review` へ進む |
| `ai-review` | PR の差分、要件、設計 | PR 上のレビュー結果 | 重要な指摘があれば `implement` に戻る。なければ人間レビューを依頼して止まる |

`<id>` は `YYYYMMDD-<topic>` とする。

### VS Code の Copilot セッションへの対応(SR-23)

実装の最初に実機で確認し、結果に応じて次のとおり対応する。

| 確認結果 | 対応 |
|---|---|
| `.claude/skills/` を読む | 追加の対応なし |
| 読まない | `.github/skills/` に、`name` と `description`、正本を読む指示だけを書いたファイルを置く。ただし、両方を読む Copilot CLI でスキルが重複しないことを確認する |
| 読まず、重複も避けられない | 実装を止めて人間に相談する |

## 5. AI レビューの構成

```text
ai-review スキル
  ├─ Claude Code → .claude/agents/ai-reviewer.md ─────┐
  └─ Copilot     → .github/agents/ai-reviewer.agent.md ┴→ docs/harness/review-criteria.md
```

- エージェントの定義にはツール固有の設定と「観点の正本を読む」指示だけを書く(SR-31)。
- サブエージェントとして実行し、実装時の会話を引き継がない(SR-30)。実験的機能である VS Code の `context: fork` は使わない。
- 差分を読むためにシェルを許可し、ファイルの編集ツールは与えない。Copilot 側には `include-custom-instructions: true` を設定する(SR-32)。
- VS Code は両方のディレクトリを読む可能性があるため、エージェントが重複して表示されないか実機で確認する。
- 観点は、要件と設計との一致、不具合、テストの不足、セキュリティ、区分の妥当性とする。指摘は「必須」「推奨」「参考」の 3 段階で報告する。

## 6. git 運用

| 項目 | ルール |
|---|---|
| ブランチ | `main` から `<type>/<topic>` を作成する(例: `feat/password-reset`) |
| 単位 | 1 つの変更につき 1 ブランチ、1 PR。文書とコードを同じ PR に含める |
| コミット | Conventional Commits |
| マージ | Squash merge。PR タイトルも Conventional Commits にする |
| マージの実行者 | 人間のみ。エージェントはマージしない |
| マージ後 | ブランチを削除する |

工程と git の流れは次のとおり。

1. 工程 1 の開始時にブランチを作成する。
2. 工程 1〜3 の文書を順に作成してコミットする。人間の確認を得たら、文書の「状態」を「確認済み」にする。
3. 工程 4、5 を実施してコミットする。
4. PR を作成し、工程 6、7 を実施する。

緊急区分では修正を先にマージし、工程 1〜3 の文書は別の `docs:` PR で追加する。PR の説明欄に事後対応の予定を書く。

### ブランチ保護

`Use this template` ではリポジトリの設定が引き継がれないため、`getting-started.md` に手順を記載する(SR-05)。

- `main` への直接 push を禁止し、PR を必須にする。
- チームでは承認を 1 件以上必須にする。GitHub では PR の作成者が自分の PR を承認できないため、1 人で開発する場合は承認を必須にせず、本人の確認とマージを人間レビューとして扱う。
- Free プランの private リポジトリではブランチ保護を使えない場合がある。使えない場合は `AGENTS.md` のルールだけで運用することになる。

## 7. 利用開始と更新

### 利用開始

1. `Use this template` でリポジトリを作成する。
2. エージェントに初期設定を依頼し、`setup-project` で `README.md` と `PROJECT.md` を書き換える。GitHub Actions による自動置き換えは、権限が必要で失敗する場合があり、第 1 章の「生成処理を持たない」にも反するため採用しない。
3. リポジトリの設定でブランチ保護を有効にする。

### 更新の取り込み

マージではなく、管理パスのファイルだけをテンプレートの内容で置き換える。履歴が別でも使え、利用者のファイルは変更されないためコンフリクトも起きない。

```shell
git remote add template https://github.com/<owner>/agent-harness-template.git  # 初回のみ
git fetch template
git switch -c chore/update-harness
git show template/main:docs/harness/managed-paths.txt | git restore --source=template/main --staged --worktree --pathspec-from-file=-
git diff --cached
```

差分を確認してコミットし、通常どおり PR を作成する。

`managed-paths.txt` には、`AGENTS.md`、スキルごとのディレクトリ、`docs/harness` などを 1 行ずつ記載する。`.claude/skills` 全体を指定すると、利用者が追加したスキルを削除するおそれがあるため指定しない。

制約は次のとおり。

- テンプレートで削除したファイルは、利用先に残る。削除が必要な場合は、更新内容に記載して手動で削除してもらう。
- `[T]` のファイルを利用者が編集していた場合、その変更は上書きされる。`git diff --cached` で確認できる。

Git 2.39.2 の一時リポジトリで Git Bash と PowerShell 5.1 から実行し、更新の適用、スキルの追加、利用者のスキルと固有ファイルの維持、削除済みファイルの残留を確認した。

## 8. ルールの強制力

フックを同梱しないため、次の違いがある。

| ルール | 強制の手段 | 強さ |
|---|---|---|
| `main` への直接 push の禁止 | ブランチ保護 | 強い(設定した場合) |
| 工程 1〜3 完了時の停止、区分の判定 | `AGENTS.md` とスキル | 弱い(エージェントの遵守に依存) |
| エージェントがマージしない | `AGENTS.md` | 弱い(エージェントの遵守に依存) |
| 人間レビュー | ブランチ保護の承認(チーム)、本人のマージ(個人) | 構成による |

弱いルールは第 9 章で動作を確認する。運用で問題が出た場合はフックの追加を再検討する。

## 9. 実装の順序

1. **事前検証:** 最小構成で第 4 章、第 5 章の未確認事項を実機で確認する。
2. **共通の土台:** `AGENTS.md`、`PROJECT.md`、`docs/harness/`、PR テンプレート。
3. **スキルとエージェント:** 7 スキルと `ai-reviewer`。
4. **動作検証:** 要件定義書の第 4 章を実施する。
5. **公開:** 秘密情報を確認し、GitHub に公開して Template repository に設定する。公開は人間の確認を得て実施する。

## 10. 要件の変更

| 要件 | 変更内容 | 理由 |
|---|---|---|
| SR-02、SR-04、SR-05 | 「README に記載」を「`docs/harness/` に記載し、README からリンクする」に変更する | README は利用者が書き換えるため、手順が失われ、更新時に上書きもできない |
| SR-03 | 区分の一覧も `docs/harness/` に置く | 同上 |
