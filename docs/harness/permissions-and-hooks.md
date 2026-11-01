# 権限とフックの参考情報

本テンプレートは権限とフックの設定を同梱しない。必要な場合は利用先で追加する。以下は 2026-11-01 時点の公式ドキュメントの内容で、変更されている可能性がある。

| 項目 | Claude Code | Copilot CLI | Copilot in VS Code |
|---|---|---|---|
| フック | `.claude/settings.json` | `.github/hooks/*.json` | `.github/hooks/*.json` など(プレビュー機能) |
| 権限 | `.claude/settings.json` の `permissions` | `preToolUse` フックなどを公式ドキュメントで確認する | `preToolUse` フックで拒否できる |

## 注意点

- VS Code の Copilot セッションは `.claude/settings.json` のフックも読む。Claude Code 用のフックが Copilot のセッションでも実行される可能性がある。
- Copilot CLI のフックを Windows で使うには PowerShell 7.0 以降が必要になる。
- フックのコマンドは OS ごとに用意する必要がある場合がある。

## 公式ドキュメント

- [Claude Code: hooks](https://code.claude.com/docs/en/hooks)
- [Claude Code: settings](https://code.claude.com/docs/en/settings)
- [Copilot CLI: hooks](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks)
- [VS Code: hooks](https://code.visualstudio.com/docs/copilot/customization/hooks)
