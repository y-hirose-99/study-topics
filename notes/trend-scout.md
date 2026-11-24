# trend-scout 調査メモ（2026-11-24 JST 実行）

- 対象期間: 前回レポート（reports/2026-11-23.md、対象 2026-10-20〜2026-11-23）以降。実質は 2026-11-23〜24 の 1 日分
- 重複チェックの対象: CLAUDE.md の過去テーマ 3 件と、backlog.md の既出候補（/code-review、auto mode、CocoaPods trunk、Gemini CLI → Antigravity CLI、Vite+/Vinext、/usage）

## 結論: 目立った新ネタなし（新規候補 0 件）

前回から 1 日しか経っていない。Flutter / Dart、Web フロントエンド、GCP、AI 開発ツールを調べたが、新しく勉強会の 1 枠になる話題は見つからなかった。
その代わり、既出候補の評価が変わる新情報が 2 件ある（下記）。

---

## 既出候補の更新情報

### 更新 1: CocoaPods trunk read-only（12/2）を一次情報で確認できた【backlog: 13 点・TOP3 条件付き】

- **確認できたこと**: CocoaPods 公式ブログのソース（GitHub の CocoaPods/blog.cocoapods.org リポジトリ）にある記事「CocoaPods Trunk Read-only Plan」（著者 orta、2024-11-30 付）の本文を読んだ。
  - 引用: "I will switch trunk to not accept new Podspecs permanently."（2026-12-02。「アメリカの感謝祭明けの水曜日」と書かれている）
  - 試験運用は 2026-11-02〜06 に「新しい Podspec を受け付けない」状態で行う予定、と書かれている。前回の検索要約では「11/1〜7」だったが、一次情報では 11/2〜6。
  - 引用: "This will keep all existing builds working" / "Infrastructure like the Specs repo and the CDN would still operate as long as GitHub and jsDelivr continue to exist"
- **URL の訂正**: 前回の根拠 URL `https://blog.cocoapods.org/CocoaPods-Trunk/` は誤りの可能性が高い。正しい公開 URL は `https://blog.cocoapods.org/CocoaPods-Specs-Repo/` と思われる（検索結果に出た URL と、ソースのファイル名 `2024-11-30-CocoaPods-Specs-Repo.markdown` が一致するため。公開ページ自体は DNS エラーで未取得）。
- **まだ確認できていないこと**:
  - 計画どおり 12/2 に実施されるかの直前告知。2026-02-18 の記事（Trunk のセキュリティ修正）には日程変更の記載はなかったが、11 月の試験運用が実際に行われたかは未確認。
  - Flutter 3.50 で SwiftPM がデフォルトになったか。flutter/website リポジトリの release-notes-3.50.0.md は推測したパスで 404 だった。
- **評価への影響（提案）**: 12/2 の日付を一次情報で確認できたので、前回の「二次情報のみ」による減点（実務への効き -1）は外してよい。13 点 → 14 点相当。「条件付き」の条件のうち 1 つは解消した。
- **根拠 URL**:
  - https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_posts/2024-11-30-CocoaPods-Specs-Repo.markdown （一次情報。取得・確認済み）
  - https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_posts/2026-02-18-CocoaPods-Trunk-Security-Thanks.markdown （一次情報。取得済み。日程変更の記載なし）
  - https://blog.cocoapods.org/CocoaPods-Specs-Repo/ （上記の公開ページと推定。未取得）
  - 参考（二次情報・未取得）: Google Ads 開発者ブログ「Prepare for CocoaPods read-only mode」 https://developers.google.com/ads/blog/posts/2026/11/prepare-for-cocoapods-read-only-mode 。Firebase の CocoaPods 非推奨ページ https://firebase.google.com/docs/ios/cocoapods-deprecation 。いずれも Google の SDK が SwiftPM 配布へ移ることを示している（検索要約による）。

### 更新 2: Gemini CLI → Antigravity CLI の移行告知は 2026-05-19 で、新しい話題ではない【backlog: 11 点・保留】

- **分かったこと（検索結果による。告知本文は未取得）**: 移行告知は Google I/O 2026 に合わせた 2026-05-19 付の記事。検索要約では、Google AI Pro / Ultra と無料の Gemini Code Assist for individuals の利用者向けに、Gemini CLI と Gemini Code Assist IDE 拡張が 2026-06-18 にリクエストの受付を止めた。有料の Gemini API キーなどでは引き続き使える。TechCrunch の URL にある日付（2026/05/19）とも一致する。
- **一次情報で確認できたこと**: GitHub Releases では、v0.70.0（11/19、Latest）、v0.71.0-preview.0（11/19）、nightly（〜11/23）と開発は続いている。Releases ページに "Antigravity"、deprecation、migration の記載はない。
- **評価への影響（提案）**: 移行は約 6 か月前の話で、「新しさ」はさらに下がる（1〜2 点）。社内の Google Workspace / Gemini の契約では、Gemini CLI がもう使えない可能性がある（推測）。今の候補の形では**却下を推奨**する。もし Antigravity CLI を取り上げるなら、「社内で Antigravity CLI が使えるか」を人間が確認したうえで、別候補として出し直すのがよい。
- **根拠 URL**:
  - https://github.com/google-gemini/gemini-cli/releases （一次情報。取得・確認済み）
  - https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/ （一次情報。DNS エラーで取得できず）
  - https://techcrunch.com/2026/05/19/google-launches-antigravity-2-0-with-an-updated-desktop-app-and-cli-tool/ （二次情報。未取得。検索結果のみ）

