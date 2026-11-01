# テンプレートの更新を取り込む手順

マージではなく、テンプレート管理のファイルだけをテンプレートの内容で置き換える。Git Bash、macOS や Linux のシェル、PowerShell で同じコマンドを使える。

```shell
git remote add template https://github.com/Diams/agent-harness-template.git  # 初回のみ
git fetch template
git switch -c chore/update-harness
git show template/main:docs/harness/managed-paths.txt | git restore --source=template/main --staged --worktree --pathspec-from-file=-
git diff --cached
```

差分を確認してコミットし、通常どおり PR を作成する。

## 注意点

- テンプレートから削除されたスキルなど、`managed-paths.txt` から外れたファイルは利用先に残る。不要なら手動で削除する。
- テンプレート管理のファイルを編集していた場合、その変更は上書きされる。`git diff --cached` で確認する。

## テンプレートを変更するときの注意

- スキルなどを追加または削除したら、`managed-paths.txt` も更新する。
- `.claude/skills` 全体を `managed-paths.txt` に指定しない。利用者が追加したスキルを削除してしまう。
