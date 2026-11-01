# 利用開始の手順

## 必要なもの

- git と GitHub アカウント
- 次のいずれか
  - Claude Code v2.1.277 以降
  - GitHub Copilot CLI
  - GitHub Copilot in VS Code

## 手順

1. GitHub で `Use this template` を選び、リポジトリを作成してクローンする。
2. `PROJECT.md` と `README.md` を自分のプロジェクト用に書き換える。
3. 下記のブランチ保護を設定する。

ハーネス自体をカスタマイズする場合や、テンプレートへ改善を提案する場合は、`Use this template` ではなくフォークを使う。

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
