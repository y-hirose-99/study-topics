# practitioner（実務適用）調査メモ

- 調査日: 2026-11-25（JST）
- 対象期間: 前回レポート（reports/2026-11-24.md）以降。前回から 1 日しか経っていないため、前回までの調査で拾えていなかった 11 月中旬以降の Claude Code 以外のネタ（Flutter / GitHub Actions / Backlog）を重点的に探した
- 観点: 「明日から業務で試せるか」。新しさより持ち帰りやすさを優先
- 重複チェック: CLAUDE.md の過去テーマ 3 件と、backlog.md の候補（/code-review + /loop、auto mode・無人 deny、CocoaPods trunk read-only、VS Code 拡張 Fix/Resolve、gh event listen）と却下（Vite+/Vinext、/usage・idle compaction、Gemini CLI→Antigravity CLI、Gem→skills）を照合した
- 結論: **新規候補は 3 件**（すべて Claude Code 以外）。5 件には満たないので、無理に埋めていない。Claude Code は 2.1.331（11/23）より新しい版が changelog になく、Claude Code の新規ネタはなし

## 注意（ネットワーク制約）

- 一次情報を本文まで確認できたのは、今回も code.claude.com / github.com / raw.githubusercontent.com だけ。
- codezine.jp が ENOTFOUND だったので、ツールの指示に従い、それ以外の新しいホストは取得を試していない（末尾の一覧を参照）。

---

## 候補 1: Flutter 3.50.1 hotfix — 3.50.0 で止まっているなら今すぐ上げるべき理由（`.env` が APK から消える、split APK の versionCode が重複する）

- **タイトル案**: 「3.50.0 のまま出荷していませんか？」Flutter 3.50.1 hotfix の実害チェック
- **日付**: 3.50.0 = 2026-11-09、3.50.1 = 2026-11-19（GitHub の tags ページで確認）
- **概要**: Flutter 3.50.1 は 11 件の不具合を直す stable の hotfix。業務アプリへの影響が大きいのは次の 4 件。
  - 3.50.0 の Android ビルドで、名前がドットで始まるアセット（`.env` など）やアンダースコアで始まるフォルダが APK / AAB に入らず、実行時に失敗する（flutter/196444）
  - `--split-per-abi` で、`versionCode` を `AndroidManifest.xml` にだけ書いていると、すべての split APK が同じ versionCode になる（flutter/196559）
  - プラグインがあり、独自の `buildType` を持つ Android ビルドが Gradle の設定段階で失敗する（flutter/196412）
  - Gradle の configuration cache が保存されず、毎回再利用されない（flutter/195006）
  - Web: `--wasm` + cross-origin isolation で動的テキストの描画が固まる、大きな Web アプリで `flutter run -d chrome` がタイムアウトする、など
- **参加者が持ち帰れるもの**: 「hotfix の CHANGELOG を読んで、自分のアプリに当たるか 5 分で判定する」手順と、APK の中身・versionCode を確認するコマンド
- **15 分構成案**:
  1. （3 分）Flutter の hotfix の方針（最新の stable だけに出る）と、3.50.1 の 11 件のうち業務アプリに効く 4 件
  2. （8 分）デモ: `.env` をアセットに含むサンプルアプリを 3.50.0 と 3.50.1 で `flutter build apk` → `unzip -l` でアセットの有無を比べる。続けて `--split-per-abi` の versionCode を `aapt2 dump badging`（または `apkanalyzer`）で比べる
  3. （4 分）社内アプリの確認チェックリスト（`.env` / `_` 始まりのアセット、split APK の運用、独自 buildType）と、CI の Flutter バージョン固定の見直し
- **30 分以内で準備できるデモ案**: `flutter create` したアプリに `.env` をアセットとして追加し、fvm などで 3.50.0 と 3.50.1 を切り替えて 2 回ビルドする。Android SDK があれば Mac 不要
- **想定対象者**: モバイル（Flutter）。Web 版 Flutter を持っている人にも一部効く
- **根拠 URL（取得・確認済み）**:
  - https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md （3.50.1 の項。11 件の issue 番号と症状を確認）
  - https://github.com/flutter/flutter/tags （3.50.1 = 2026-11-19、3.50.0 = 2026-11-09、3.51.0-0.2.pre = 2026-11-18）
