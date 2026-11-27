# 勉強会ネタ backlog

リードが毎回更新する。人間は「フィードバック」欄に自由に追記してよい（次回以降の採点に反映される）。

## 候補（未採用）
| 追加日 | タイトル | スコア | 状態 | メモ |
|---|---|---|---|---|
| 2026-11-23 | `/code-review` 重要度ラベル + `--fix` でセルフレビュー定着（+ `/loop` で CI 見張り） | 18 | TOP1（11/24〜11/27 も TOP1） | reports/2026-11-24.md。Flutter デモ・Copilot 比較は避ける。デモは 2.1.332 推奨。11/26: 用語「routine」→「cloud scheduled task」に合わせる。VS Code で `/rewind` すると `/loop` が止まる点に注意。11/27: 2.1.330 で `/loop`・セッション内タスクの上限 7→30 日を 1 行補足（セッション継続中のみ、cloud scheduled task と混同しない、「終了時に通知」と断定しない） |
| 2026-11-23 | Claude Code の許可確認との付き合い方（deny 設定 + `PreSkillShellCommand` hook） | 16 | TOP3（11/26 に次点から再浮上、11/27 据え置き） | reports/2026-11-26.md。11/26 に 2.1.332 の `PreSkillShellCommand` と `/permissions` 改善を統合（15→16）。auto mode は社内で有効な場合のみ。MCP skill の防御として紹介しない。「以前は止められなかった」と言わない（`disableSkillShellExecution` があった） |
| 2026-11-23 | CocoaPods trunk read-only（12/2）と Flutter の SwiftPM 対応 | 14 | 次点（11/25 に TOP3 から降格） | 11/24 に 12/2 を一次確認（13→14）。11/25 に「3.44 から SwiftPM 既定」も Flutter docs で一次確認。フォールバック一覧化コマンドは docs になくデモは手作業。12/2 前開催なら Flutter 3.50 枠の最後に 1 枚で触れる。11/27 更新なし |
| 2026-11-24 | VS Code 拡張「Fix with Claude Code」「Resolve Merge Conflicts」 | 16 | 次点（11/25 に TOP2 → TOP3、11/26 に同点比較で次点） | 2.1.330/2.1.331。2.1.331 の「Add Cell to Chat」も補足可。Copilot 比較表は作らない、Flutter 以外のリポジトリでデモ |
| 2026-11-24 | `gh event listen` で PR レビュー・CI 完了をポーリングなしで待つ | 14 | 保留 | gh v2.105.0（GitHub Releases 上は 11/18）、preview・ウェイトリスト制。通過していれば 16 点相当。v2.106.0（11/25）でも preview のまま |
| 2026-11-25 | Flutter 3.50 に上げるときに踏むもの（material_ui / cupertino_ui 移行 + 3.50.1 の Android ビルド修正） | 17 | TOP2（11/26・11/27 も TOP2） | reports/2026-11-25.md。3.51 beta は中身なし（11/26 確認）。3.50 は 11/11 告知（タグ 11/9）。「今は警告のみ」と煽らない。CocoaPods は同じ枠に入れない。社内の Flutter バージョンと共通パッケージの有無を確認 |
| 2026-11-25 | GitHub Actions ランナーイメージの 11 月の変更と `-latest` ラベルの棚卸し | 15 | 次点・条件付き | runner-images #14748/#15397/#15315/#15253。Go 削除は「11/18 以降」、Java 25 既定は要確認。社内 CI が Actions なら 16。11/26: #15373（VS 2027、VS 2026 ラベル非推奨化 12/1〜）は記述が自己矛盾、点は据え置き。11/27: #15368 は起票者が自分で改題・Close（メンテナ対応なし）、追跡終了 |
| 2026-11-25 | Backlog MCP Server を Claude に繋ぐ最小構成と toolset の絞り方 | 14 | 保留（定番 Tips） | 初版 2025-04-21 で新しさ 1。新ネタが少ない回の予備。API キー発行・外部 MCP 利用の社内許可を確認。CVE 番号は NVD/GHSA で要確認。v0.23.4（11/18）は依存更新のみ、skills 非対応 |
| 2026-11-26 | Claude Code「Skills over MCP」（public beta、2.1.332） | 14 | 保留 | Team 組織は Owner が有効化するまで OFF、社内 MCP が skills を返すか未確認。モデル要件・feature flag fetching あり。docs「2.1.331 以上」は誤りの可能性、2.1.332 以上で統一。デモ候補 olaservo/skills-over-mcp-demo が動けば 15。`PreSkillShellCommand` と結び付けない。11/27: GitHub MCP Server v2.2.0（11/16）が skills に opt-in 対応（サーバー `--features=skills` とクライアント `MCP_SKILLS=true` の両方が必要、Claude Code 言及なし、server instructions が置き換わる副作用）。Claude Code で `github:<skill>` が出ればリハーサル確認後 16 程度（推測） |
| 2026-11-27 | Claude の Computer use（Team / Enterprise 向け beta、11/20） | 13 | 保留 | support.claude.com リリースノート。既定 OFF・Owner のみ有効化・Team は全員に適用・サンドボックスなし・Claude Code 非対応・macOS 14+ / Windows。Owner が有効化すれば 16 相当（推測）。Monthly recap（Team 対象外）や artifact アプリ化と束ねない |

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
| 2026-11-26 | gh v2.106.0（脆弱性 7 件修正、`gh auth logout` のトークン失効） | 単独では 15 分にならない。深刻度・`GH_TOKEN` の扱いが未記載でデモは危険。勉強会とは別に即時周知を推奨 |
| 2026-11-27 | Vite v8.4.0（`server.fs.strict: 'extended'` ほか） | 既定 `true` で既に制限済みで強化幅が小さい、後続修正 PR #24206 で挙動未確定、前提説明が重い、Web 限定。社内 Web が Vite 8 なら「8.4 へ上げるときの確認点」として出し直し可（14 程度） |
| 2026-11-26 | claude-code-action v1.0.288（`allow_fork_prs` 既定 false） | 社内利用未確認、デモの見せ場なし。カスタム `github_token` の `pull-requests: read` 不足で private でも影響し得る点は別途確認 |

## フィードバック（人間が記入）
<!-- 例: 「AI ツール比較系はもう飽和気味」「デモありのネタが好評だった」など -->
