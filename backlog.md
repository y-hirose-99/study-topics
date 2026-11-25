# 勉強会ネタ backlog

リードが毎回更新する。人間は「フィードバック」欄に自由に追記してよい（次回以降の採点に反映される）。

## 候補（未採用）
| 追加日 | タイトル | スコア | 状態 | メモ |
|---|---|---|---|---|
| 2026-11-23 | `/code-review` 重要度ラベル + `--fix` でセルフレビュー定着（+ `/loop` で CI 見張り） | 18 | TOP1（11/24・11/25 も TOP1） | reports/2026-11-24.md。Flutter デモ・Copilot 比較は避ける。デモは 2.1.331 以上 |
| 2026-11-23 | Claude Code の許可確認との付き合い方（auto mode + 無人 deny 設定） | 15 | 次点（11/24 に TOP2 から降格、11/25 も次点） | 社内 Team 組織で auto mode が有効なら VS Code 拡張候補と入れ替え可。2.1.330 の rm 承認必須化の拡大を補足 |
| 2026-11-23 | CocoaPods trunk read-only（12/2）と Flutter の SwiftPM 対応 | 14 | 次点（11/25 に TOP3 から降格） | 11/24 に 12/2 を一次確認（13→14）。11/25 に「3.44 から SwiftPM 既定」も Flutter docs で一次確認。フォールバック一覧化コマンドは docs になくデモは手作業。12/2 前開催なら Flutter 3.50 枠の最後に 1 枚で触れる |
| 2026-11-24 | VS Code 拡張「Fix with Claude Code」「Resolve Merge Conflicts」 | 16 | TOP3・条件付き（11/25 に TOP2 から降格） | 2.1.330/2.1.331。2.1.331 の「Add Cell to Chat」も補足可。Copilot 比較表は作らない、Flutter 以外のリポジトリでデモ |
| 2026-11-24 | `gh event listen` で PR レビュー・CI 完了をポーリングなしで待つ | 14 | 保留 | gh v2.105.0（GitHub Releases 上は 11/18）、preview・ウェイトリスト制。通過していれば 16 点相当。11/25 時点で更新なし |
| 2026-11-25 | Flutter 3.50 に上げるときに踏むもの（material_ui / cupertino_ui 移行 + 3.50.1 の Android ビルド修正） | 17 | TOP2 | reports/2026-11-25.md。3.50 は 11/11 告知（タグ 11/9）。「今は警告のみ」と煽らない。CocoaPods は同じ枠に入れない。社内の Flutter バージョンと共通パッケージの有無を確認 |
| 2026-11-25 | GitHub Actions ランナーイメージの 11 月の変更と `-latest` ラベルの棚卸し | 15 | 次点・条件付き | runner-images #14748/#15397/#15315/#15253。Go 削除は「11/18 以降」、Java 25 既定は要確認。社内 CI が Actions なら 16 |
| 2026-11-25 | Backlog MCP Server を Claude に繋ぐ最小構成と toolset の絞り方 | 14 | 保留（定番 Tips） | 初版 2025-04-21 で新しさ 1。新ネタが少ない回の予備。API キー発行・外部 MCP 利用の社内許可を確認。CVE 番号は NVD/GHSA で要確認 |

## 採用済み（実際に発表した / 予定）
| 日付 | タイトル | 発表者 |
|---|---|---|

## 却下
| 日付 | タイトル | 理由 |
|---|---|---|
| 2026-11-23 | Vite+ 安定版 / Vinext 1.0 | 対象期間外（9/28）、本番非推奨、帰属誤りの可能性、Web 限定 |
| 2026-11-23 | Claude Code `/usage` 改善と idle compaction | 中身が薄くデモ不安定、fast mode 終了（11/30）で陳腐化 |
| 2026-11-24 | Gemini CLI → Antigravity CLI 移行と 11 月更新 | 移行告知は 2026-05-19 で古い。本文未確認、Gemini CLI は開発継続中で軸が定まらない（11/23 保留から却下） |
| 2026-11-24 | Gemini の Gem → skills 移行と skills 共有 | 一次情報本文未取得、当初英語のみ、移行時期の情報が矛盾。日本語対応・日程確認後に再評価可 |
| 2026-11-25 | Flutter 3.50.1 hotfix 単独枠 | 単独では薄い。「Flutter 3.50 に上げるときに踏むもの」に統合 |
| 2026-11-25 | Dart package skills（`dart run skills@ get`） | 新しさ不足（最新 10/16）、社内依存パッケージの skills 同梱が未確認でデモ不成立の恐れ。同梱が増えたら再評価可 |
| 2026-11-25 | Firebase CLI v15.37.0 / Crashlytics MCP | 中核の使い方が検索要約のみ、新要素は filterExpression のみ。firebase.google.com が読め、社内の Crashlytics 利用が確認できれば出し直し可 |

## フィードバック（人間が記入）
<!-- 例: 「AI ツール比較系はもう飽和気味」「デモありのネタが好評だった」など -->