- **重複チェック**: 過去テーマ「Claude を使った Flutter モバイル開発の実践」とは別（AI の話ではない）。backlog の CocoaPods 候補とも別（iOS ではなく Android / Web の話）
- **推測部分**:
  - 社内アプリが 3.50.0 に上がっているかは未確認。上がっていなければ「上げる前に 3.50.1 を選ぶ」という話になり、緊急性は下がる
  - `.env` をアセットで同梱する運用（flutter_dotenv など）が社内にあるかは未確認。あれば実務への効きは大きい
  - 3.50.0 自体の目玉（docs.flutter.dev のリリースノート）は ENOTFOUND で未取得。3.50.0 の新機能まで話すなら別途確認が必要

---

## 候補 2: GitHub Actions のランナーが 11 月に一斉に変わった — `-latest` ラベル依存の棚卸し（ubuntu-latest → 26.04、Xcode 27 GA、Windows の ReFS 化）

- **タイトル案**: 「CI が急に落ちた」の正体 — GitHub Actions runner-images の 11 月の変更点と `-latest` の固定戦略
- **日付**: ubuntu-latest → 26.04 は 2026-10-19 から段階移行、11-19 完了予定（#14748）／ Xcode 27 GA 告知 2026-11-23（#15397）／ Windows の D: が ReFS に変わるのは 2026-11-30（#15315）／ Go 1.24 の tool cache 削除 2026-11-18（#15253）
- **概要**: actions/runner-images の告知 issue によると、11 月にランナーの中身が大きく変わった。
  - `ubuntu-latest` が Ubuntu 26.04 になった。カーネル 6.17 → 7.0、systemd 255 → 259、既定の Java が 17 → 25 に上がる。issue のコメントでは Docker 29 の containerd image store、bubblewrap の AppArmor 拒否、pip の TLS 失敗、wkhtmltopdf の apt パッケージがないこと、などの報告がある（メンテナーは未確認）
  - Xcode 27 の専用イメージ（`runs-on: xcode-27`）が GA になった。macOS 26 / Xcode 27 では既定の OpenSSL が 3 から 4 に変わる予定（#14856）
  - 11/30 から Windows イメージの D: ドライブが NTFS から ReFS に変わる
- **参加者が持ち帰れるもの**: 自分のリポジトリの `runs-on: *-latest` を洗い出し、「固定する / 追従する」を決めるチェックリストと、ジョブログの「Runner Image」欄で今どのイメージで走ったか確認する方法
- **15 分構成案**:
  1. （4 分）11 月に何が変わったか（Ubuntu 26.04、Xcode 27 GA、Windows ReFS、Go 1.24 削除）。「-latest は予告つきで動く」ことを先に言う
  2. （7 分）デモ: `gh search code` または `grep -r "runs-on" .github/workflows` で `-latest` を洗い出す → 同じジョブを `matrix: os: [ubuntu-24.04, ubuntu-26.04]` で走らせて Java のバージョン差などを見せる → ジョブログの Set up job にある Runner Image を確認
  3. （4 分）固定するか追従するかの判断基準と、runner-images の Announcement ラベルの issue を Watch しておく運用
- **30 分以内で準備できるデモ案**: テスト用リポジトリに `java -version` / `lsb_release -a` / `docker info` を表示するだけのワークフローを置き、24.04 と 26.04 の matrix で実行する。数分で終わる
- **想定対象者**: GitHub Actions を使う全員（Web / インフラ）。Flutter の iOS ビルドを Actions で回しているモバイルの人には Xcode 27 の部分が効く
- **根拠 URL（取得・確認済み）**:
  - https://github.com/actions/runner-images/issues/14748 （ubuntu-latest → 26.04）
  - https://github.com/actions/runner-images/issues/15397 （Xcode 27 GA、2026-11-23）
  - https://github.com/actions/runner-images/releases （11/22〜24 のイメージ。xcode-27-arm64 に GitHub CLI 2.105.0 が入っている、など）
  - https://github.com/actions/runner-images/issues?q=is%3Aissue+label%3AAnnouncement+is%3Aopen （告知 issue 一覧。#15315 ReFS、#15253 Go 1.24、#14856 OpenSSL 4 など）
