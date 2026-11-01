---
name: commit-and-pr
description: 規約に沿ってコミットし、区分を明記した PR を作成する。テストの成功後に使う。
---

## 入力

作業ブランチの変更。

## 手順

1. `docs/harness/git-workflow.md` を読む。
2. 秘密情報や不要なファイルが含まれていないか確認する。
3. 未コミットの変更を Conventional Commits に沿ってコミットする。`main` には直接コミットしない。
4. ブランチを push する。
5. `.github/pull_request_template.md` の形式で PR を作成する。タイトルも Conventional Commits にする。

## 成果物

コミットと PR。

## 完了条件

PR に区分、なぜ、何を変えるか、確認方法が書かれている。

## 次の工程

`ai-review` に進む。PR はマージしない。
