# trend-scout 調査メモ（2026-11-25 JST 実行）

- 対象期間: 前回レポート（reports/2026-11-24.md）以降。実質 2026-11-24〜25 の 1 日分。ただし、これまでネットワーク制約で一次情報を読めず、backlog に一度も載っていない話題は「新規（backlog 未登録）」として扱った（リリース日は各候補に明記）
- 重複チェックの対象: CLAUDE.md の過去テーマ 3 件、backlog.md の候補 5 件・却下 4 件
- 今回の方針: Claude Code 以外（Flutter / Dart、Firebase、GitHub、Workspace、Backlog、インフラ）を優先。docs.flutter.dev が取れないため、flutter/website リポジトリの raw ソース（`sites/docs/src/content/...`）を読んだ。これで Flutter の一次情報が初めて読めた

## 結論

- 2026-11-24 以降に出た新しいリリースで勉強会 1 枠になるものは**なし**。
- ただし、Flutter の一次情報が初めて読めたので、**backlog 未登録の新規候補を 3 件**出す（新規 1 が本命。新規 2・3 は新しさが弱い）。
- **既出候補の更新が 2 件**ある（CocoaPods trunk、`gh event listen`）。

---

## 新規候補

### 新規 1: Flutter 3.50 で非推奨になった Material / Cupertino を `dart fix` 一発で `material_ui` / `cupertino_ui` に移行する

- **リリース日**: Flutter 3.50 は 2026-11-11（docs の What's new に「11 November 2026: Release 3.50」とある）。hotfix 3.50.1 も出ているが、CHANGELOG に日付がないので、リリース日は不明
- **概要**: Flutter 3.50 で、SDK に同梱されている Material / Cupertino ライブラリが正式に非推奨になった（"The in-framework design libraries are formally deprecated in Flutter 3.50."）。今後は pub.dev の独立パッケージ `material_ui` / `cupertino_ui` を使う。経緯は、3.44 で SDK 内のコードを凍結、3.47 で独立パッケージ 1.0 を公開（opt-in）、3.50 で非推奨。`dart fix --apply --code=migrate_design_widgets` で import を自動で書き換えられる。依存パッケージが未移行のときの一時的な橋渡しとして `MaterialUiCompatibilityBridge` がある。
- **落とし穴（一次情報に記載あり）**:
  - 3.50 の `flutter gen-l10n` は、まだ非推奨の delegates を生成する。回避策は `l10n.yaml` に `header: '// ignore_for_file: deprecated_member_use'` を書くこと。
  - `flutter_localizations` の import が残ると `ambiguous_import` エラーになる。
  - `CupertinoApp` の中で Material ウィジェットを使っている場合、Material の delegates を入れないと "No MaterialLocalizations found" になる。
  - パッケージ作者が移行すると、利用者にとって破壊的変更になる（"Treat this migration as a major version bump"）。
- **なぜ今話す価値があるか**: 社内のモバイルは Flutter が中心で、全アプリが通る移行。非推奨の警告が出始めたばかりの今がちょうどよい。
- **15 分構成案**: (1) 3.44→3.47→3.50 の経緯と、まだ削除時期は決まっていないこと（3 分） (2) デモ: サンプルアプリで `dart fix` → `flutter pub add material_ui` → ビルド（7 分） (3) gen-l10n・localizations・依存パッケージの落とし穴（5 分）
- **想定デモ**: `flutter create` で作ったアプリ（l10n 入り）を 3.50 にして、`dart fix --apply --code=migrate_design_widgets` を実行し、差分とビルドが通ることを見せる。gen-l10n の警告と、その回避も見せる。準備は 30 分以内でできる見込み（推測。発表者の環境に 3.50 が必要）
- **一次情報 URL**:
  - https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/release/breaking-changes/material-ui-and-cupertino-ui.md （**取得・確認済み**。移行ガイドのソース）
  - https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/release/breaking-changes/index.md （**取得・確認済み**。「Released in Flutter 3.50」の破壊的変更 7 件）
  - https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/release/whats-new.md （**取得・確認済み**。11/11 Release 3.50）
  - https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/release/release-notes/release-notes-3.50.0.md （**取得・確認済み**。前半 10 万字のみ）
  - https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md （**取得・確認済み**。3.50.1 の hotfix 内容）
  - 公開ページ（未取得。ENOTFOUND が続くため試していない）: https://docs.flutter.dev/release/breaking-changes/material-ui-and-cupertino-ui