- **重複チェック**: backlog の CocoaPods 候補（iOS の依存管理）と、Xcode 27 の部分が少し重なる。重ねる場合は Xcode 27 を CocoaPods 候補の補足に回し、こちらは Ubuntu / Windows 中心にする
- **推測部分**:
  - 社内でどのくらい GitHub Actions を使っているか（Backlog Git や他の CI がメインの可能性）は未確認
  - issue のコメントの不具合報告（Docker、bwrap、pip/SSL）はユーザー報告で、メンテナーは確認していない。発表では「報告がある」にとどめる
  - #15397 から GitHub Changelog（2026-10-15）の記事にリンクがあるが、github.blog は ENOTFOUND のため未取得
  - 移行は 11/19 完了予定とされるが、実際に全ジョブで切り替わったかは未確認（コメントには 10/19 より前に 26.04 になっていたという報告もある）

---

## 候補 3: Backlog MCP Server を Claude に繋いで「課題を読む・起票する」を会話でやる（v0.23.x、権限を絞る設定つき）

- **タイトル案**: Backlog の課題、Claude に読ませていますか？ Backlog MCP Server の最小構成と toolset の絞り方
- **日付**: v0.23.4 = 2026-11-18（依存更新）、v0.23.3 = 2026-10-27（セキュリティ修正 CVE-2026-111768）、v0.22.0 / v0.21.0 = 2026-10-13（破壊的変更: `get_document` がプレーンテキストのみに、ツールの応答を圧縮）
- **概要**: ヌーラボが公開している Backlog MCP Server を使うと、Claude（Claude Code / Claude Desktop）から Backlog のプロジェクト・課題・Wiki・Git・プルリクエスト・通知・ドキュメントを扱える。`npx backlog-mcp-server` か Docker（`ghcr.io/nulab/backlog-mcp-server`）で起動し、`BACKLOG_DOMAIN` と `BACKLOG_API_KEY` を渡すだけで動く。`--enable-toolsets`（例: `project,issue`）で使うツールを絞れる。Streamable HTTP や OAuth 2.0 にも対応している。日本語ガイド（README.ja.md）がある。
- **参加者が持ち帰れるもの**: Claude Code に Backlog MCP を追加する設定 1 つと、「課題の要約 → 子課題の起票」を頼むプロンプト例
- **15 分構成案**:
  1. （3 分）何ができるか（toolset 一覧）と、API キーの扱い。古い版を使っている人は v0.23.3 以上に上げる（CVE 修正）
  2. （8 分）デモ: Claude Code に `project,issue` だけを有効にして追加 → 「このマイルストーンの未完了課題を担当者別にまとめて」→ 「この課題を 3 つの子課題に分けて起票して」（起票前に許可確認が出ることを見せる）
  3. （4 分）運用の注意: toolset を絞る、HTTP モードを外に公開しない（README の警告）、v0.21.0 / v0.22.0 の破壊的変更
- **30 分以内で準備できるデモ案**: テスト用の Backlog プロジェクトを 1 つ作り、ダミー課題を 5 件ほど入れる。個人 API キーを発行し、`npx backlog-mcp-server` を Claude Code に MCP サーバーとして登録する
- **想定対象者**: Backlog を使う全員（エンジニア以外にも効く）。Claude Team の利用者
- **根拠 URL（取得・確認済み）**:
  - https://github.com/nulab/backlog-mcp-server （README: 起動方法、環境変数、toolset、HTTP / OAuth）
  - https://github.com/nulab/backlog-mcp-server/releases （v0.21.0〜v0.23.4 の日付と内容）
- **重複チェック**: backlog・過去テーマに Backlog × AI の候補はない。過去テーマ「Copilot から Claude への乗り換え比較」とも別
- **推測部分・条件**:
  - **新しさは低い**。このサーバー自体は 2025 年から公開されていたと記憶している（推測、今回は未確認）。直近の変更は破壊的変更・セキュリティ修正・依存更新で、目玉の新機能ではない。採点では新しさ 2〜3 点が妥当と考える
  - README は Claude Desktop などの汎用の `mcpServers` JSON 例が中心で、`claude mcp add` の具体的なコマンドは記載されていない（Claude Code 側のドキュメントで補う）
  - README に読み取り専用モードの記載はない。書き込みを避けたい場合は toolset の絞り込みと Claude 側の許可設定で制御する
  - 社内で Backlog の API キー発行や MCP の利用が許可されているかは未確認。「無保証・公式サポートなし」と README に書かれている点も伝える
  - Backlog 本体の AI アシスタント（2026-03 搭載とされる、検索要約のみ）との使い分けは未調査

