# 要件定義書: agent-harness-template

- 工程: 2. 要件定義
- 状態: 確認済み
- 更新日: 2026-11-01
- 入力: [要求文書](01-requirements.md)

## 1. 対象環境

| 環境 | 条件 |
|---|---|
| Claude Code | v2.1.277 以降(`AGENTS.md` の直接読み込みに必要) |
| GitHub Copilot CLI | 検証時点の最新版 |
| GitHub Copilot in VS Code | 検証時点の最新版。Copilot セッションと Local セッション |

## 2. 仕様の確認結果

2026-11-01 時点の公式ドキュメントの内容。「未確認」はドキュメントから判断できない項目で、実機で検証する。

| 項目 | Claude Code | Copilot CLI | Copilot in VS Code |
|---|---|---|---|
| `AGENTS.md` | 読む。ただし `CLAUDE.md` または `CLAUDE.local.md` があると既定では読まない | 読む | 読む |
| `CLAUDE.md` | 読む | 読む | Local は `chat.useClaudeMdFile` が有効なときに読む。Copilot セッションは未確認 |
| `.github/copilot-instructions.md` | 読まない(記載なし) | 読む | 読む |
| 複数の指示ファイル | 上記の条件による | 結合する | 結合する |
| 他ファイルの取り込み | `CLAUDE.md` で `@path`。`AGENTS.md` 内は未確認 | `AGENTS.md` などで `@path` | Markdown リンク。読み込みの保証は未確認 |
| `.claude/skills/` | 読む | 読む | Local は読む。Copilot セッションは未確認 |
| `.github/skills/` | 読まない(記載なし) | 読む | 読む |
| スキルの形式 | `SKILL.md`。`description` を推奨 | `SKILL.md`。`name`、`description` は必須 | `SKILL.md`。`name`、`description` は必須。`name` はディレクトリ名と一致が必須 |
| サブエージェント | `.claude/agents/`。独立したコンテキスト | `.github/agents/`。独立したコンテキスト。既定ではリポジトリの指示に従わない | `.github/agents/`、`.claude/agents/`。Copilot セッションでの `.claude/agents/` の扱いは未確認 |
| フック | `.claude/settings.json` | `.github/hooks/*.json` | `.github/hooks/*.json` など。プレビュー機能。Copilot セッションは `.claude/settings.json` も読む |
| 権限 | `.claude/settings.json` | リポジトリで共有する設定は未確認 | `preToolUse` フックで拒否できる |

出典:

