# trend-scout 調査メモ（2026-11-27 JST 実行）

- 対象期間: 前回レポート（reports/2026-11-26.md、11/26 朝）以降。実質 2026-11-26〜27 の約 1 日分
- 重複チェックの対象: CLAUDE.md の過去テーマ 3 件、backlog.md の候補 9 件・却下 9 件
- 読めたホスト: code.claude.com / github.com / raw.githubusercontent.com / support.claude.com。今回は ENOTFOUND の出るホストには当たらなかった（前回までに ENOTFOUND だった github.blog / nulab.com / modelcontextprotocol.io は試していない）

## 結論

- **新規候補: 1 件だけ（弱い）**。Vite v8.4.0（11/26 stable）。Web 限定で中身は小粒なので、単独枠になるかはリードの判断に任せる。無理に 5 件は埋めていない。実質的には「新ネタなし」に近い。
- **既出の更新: 2 件**
  - Skills over MCP（保留）: GitHub MCP Server v2.2.0（11/16）が MCP skills に対応していた。前回の「社内 MCP が skills を返すか未確認」に関わる。ただし opt-in で、Claude Code で動くかは未確認
  - runner-images: #15368（LLVM 22）が 11/26 に Closed。経緯が不自然なので点は動かさないこと
- **動きなし（前回から変化なし）**: Claude Code（最新 2.1.332 / 11/25 のまま）、Flutter（stable 3.50.1、beta 3.51.0-0.2.pre のまま。11/19 以降タグなし）、gh（v2.106.0 / 11/25 のまま）、claude-code-action（v1.0.290 / 11/25 のまま）、runner-images の Announcement（最新は #15397 / 11/23 のまま）、Gemini CLI、Firebase CLI、TypeScript、Next.js（canary のみ）、Dart（dev タグのみ）

---

## 新規候補

### 新規 1: Vite 8.4 の dev サーバーのファイル制限強化（`server.fs.strict: 'extended'`）と細かな改善

- **リリース日**: v8.4.0 / create-vite@9.4.0 とも **2026-11-26**（GitHub Releases。年の表記はないが、beta.2 が 11/25 で連続しており 2026 と判断）
- **概要**:
  - `server.fs.strict` に `'extended'` という値が追加された。docs によると、`true`（既定）と同じアクセス制限を **plugin が読み込むモジュールにも**かける（`\0` で始まる仮想モジュールは除く）。関連して `environment.addSafeModule` が追加された（#23974）。
  - 細かな改善としては、hook エラーに plugin 名が出る（#24107, #24151）、build manifest に worker chunk が入る（#23699）、CSS preload の失敗時に再試行する（#23710）、SSR の bundled dev mode（#21626）、WASM の source phase import（#22675）がある。
  - 移行が必要になり得るもの: `optimizeDeps.rollupOptions` の削除（#23760）、`--configLoader=runner` の非推奨化（#23714）、`css.devSourcemap` から `dev.sourcemap.css` への移行（#23765）。リリースノートに「破壊的変更」の節はない。
  - create-vite@9.4.0 では devtools オプションと Vite+（`vp`）パッケージマネージャーに対応した。
- **なぜ今話す価値があるか**: 前日に出たばかりの stable。「dev サーバーがどのファイルまで配信するか」は Web フロントの全員に関係し、設定 1 行で強化できる。
- **デモ案**: plugin 経由でワークスペース外のファイルを読み込む最小構成を作る。`strict: true` では配信され、`'extended'` では 403 になることを見せる。準備は 30 分以内に収まる見込み（**推測**。docs の記述から組み立てたもので、実際には動かしていない）。
- **評価上の注意（推測を含む）**:
  - Web 限定で、モバイル（Flutter 中心）には関係しない。実務への効きは 2〜3 程度の見込み。
  - リリースノートにも PR #23972 にも、どのような脆弱性への対策なのかは書かれていない。「セキュリティ修正」とは言わず、「制限を強化できるオプション」として紹介する。
  - 公式ブログ記事はリリースページからリンクされていなかった（vite.dev は今回試していない）。
  - 社内の Web が Vite を使っているかは未確認。
  - 却下済みの「Vite+ 安定版」とは別物。ただし create-vite の `vp` 対応で少し接点がある。
- **根拠 URL（いずれも取得・確認済み）**:
  - https://github.com/vitejs/vite/releases/tag/v8.4.0
  - https://github.com/vitejs/vite/releases （create-vite@9.4.0 の日付と内容）
  - https://github.com/vitejs/vite/pull/23972 （11/12 マージ。説明は "applies the checks to more requests" だけ）
  - https://raw.githubusercontent.com/vitejs/vite/main/docs/config/server-options.md （`'extended'` の挙動の定義）

---

## 既出の更新

### 更新 1: Skills over MCP【backlog: 14 点・保留】に GitHub MCP Server の skills 対応

- **事実（一次情報で確認済み）**:
  - github/github-mcp-server **v2.2.0（2026-11-16、"The one with Skills"）**: "feat(skills): replace server instructions with filtered MCP skills"（#3685、11/13 マージ）
  - PR #3685 の内容:
    - **opt-in** で、サーバー側は `--features=skills` または `--insiders` で有効になる。有効にすると server instructions の代わりに skills catalog を返す（`skills/list`・`skills/get` など）。無効なら従来どおり instructions を返し、skills は出ない。
    - 同梱の skills は 18 個（issue 作成、backlog の整理、PR 作成・レビュー、workflow 失敗の切り分け、セキュリティアラートの切り分け、リリース準備など）。
    - 使える tool に応じて絞り込まれる（toolset、read-only、除外設定などが反映される）。
    - 仕様については「`modelcontextprotocol/ext-skills` の beta Skills extension に aligned」とだけ書かれ、protocol revision の明記はない。
    - 動作確認は GitHub Copilot CLI（`MCP_SKILLS=true`）でのみ行われていて、**Claude Code については記載がない**。stdio と StreamableHTTP に対応し、旧 SSE は対象外。
  - v2.3.0（11/24）には skills 関連の記載はない（Dependabot / code scanning / secret scanning のアラート更新ツール、experimental の PR artifact ツールなど）。