- **推測・未確認**:
  - 同梱ライブラリを削除するバージョンは、一次情報に書かれていない。
  - リリースノートの PR 一覧の要約に「AGP 9.x、最低 9.1.1」「Android 17（API 37）対応」とあった。ただし PR タイトルの要約なので、Android 側の必須要件かどうかは未確認。破壊的変更の一覧には AGP の項目はない。
  - 既出の CocoaPods 候補（iOS ビルド）とは別の話題。ただ、同じ Flutter 3.50 の話なので、リードの判断で「Flutter 3.50 移行で踏むもの」として 1 枠にまとめる手もある（提案）。
- **重複チェック**: 過去テーマ「Claude を使った Flutter モバイル開発の実践」は AI 活用の話で、こちらは SDK の移行の話なので重ならない。backlog にもない。

### 新規 2: Dart の package skills: `dart run skills@ get` で依存パッケージの AI 用スキルをまとめて入れる（新しさは弱い）

- **リリース日**: docs の追加は 2026-08-31（site-www のコミット履歴）。skills CLI の 1.0.1 が 9/4、最新の 1.0.4 が 10/16（github.com/dart-lang/ai の Releases）。1.0.0 の日付は確認できず。**対象期間からは外れる**。11/11 の Flutter 3.50 の docs 更新で「新しい package skills ガイド」として AI ドキュメントが整理し直されたので、Flutter 側で目に入るのは今月から
- **概要**: パッケージ作者が `skills/` ディレクトリに Agent Skills 仕様（SKILL.md）のスキルを同梱し、利用者は `dart run skills@ get` で `pubspec.yaml` の直接依存からスキルを集めて `.agents/skills/` などに入れる。`list` / `prune` / `remove` / `add <git_url>` / `create` がある。CHANGELOG によると Claude Code、Cursor、GitHub Copilot、Codex、Antigravity などの各エージェント向けのディレクトリに対応している。
- **なぜ今話す価値があるか**: AI 活用の具体例で、Claude と Gemini のどちらでも使える。「どのエージェントでもパッケージの正しい API を使わせる」という、ツール比較ではない切り口が取れる。
- **想定デモ**: 社内の Flutter プロジェクトで `dart run skills@ get --all` を実行し、入ったスキルを `list` で確認する。スキルがある場合とない場合で、エージェントの出力の違いを見せる（出力は毎回変わるので、リハーサルが必要）
- **一次情報 URL**:
  - https://raw.githubusercontent.com/dart-lang/site-www/main/src/content/ai/package-skills.md （**取得・確認済み**）
  - https://raw.githubusercontent.com/dart-lang/ai/main/pkgs/skills/CHANGELOG.md （**取得・確認済み**。日付の記載なし）
  - https://github.com/dart-lang/ai/releases （**取得・確認済み**。skills-v1.0.1〜v1.0.4 の日付。年の表記はないが 2026 年と判断した）
  - https://github.com/dart-lang/site-www/commits/main/src/content/ai/package-skills.md （**取得・確認済み**。初回コミットは 2026-08-31）
  - 未取得（検索結果のみ）: https://dart.dev/blog/skills-cli-1-0-bundle-and-distribute-ai-agent-skills-for-your-packages 、https://pub.dev/packages/skills
- **推測・未確認**:
  - 社内で使っている主要パッケージ（Firebase など）が、実際に `skills/` を同梱しているかは未確認。同梱が少なければ、デモの見栄えが落ちる。
  - 新しさは 2〜3 点と見る（推測）。
- **重複チェック**: 却下済みの「Gemini の Gem → skills 移行」は Gemini アプリ（Workspace）の機能で、別物。ただし「skills」という言葉が同じなので、リードは区別して扱うこと。過去テーマ「Claude を使った Flutter モバイル開発」とは、Claude に特化しない点で切り分けられる。ただし Claude Code でデモすると見た目が重なるので、Gemini CLI か Antigravity でのデモを推奨する（提案）。

### 新規 3: Firebase CLI v15.37.0: Cloud Functions の Node.js 26 ランタイム（Preview）と、Crashlytics MCP でクラッシュを AI に調べさせる（新しさは弱い）

