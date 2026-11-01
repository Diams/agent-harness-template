---
name: setup-project
description: テンプレートから作成したリポジトリの README.md と PROJECT.md をプロジェクト用に書き換える。利用者がプロジェクトの初期設定を依頼したときに使う。
---

## 入力

利用者から聞き取った内容と、リポジトリ内の既存ファイル。

## 手順

1. `git remote get-url origin` を確認する。`template` など他の remote は判定に使わない。`origin` が `Diams/agent-harness-template` 本体であれば、作業せず止まって報告する。`origin` のリポジトリ名が `agent-harness-template` のままの場合は、ハーネス自体のフォークの可能性があるため、続けてよいか利用者に確認する。
2. `main` から `docs/setup-project` ブランチを作成する。区分は文書のみとする。
3. `README.md` にテンプレートの案内以外の内容があれば、上書きしてよいか確認する。
4. 既存のファイルから分かることを読み取り、不足する次の項目を利用者に質問する。不明な項目は推測せず、未定として残す。
   - プロジェクト名と目的
   - 技術スタック
   - ビルド、テスト、リントのコマンド
   - 固有のルール
5. `README.md` を次の構成で置き換える。テンプレートの案内は残さない。
   - プロジェクト名と目的
   - 使い方、または開発の始め方
   - 末尾の「開発の進め方」に `AGENTS.md` と `docs/harness/getting-started.md` へのリンク
6. `PROJECT.md` の各項目を記入する。
7. `LICENSE` はプロジェクトに合っているか利用者に確認するよう伝える。変更はしない。
8. `commit-and-pr` で PR を作成する。

`AGENTS.md` など、`docs/harness/managed-paths.txt` に記載のファイルは編集しない。

## 成果物

書き換えた `README.md` と `PROJECT.md`、その PR。

## 完了条件

`README.md` にテンプレートの案内が残っておらず、`PROJECT.md` の括弧書きのひな形が残っていない。`PROJECT.md` 冒頭の説明文は残す。未定の項目は未定と明記されている。

## 次の工程

`ai-review` に進む。その後、人間にレビューを依頼するときに、`docs/harness/getting-started.md` のブランチ保護の設定がまだであれば、あわせて案内する。