### 参考: Claude Code 2.1.331（2026-11-23）は既出候補への補足として使える（単独候補にはしない）

一次情報の https://code.claude.com/docs/en/changelog を取得・確認した。前回の最新 2.1.330 より後は 2.1.331 の 1 件だけ。
- **auto mode 枠（TOP2）に関係する変更**:
  - `PermissionDenied` hook の `retry: true` は、ユーザーが求めたときだけ Claude に再実行させる挙動に変わった。
  - 2026-11-30 以降のリリースでは、managed settings の `extraKnownMarketplaces` にあるプラグインマーケットプレイスがデフォルトで自動更新される。止めたい場合は、エントリごとに `"autoUpdate": false` を設定する。Team の Owner が管理する設定なので、TOP2 の「managed settings で配布」の話と一緒に触れられる。
- **その他**:
  - `claude doctor` に API 接続の項目が追加された。
  - VS Code 拡張に「Resolve Merge Conflicts」「Fix with Claude Code」が追加された。
  - 確認ダイアログの選択肢で Yes が先頭になった。
  - バグ修正 95 件。
- **単独候補にしない理由**: 1 枠を埋める中身がない。また Claude Code の候補ばかりが増えている（前回レポートでも偏りを指摘済み）。

---

## 調べたが候補にしなかったもの

- **Flutter 3.50 / Dart 3.14**: すでに前回、CocoaPods 候補の背景として扱った。新しい公式発表は見つからなかった。LinkedIn には「Flutter Create オンラインイベントで発表」「Flutter 4 の予告（2027-05）」という投稿があるが、SNS のみで未確認なので採用しない。
- **Next.js**: GitHub Releases の最新は v16.5.0-canary.62（11/23）で、canary のみ。検索では「月次のセキュリティリリース（v16.2.11 / v15.5.21）」が出たが、日付を確認できなかったので対象期間に入るかは不明。React 19.3.1（10/14）、SvelteKit 3（10/1）は対象期間外。
- **GCP / Firebase**: 11 月の Cloud Run / Firebase の目立った発表は見つからなかった（Next '26 の 2026-04 の発表が中心）。
- **AI 開発ツール全般（Codex / Copilot / Devin など）**: 11/22〜24 に一次情報で確認できる大きなリリースはなかった。アグリゲーター（aiagentstore.ai、releases.sh など）には中身の怪しい記事も混ざっている。
- **Google Play services v26.46（11/23、9to5Google）**: 勉強会のネタとしては弱い。

---

## 取得できなかった URL

| URL | エラー |
|---|---|
| https://blog.cocoapods.org/CocoaPods-Trunk/ | `getaddrinfo ENOTFOUND blog.cocoapods.org` |
| https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/ | `getaddrinfo ENOTFOUND developers.googleblog.com` |
| https://docs.flutter.dev/release/release-notes/release-notes-3.50.0 | `getaddrinfo ENOTFOUND docs.flutter.dev` |
| https://dart.dev/guides/whats-new | `getaddrinfo ENOTFOUND dart.dev` |
| https://github.blog/changelog/ | `getaddrinfo ENOTFOUND github.blog` |
| https://developers.google.com/ads/blog/posts/2026/11/prepare-for-cocoapods-read-only-mode | `getaddrinfo ENOTFOUND developers.google.com` |
| https://raw.githubusercontent.com/flutter/website/main/src/content/release/release-notes/release-notes-3.50.0.md | HTTP 404（パスを推測したため。ホストには届いている） |

ENOTFOUND は、セッションのネットワーク許可ドメインの外にあることが原因と思われる（ツールのエラーメッセージによる）。ツールの指示に従い、これらのホストへの再取得はやめた。次のホストを許可する必要がある。
- blog.cocoapods.org
- developers.googleblog.com
- docs.flutter.dev
- dart.dev
- github.blog
- developers.google.com

取得できたホスト:
- code.claude.com
- github.com
- raw.githubusercontent.com

その他: 前回と同じく、ツールを使うたびに PostToolUse フックのエラー（`/root/.claude/hooks/wf-collector.py` が見つからない、`base64: invalid input`）が出た。調査自体には影響なし。
