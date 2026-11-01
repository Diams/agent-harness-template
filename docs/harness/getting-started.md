# 利用開始の手順

## 必要なもの

- git と GitHub アカウント
- 次のいずれか
  - Claude Code v2.1.277 以降
  - GitHub Copilot CLI
  - GitHub Copilot in VS Code

## 手順

1. GitHub で `Use this template` を選び、リポジトリを作成してクローンする。
2. エージェントに「プロジェクトを初期設定して」と依頼する。`setup-project` スキルが質問に沿って `README.md` と `PROJECT.md` を書き換え、PR を作成するので、確認してマージする。手動で書き換えてもよい。
3. 下記のブランチ保護を設定する。

ハーネス自体をカスタマイズする場合や、テンプレートへ改善を提案する場合は、`Use this template` ではなくフォークを使う。

## 関連文書

- [テンプレートの更新を取り込む手順](updating.md)
- [開発プロセス](development-process.md)
- [git 運用](git-workflow.md)
- [スキルの追加手順](adding-skills.md)
- [権限とフックの参考情報](permissions-and-hooks.md)

## 既知の制約

- VS Code の Local セッションでは、エージェント一覧に `ai-reviewer` が 2 個表示される場合がある。どちらを選んでも同じ観点でレビューする。

## ブランチ保護

`Use this template` ではリポジトリの設定が引き継がれない。リポジトリの Settings で `main` に次を設定する。

- 直接 push を禁止し、PR を必須にする。
- チームで開発する場合は、承認を 1 件以上必須にする。

GitHub では PR の作成者が自分の PR を承認できない。1 人で開発する場合は承認を必須にせず、本人の確認とマージを人間レビューとして扱う。

ご利用のプランや公開範囲によってはブランチ保護を使えない場合がある。その場合は `AGENTS.md` のルールだけで運用する。

## ファイルの区分

| 区分 | ファイル | 編集 |
|---|---|---|
| テンプレート管理 | [managed-paths.txt](managed-paths.txt) に記載のパス | しない。更新時に上書きされる |
| 利用者 | `PROJECT.md`、`README.md`、`docs/specs/`、上記以外のすべて | 自由に編集できる |

ルールを変えたいときは `AGENTS.md` を編集せず、`PROJECT.md` に書く。スキルを追加するときは [adding-skills.md](adding-skills.md) を読む。