- **リリース日**: firebase-tools v15.37.0 は 11/19。v15.36.0 は 11/11
- **概要**:
  - v15.37.0 で、Cloud Functions に Node.js 26 ランタイムのサポートが追加された（Preview）。Node.js 26 は 2026-10-28 に LTS（'Lithium'）になっている。
  - 同じリリースで、`@modelcontextprotocol/sdk` などの critical / high のセキュリティ修正、`firebase deploy --dry-run` が Firestore DB を作ってしまう不具合の修正、Crashlytics MCP の `get_report` への `filterExpression` 追加があった。v15.36.0 では `list_events` にも `filterExpression` が追加されている。
  - Crashlytics の MCP ツール自体は以前からある（v15.27.0、8/13 に Web アプリにも対応）。
- **なぜ今話す価値があるか**: Flutter + Firebase の構成なら、モバイルとインフラの両方に効く。セキュリティ修正が入っているので、CLI の更新を促す意味もある。
- **想定デモ**: `npx -y firebase-tools@latest mcp` を MCP サーバーとして登録し、Crashlytics の上位クラッシュを filterExpression で絞り込んで、AI に原因の候補を挙げさせる。実データのある Firebase プロジェクトが必要
- **一次情報 URL**:
  - https://github.com/firebase/firebase-tools/releases （**取得・確認済み**。v15.37.0 / v15.36.0。年の表記はないが 2026 年と判断した）
  - https://github.com/firebase/firebase-tools/releases?page=2 （**取得・確認済み**。v15.27.0 の Crashlytics MCP の Web 対応）
  - https://github.com/nodejs/node/releases （**取得・確認済み**。Node.js 26 の LTS 移行は 10/28）
  - https://firebase.google.com/docs/crashlytics/ai-assistance-mcp （**取得失敗: ENOTFOUND**。セットアップ手順は検索結果の要約のみ）
- **推測・未確認**:
  - 中核の「Crashlytics MCP の使い方」は二次情報（検索要約）だけ。根拠ルールの減点対象になりうる。
  - 社内アプリが Crashlytics を使っているかは未確認。
  - 1 リリースとしては中身が薄く、単独枠にするなら「Crashlytics × MCP」に寄せる必要がある。
- **重複チェック**: backlog・過去テーマとの重複はない。

---

## 既出候補の更新

### 更新 1: CocoaPods trunk read-only（12/2）と Flutter の SwiftPM【backlog: 14 点・TOP3 条件付き】

- **確定したこと（一次情報）**: Flutter docs のソースに "As of the 3.44 release, Flutter's Swift Package Manager (SwiftPM) support is on by default." とある。backlog で「推測」とされていた「3.44 から既定」は、**一次情報で確認できた**。
- 同じページに "the CocoaPods registry permanently becomes read-only on December 2, 2026" とある。12/2 を Flutter 公式も明記している。
- SwiftPM に対応していない依存については、Flutter が CocoaPods にフォールバックする（"Flutter falls back to CocoaPods for dependencies that don't yet support Swift Package Manager."）。将来は SwiftPM を無効にできなくなる、とも書かれている。
- **デモへの影響**: フォールバックしているプラグインを一覧にするコマンドは、docs に書かれていない。確認方法として書かれているのは、Xcode で `FlutterGeneratedPluginSwiftPackage` が target dependency になっているかを見ることだけ。デモ「フォールバックしているプラグインの洗い出し」は、Podfile.lock などを手で確認する形になる（推測）。
- **評価への影響（提案）**: 推測が 1 つ減る。15 分で話せるか / デモ映えは据え置き。新規 1（material_ui 移行）とまとめて「Flutter 3.50 移行で踏むもの」の 1 枠にする選択肢もある。
- **URL**: https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/packages-and-plugins/swift-package-manager/for-app-developers.md （**取得・確認済み**。前回 404 だった `src/content/...` のパスは誤りで、正しくは `sites/docs/src/content/...`）

### 更新 2: `gh event listen`【backlog: 14 点・保留】