- **評価への影響（提案）**:
  - 前回レポートの保留理由の 1 つ「社内で使う GitHub MCP が skills を返すか未確認」は、「**サーバー側は opt-in で返せる**。Claude Code で受け取れるかは未確認」に更新される。
  - デモ候補が、個人リポジトリの olaservo/skills-over-mcp-demo から GitHub 公式サーバーに替えられる可能性がある。ただし次の 3 点が未確認なので、点はまだ上げない（推測）。
    - Claude Code 2.1.332 の MCP skills 実装と、このサーバーの skills 応答が噛み合うか
    - docs が要求する protocol revision 2026-07-28 に対応しているか
    - Team 組織の Owner による有効化
  - 時期の注意: v2.2.0 は 11/16 なので、前回レポートの対象期間より前に出ていた（見落としていた分の補足）。
  - 仕様リポジトリ名に食い違いがある。PR は `modelcontextprotocol/ext-skills`、前回の根拠は `modelcontextprotocol/experimental-ext-skills`。改名なのか別物なのかは未確認。
- **URL（取得・確認済み）**:
  - https://github.com/github/github-mcp-server/releases
  - https://github.com/github/github-mcp-server/releases/tag/v2.2.0
  - https://github.com/github/github-mcp-server/pull/3685
  - https://github.com/github/github-mcp-server/releases/tag/v2.3.0

### 更新 2: GitHub Actions ランナーイメージ【backlog: 15 点・次点・条件付き】

- #15368（LLVM 22 への更新要望。VS 2027 イメージで clang-cl が壊れた件と紐づいていた）が **11/26 に "completed" で Closed** になった。同じ日にタイトルが "It outdated" に書き換えられている。11/25 以降のコメントはなく、PR #15370 がマージ・反映されたかはページから確認できなかった。
- 閉じた人がメンテナかどうかは確認していない。経緯が不自然なので、「直った」とは扱わないこと（推測）。点は据え置きを推奨。
- Announcement の新規はない（最新は #15397、11/23）。#15373（VS 2026 ラベルの非推奨化、12/1〜）が近づいている点だけは変わらない。
- **URL（取得・確認済み）**: https://github.com/actions/runner-images/issues/15368 / https://github.com/actions/runner-images/issues?q=is%3Aissue+label%3AAnnouncement+sort%3Acreated-desc

### 変化なし（確認のみ）

| 対象 | 確認結果 | URL（取得済み） |
|---|---|---|
| Claude Code | 最新は 2.1.332（11/25）。2.1.333 以降はまだない | https://code.claude.com/docs/en/changelog |
| Flutter | 最新タグは 3.50.1（11/19）。3.50.2 / 3.51 stable はない | https://github.com/flutter/flutter/tags |
| gh | 最新は v2.106.0（11/25） | https://github.com/cli/cli/releases |
| claude-code-action | 最新は v1.0.290（11/25、`GITHUB_OUTPUT` の復元のみ） | https://github.com/anthropics/claude-code-action/releases |

---

## 調べたが候補にしなかったもの

- **Claude アプリのリリースノート（11/20・11/24）**: Team / Enterprise 向けの Computer use（beta、11/20）、Monthly recap の正式版、artifact をアプリとしてインストール（beta）、connected apps へのファイル送信（beta、Team は Owner の有効化が必要）。いずれも前回レポートの対象期間より前で、Claude Code ではなくアプリの機能。デモの再現性も Owner の設定次第なので候補にしなかった。リードが「社内 Claude Team 活用」枠を検討するなら材料にはなる（提案）。出典: https://support.claude.com/en/articles/12138966-release-notes.md （取得済み）
- **Dart**: 3.15.0-185.0.dev（11/26）まで dev のみ。dartpad-v0.2.0（11/25）は DartPad パッケージで、SDK のリリースではない。https://github.com/dart-lang/sdk/tags
- **Next.js**: v16.5.0-canary.69（11/26）まで canary のみ。https://github.com/vercel/next.js/releases
- **TypeScript**: 11/6 の vscode-typescript v1.0.2 以降、リリースはない。https://github.com/microsoft/TypeScript/releases
- **Gemini CLI**: v0.71.1 / v0.72.0-preview.0（11/24）以降、リリースはない。https://github.com/google-gemini/gemini-cli/releases
- **Firebase CLI**: v15.37.0（11/19）以降、リリースはない。https://github.com/firebase/firebase-tools/releases
- **Web 検索（Flutter / Firebase / GCP、Anthropic）**: 11/26 付の一次情報は見つからなかった。ヒットしたのは Cloud Next 2026 の Dart for Firebase Functions（preview）の再掲で、まとめサイトばかりだった（期間外で、一次情報も未確認）。

---

## 取得できなかった URL

- なし（今回試した URL はすべて取得できた）
- 試していないもの: github.blog / nulab.com / modelcontextprotocol.io（前回までに ENOTFOUND）、vite.dev のブログ、firebase.google.com、docs.flutter.dev
- その他: https://code.claude.com/docs/en/changelog は 144 万字あり、先頭の 10 万字（最新版の範囲）だけを確認した。

**運用上の注意**: 今回も PostToolUse フックのエラー（`/root/.claude/hooks/wf-collector.py` が見つからない、`base64: invalid input`）が毎回出た。調査には影響なし。