- Claude Code: [memory](https://code.claude.com/docs/en/memory)、[skills](https://code.claude.com/docs/en/skills)、[sub-agents](https://code.claude.com/docs/en/sub-agents)、[hooks](https://code.claude.com/docs/en/hooks)、[settings](https://code.claude.com/docs/en/settings)
- Copilot CLI: [custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions)、[skills](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-skills)、[custom agents](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli)、[hooks](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks)
- VS Code: [instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)、[skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)、[agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents)、[hooks](https://code.visualstudio.com/docs/copilot/customization/hooks)

## 3. システム要件

### 3.1 配布と更新

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-01 | GitHub の Template repository として公開する | `Use this template` で新規リポジトリを作成できる | F1 |
| SR-02 | `docs/harness/` に利用開始手順を記載し、README からリンクする。追加ツールとして git、GitHub、対象エージェント以外を要求しない | 手順どおりに新規リポジトリで利用を開始できる | F1 |
| SR-03 | テンプレートが管理するファイルと利用者が編集するファイルを区別する | ファイルの区分が `docs/harness/` に一覧されている | F2, F3 |
| SR-04 | テンプレートの更新を取り込む git の手順を `docs/harness/` に記載する | 固有設定を追加したリポジトリで更新を取り込め、固有設定のファイルでコンフリクトが起きない | F3 |
| SR-05 | リポジトリの設定を要する項目(ブランチ保護など)を `docs/harness/` に記載する | 初期設定の手順に含まれている | F1, F7 |

### 3.2 指示

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-10 | 共通の指示の正本を、リポジトリ直下の `AGENTS.md` とする | 正本以外に同じ指示の本文がない | F4 |
| SR-11 | `AGENTS.md` に振る舞い、7 工程の概要、区分の判定基準、git の基本ルールを記載する | 第 4 章の環境で内容に沿って回答する | F4, F7, F8, F9 |
| SR-12 | プロジェクト固有の指示を別ファイルに置き、`AGENTS.md` から読むよう明示する。ツール固有の自動取り込みには依存しない | 第 4 章の環境で固有ファイルの内容に従う | F2 |

### 3.3 スキル

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-20 | 初期スキル 8 個を同梱する | 要求文書 6.6 のスキルがすべて存在する | F5 |
| SR-27 | `setup-project` は `README.md` からテンプレートの案内を除き、プロジェクト用に置き換える。`PROJECT.md` も記入する | 実行後の `README.md` と `PROJECT.md` にテンプレートの案内とひな形の文言が残らない | F1, F2 |
| SR-21 | スキルの正本を `.claude/skills/<name>/SKILL.md` とする | スキルの本文が 1 か所にある | F5, F6 |
| SR-22 | `name` と `description` を必須とし、`name` は英小文字、数字、ハイフンのみでディレクトリ名と一致させる。ツール固有の項目は、無視されても動作するものに限る | 全スキルが規則を満たす | F5, F6 |
| SR-23 | VS Code の Copilot セッションが `.claude/skills/` を読まない場合は、対応策を設計で定める | 第 4 章の検証結果に対応策がある | F6 |
| SR-24 | スキルの追加手順を文書化する | 手順どおりに追加したスキルを各環境で呼び出せる | F5 |
| SR-25 | 各スキルに、入力、手順、成果物、完了条件、次の工程を記載する | 全スキルが項目を満たす | F8, F9 |
| SR-26 | シンボリックリンクと OS 固有のスクリプトを必須としない | ファイル構成を確認する | 非機能 |

### 3.4 AI レビュー

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-30 | `ai-review` は実装時の会話を引き継がないコンテキストで実行する | 第 4 章の環境で、実装時の会話に依存せずレビューできる | F9 |
| SR-31 | レビュー用エージェントは `.claude/agents/` と `.github/agents/` に定義し、観点の本文は 1 か所に置く | 両方が同じ観点を参照している | F6 |
| SR-32 | Copilot のレビュー用エージェントでリポジトリの指示を有効にする(`include-custom-instructions: true`) | 定義に設定がある | F6, F9 |
| SR-33 | レビューでは要件、設計、差分を照合し、指摘を重要度付きで報告する | レビュー結果の形式を確認する | F8 |

### 3.5 git 運用

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-40 | GitHub Flow のブランチ、PR、マージ、ブランチ削除のルールを文書化する | 文書に各項目がある | F7 |
| SR-41 | Conventional Commits の type と書式を文書化する | 文書に各項目がある | F7 |
| SR-42 | 区分、なぜ、何を変えるか、確認方法を含む PR テンプレートを同梱する | PR 作成時にテンプレートが表示される | F7, F8 |
| SR-43 | `commit-and-pr` は規約に沿ったコミットと PR を作成する | 作成結果が規約に沿っている | F7, F9 |

### 3.6 開発プロセス

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-50 | 7 工程それぞれの入力、成果物、完了条件を文書化する | 文書に全工程の項目がある | F8 |
| SR-51 | エージェントは作業の開始時に区分を判定し、提示する | 区分の異なる依頼で適切に判定する | F8, F9 |
| SR-52 | 標準区分では工程 1、2、3 の完了時に止まり、人間の確認を待つ | 確認なしに次の工程へ進まない | F8, F9 |
| SR-53 | 工程の成果物を保存する場所を定める | 文書に保存場所がある | F8 |
| SR-54 | 緊急区分で工程 1〜3 を事後に実施する方法を定める | 文書に手順がある | F8 |

### 3.7 権限、フック、安全性

| ID | 要件 | 検証方法 | 対応 |
|---|---|---|---|
| SR-60 | 初期版にはフックを同梱しない | フックの設定ファイルがない | F6 |
| SR-61 | 初期版には権限の設定を同梱しない。利用者が追加する場合の参考情報を文書に記載する | 権限の設定ファイルがなく、文書に参考情報がある | F6 |
| SR-62 | 秘密情報を含めない | 公開前に確認する | 非機能 |

## 4. 動作検証

以下の環境で、新規リポジトリを使って検証する。

| 検証項目 | Claude Code | Copilot CLI | VS Code Copilot セッション | VS Code Local セッション |
|---|---|---|---|---|
| `AGENTS.md` の指示に従う | 対象 | 対象 | 対象 | 対象 |
| 固有ファイルの指示に従う | 対象 | 対象 | 対象 | 対象 |
| 全スキルを呼び出せる | 対象 | 対象 | 対象 | 対象 |
| `ai-review` が独立して動く | 対象 | 対象 | 対象 | 対象 |
| 工程 1〜3 の完了時に止まる | 対象 | 対象 | 対象 | 対象 |

検証は Windows で実施する。macOS と Linux は、SR-26 を満たしていることのファイル構成の確認で代える。

## 5. 決定事項

1. **要求文書 6.2 の変更:** 「各ツールのファイルから `AGENTS.md` を参照する」を「`AGENTS.md` のみを置き、`CLAUDE.md` と `.github/copilot-instructions.md` は置かない」に変更した。3 環境とも `AGENTS.md` を直接読めるため参照用のファイルは不要で、Copilot CLI と VS Code では指示が重複して読み込まれる可能性も避けられる。
2. **Claude Code の最低バージョン:** v2.1.277 以降を必須とする。古い版に対応する場合は `CLAUDE.md` が必要になる。
3. **フックと権限(SR-60、SR-61):** 初期版では同梱しない。両ツールの仕組みが異なり、`.claude/settings.json` を VS Code の Copilot セッションも読むなど動作が複雑なため。