- GitHub Releases の表示では、v2.105.0 のリリース日は **11/18**（backlog では告知 11/17。タイムゾーンの差か、告知とリリースの日付が違う可能性がある）。v2.105.0 の後に新しいリリースはない。
- 追加で確認できたこと: `--count` / `--follow`、出力は CloudEvent JSON、`gh pr` / `gh run watch` などが `gh event listen` を案内するようになった。ウェイトリスト制の状況は、新しい情報なし。
- URL: https://github.com/cli/cli/releases （**取得・確認済み**）

### 参考: Claude Code・Gemini CLI（変更なし）

- Claude Code の最新は 2.1.331（11/23）のまま。それより新しいエントリはない（https://code.claude.com/docs/en/changelog 、取得・確認済み）。
- Gemini CLI は v0.71.0 / v0.71.1（11/24、A2A サーバーの認証修正）、v0.72.0-preview.0（11/24）が出た（https://github.com/google-gemini/gemini-cli/releases 、取得・確認済み）。小さな修正だけなので、却下判断は変わらない。

---

## 調べたが候補にしなかったもの

- **Node.js**: 11/11 に 26.13.1 / 24.22.3 / 22.23.5 のセキュリティリリース。27.0.0 は alpha のみ。単独ネタとしては弱い（新規 3 の背景として使った）。
- **Terraform v1.17.2（11/11）**: 小さな修正のみ。
- **Next.js**: 11/16〜23 は v16.5.0-canary だけで、stable のリリースはない。
- **TypeScript**: 10/25 以降は vscode-typescript v1.0.2（11/6、TS 7.0.2 を同梱）だけ。TS 7.0 本体のリリース日は確認できなかった。
- **GitHub Actions runner**: 最新は v2.338.2（10/26、内部 Node を 24 に移行）で、対象期間外。macOS 14 ランナーの廃止（11/2）は検索要約のみで、github.blog は未取得。
- **Dart 3.14**: main の CHANGELOG では 3.14.0 は "Unreleased" と表示されていた（main ブランチのため）。前回のとおり、stable 3.14.1 は 11/17。`dart migrate`、`dart run -e` などの新機能は、新規 1 の補足になる程度。
- **Google Workspace / Gemini**: 検索では、Gemini skills の第 2 波（11/30〜）と Gmail の Gemini 対象拡大（11/10）だけ。skills は却下済み。どちらも一次情報の本文は取れていない。
- **Backlog**: 11 月のリリース情報は見つからなかった。2027-01 のプラン刷新（@IT、7/24）は既知。backlog.com も ENOTFOUND。
- **GCP**: cloud.google.com/release-notes は docs.cloud.google.com へリダイレクトされ、そのホストが ENOTFOUND。

---

## 取得できなかった URL

| URL | エラー |
|---|---|
| https://firebase.google.com/docs/crashlytics/ai-assistance-mcp.md.txt | `getaddrinfo ENOTFOUND firebase.google.com` |
| https://docs.cloud.google.com/release-notes | `getaddrinfo ENOTFOUND docs.cloud.google.com`（cloud.google.com/release-notes からの 301 リダイレクト先） |
| https://backlog.com/ja/blog/ | `getaddrinfo ENOTFOUND backlog.com` |
| https://github.com/flutter/website/tree/main/src/content/release/release-notes | HTTP 404（パスの誤り。正しくは `sites/docs/src/content/...`） |

ENOTFOUND が続いたため、ツールの指示に従い、ほかの外部ホストの取得は試していない。dart.dev / docs.flutter.dev / pub.dev / github.blog / workspaceupdates.googleblog.com / nulab.com / workspace.google.com は、検索結果に出ただけで未取得。

取得できたホスト: code.claude.com / github.com / raw.githubusercontent.com（cloud.google.com はリダイレクトの応答だけ返った）

**運用上の提案**:
- Flutter / Dart の docs は、`raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/<docs のパス>.md` と `raw.githubusercontent.com/dart-lang/site-www/main/src/content/<パス>.md` で読める。次回からこの方法を使えば、Flutter 側の偏りは解消できる。
- 新たに許可が必要なホスト: firebase.google.com / docs.cloud.google.com / backlog.com（これまでに挙がったホストに追加）。
- 毎回、PostToolUse フックのエラー（`/root/.claude/hooks/wf-collector.py` が見つからない、`base64: invalid input`）が出ていた。調査自体には影響なし。