---

## 既出候補の更新情報

| 既出候補 | 新情報 | 根拠 | 評価への影響（推測） |
|---|---|---|---|
| `/code-review` + `/loop`（TOP1） | changelog に 2.1.331（11/23）より新しい版はない | https://code.claude.com/docs/en/changelog （取得済み） | 変化なし |
| VS Code 拡張 Fix / Resolve（TOP2） | 2.1.331 にはほかに、ノートブックのセルを出力・エラーごと渡す「Add Cell to Chat」と、大きな選択範囲を送る前の警告もある。デモの補足に使える | 同上 | 変化なし（補足 1 行） |
| auto mode・無人 deny（次点） | 新情報なし | 同上 | — |
| CocoaPods trunk read-only（TOP3） | 公式ブログのリポジトリの最新コミットは 11/2 の「Update dates」で、それ以降の更新はない（12/2 の予定は変わっていない）。Flutter の stable CHANGELOG では、3.47.2 に「Xcode 27 で SwiftPM を使って Flutter を組み込んだ既存のネイティブ iOS アプリのビルドが失敗することがある」修正がある。Xcode 27 が Actions で GA になった（候補 2）ので、iOS の CI の話と繋げられる。CHANGELOG の 3.44〜3.50 に CocoaPods への言及はない | https://github.com/CocoaPods/blog.cocoapods.org/commits/master （取得済み）、https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md （取得済み） | 変化なし。「SwiftPM の既定化は 3.44 から」は今回も一次情報で確認できていない |
| `gh event listen`（保留） | GitHub CLI の最新は今回も v2.105.0（11/18）で、新しい版はない。リリースページは今回も「GitHub Event Streams is in preview」と、ウェイトリスト（gh.io/event-streams-signup）へのリンクだけで、一般公開の情報はない。補足として、runner-images の xcode-27-arm64 イメージには GitHub CLI 2.105.0 が入っている | https://github.com/cli/cli/releases （取得済み）、https://github.com/actions/runner-images/releases （取得済み） | 保留のまま。ウェイトリストの通過状況は人間に確認が必要 |
| Backlog 横断ガントチャート（未確認の手がかり） | 今回もリリースを確認できなかった。検索結果に 11 月のリリース情報はなく、nulab.com は過去 ENOTFOUND のため試していない | 検索結果の要約のみ | 候補化しない |
| Gemini CLI（却下済み） | 参考: v0.71.0 / v0.71.1（11/24）が出ている。中身は PowerShell の非同期パースと A2A サーバーの認証修正で、却下判断を変えるほどではない | https://github.com/google-gemini/gemini-cli/releases （取得済み） | 却下のまま |

## 取得できなかった URL

| URL | エラー |
|---|---|
| https://codezine.jp/news/detail/28470 | getaddrinfo ENOTFOUND codezine.jp（ネットワーク許可外と推測） |

- codezine.jp が ENOTFOUND になった後は、ツールの指示に従って新しいホストを試していない。前回までに ENOTFOUND だった docs.flutter.dev / *.googleblog.com / github.blog / nulab.com も今回は試していない。
- https://github.com/flutter/flutter/releases は取得できたが、2024-01 の 3.19 beta までしか載っていない古いページだった（Flutter は GitHub Releases を更新していないと推測）。代わりに tags ページと stable の CHANGELOG を使った。
- 未取得の参考 URL（検索結果に出たもの）: https://docs.flutter.dev/release/release-notes/release-notes-3.50.0 、 https://atmarkit.itmedia.co.jp/ait/articles/2607/24/news048.html

## 未確認の手がかり（候補化していない）

- Flutter 3.51 beta（3.51.0-0.2.pre、11/18）: 次の stable の先取りとして使える可能性があるが、中身は未確認
- runner-images #15373（11/20）: Visual Studio 2027 が使えるようになり、12 月から Visual Studio 2026 のラベルが非推奨になり始める。社内に Windows のビルドがあれば関係する
- Backlog の料金プラン: 2027-01-01 から新プラン（エコノミー / ビジネス / プロフェッショナル）。現行プランの新規契約は 2026-12-31 まで（@IT 2026-07-24、二次情報・未取得）
