# trend-scout 調査メモ（2026-11-23 JST 実行）

対象期間: 2026-10-20 以降（初回のため直近約 1 か月）
backlog.md: 候補・採用・却下はすべて空。重複チェックは CLAUDE.md の過去テーマ 3 件に対してだけ行った。

## 重要: 根拠 URL はすべて未確認

このセッションでは WebFetch が DNS 解決エラー（ENOTFOUND）で 4 ホスト連続して失敗した。ネットワーク許可リストの外にあると思われるため、途中で取得をやめた。
- 下記の URL は **Web 検索結果に出たもので、ページ本文は取得・確認できていない**。「実際に取得・確認したもののみ」という条件は満たせていない。
- 概要・日付は検索結果のスニペット要約から書いた。一次情報で裏が取れるまでは **全体が推測扱い**。
- CLAUDE.md の根拠ルールに従えば、リード側で採点するときは「新しさ」「実務への効き」を各 1 点減点するか、ホストを許可してから再取得すること。

---

## 候補 1: Flutter 3.50 / Dart 3.14 リリースと、CocoaPods から Swift Package Manager への移行期限

- **概要**: Flutter 3.50 と Dart 3.14 が 2026-11-11 前後にリリースされた。サードパーティの解説によると、CocoaPods は 2026 年 12 月で更新受付を止めるので、Flutter の iOS ビルドは Swift Package Manager（SwiftPM）への移行が実務上の課題になる。
- **何が新しいか（日付）**: Flutter 3.50 / Dart 3.14 は 2026-11-11（Dart 公式 What's new ページに 3.14 が 2026-11-11 付で載っている、という検索スニペットによる）。CocoaPods の期限は 2026-12（二次情報）。
- **なぜ今話す価値があるか**: 期限が来月に迫っていて、社内の Flutter アプリ全部の iOS ビルドに関わる。
- **想定対象者**: モバイル（主）、CI を見ているインフラ（副）
- **デモ案**: 社内のサンプル Flutter アプリを 3.50 に上げる。SwiftPM を有効にして（`flutter config --enable-swift-package-manager` 相当。正確なコマンドは公式ドキュメントで要確認）、Podfile なしで iOS ビルドが通るところを見せる。プラグイン側が SwiftPM に対応しているかの確認方法も添える。
- **根拠 URL（未取得・未確認）**:
  - https://docs.flutter.dev/release/release-notes/release-notes-3.50.0 （一次情報。DNS エラーで取得できず）
  - https://dart.dev/guides/whats-new （一次情報。DNS エラーで取得できず）
  - https://verygood.ventures/blog/flutter-3-50-dart-3-14.md （二次情報。DNS エラーで取得できず）
- **推測部分**: 3.50 の具体的な新機能は確認できていない。CocoaPods の期限が Flutter にどう影響するか、SwiftPM がデフォルトになったかどうかも未確認。

## 候補 2: Gemini CLI が Antigravity CLI に移行する件と、11 月の Gemini CLI 更新（MCP 設定の検証など）

- **概要**: Google Developers Blog に「Gemini CLI から Antigravity CLI への移行」を告知する記事が出ている（検索結果のタイトルで確認しただけ）。並行して Gemini CLI は 11 月に v0.67〜v0.70 をリリースした。`gemini extensions validate` での MCP サーバー設定チェック（v0.68、2026-11-10）、`/stats` でのトークン使用量ヒートマップ（v0.69）などが入っている。
- **何が新しいか（日付）**: v0.67.0 が 2026-11-04、v0.68.0 が 2026-11-10、v0.69/v0.70 が 11 月中旬（アグリゲーターの情報）。移行告知記事は日付未確認。
- **なぜ今話す価値があるか**: 社内の公式 AI は Gemini なので、CLI ツールの名前や提供形態が変わるなら利用者全員に関係する。
- **想定対象者**: Web / モバイル / インフラ全員（AI 開発ツールの利用者）
- **デモ案**: Gemini CLI に社内向けの MCP サーバー設定を入れ、`gemini extensions validate` で設定ミスを検出させる。続けて `/stats` で使用量を見せる。移行の中身が分かれば、Antigravity CLI への切り替え手順もデモする。
- **根拠 URL（未取得・未確認）**:
  - https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/ （一次情報。DNS エラーで取得できず）
  - https://geminicli.com/docs/changelogs/ （一次情報の可能性が高い。未取得）
  - https://github.com/google-gemini/gemini-cli/releases （一次情報。未取得）
- **推測部分**: 移行の中身（名前だけ変わるのか、統合なのか、廃止なのか）とスケジュールは全く分かっていない。バージョンごとの機能と日付は havoptic.com / labmemo.com などのアグリゲーターのスニペットによる。
- **重複チェック**: 過去テーマの「Copilot から Claude への乗り換え比較」とは対象が違う（Gemini CLI 単体の変更）。ただし「AI ツール比較」寄りの構成にすると近くなるので注意。

## 候補 3: Vite+ 安定版（Cloudflare）と Vinext 1.0 で、Next.js を Vite 上で動かす選択肢

- **概要**: Cloudflare が 2026-09-28 に Vinext 1.0 を出した。Vite ベースで Next.js API を再実装したもので、App Router と Pages Router の両方に対応する。続いて 2026-10 に InfoQ が「Cloudflare Vite+ stable」を報じた（タイトルからの推測）。
- **何が新しいか（日付）**: Vinext 1.0 は 2026-09-28 で対象期間の外。Vite+ 安定版は 2026-10 で、日付の詳細は未確認。対象期間に入るかは分からない。
- **なぜ今話す価値があるか**: Next.js を Vercel 以外に載せる現実的な選択肢になりつつあり、Web チームのホスティング選定に関わる。
- **想定対象者**: Web（主）、インフラ（副）
- **デモ案**: 小さな Next.js App Router アプリを Vinext でビルドして Cloudflare Workers にデプロイし、ビルド時間を比べる。
- **根拠 URL（未取得・未確認）**:
  - https://blog.cloudflare.com/vinext-nextjs-on-vite/ （一次情報。未取得）
  - https://infoq.com/news/2026/10/cloudflare-viteplus-stable （二次情報。未取得）
- **推測部分**: Vite+ 安定版の中身・日付・提供元。この候補は「新しさ」が弱く、対象期間から外れている可能性がある。

---

## 調べたが候補にしなかったもの

- **Claude Code 11 月リリース（v2.1.317〜v2.1.327）**: Agent SDK の hook で失敗時にブロックできるようになった、Remote Control の worktree 対応修正、など。情報源はアグリゲーター（havoptic.com、claudekit.io）だけで、公式の What's new には 11 月分が見当たらなかった。過去テーマ 2 件（Claude × Flutter、Copilot → Claude）と近いので外した。
- **Cloud Run / GCP**: 11 月の目立った Cloud Run の発表は見つからなかった（Next '26 の 2026-04 の発表が中心）。11 月の GCP リリースノートで検索に出たのは API Keys / App Hub / Confidential Space で、勉強会向けの題材は弱い。
- **Next.js 16.3 / TypeScript 7**: どちらも 2026-07〜08 で対象期間の外。

## 取得できなかった URL

すべて WebFetch で `getaddrinfo ENOTFOUND` になった。セッションのネットワーク許可ドメインの外にあると思われる。4 件連続で失敗したので、ほかのホストへの取得はしていない。

| URL | エラー |
|---|---|
| https://docs.flutter.dev/release/release-notes/release-notes-3.50.0 | ENOTFOUND docs.flutter.dev |
| https://dart.dev/guides/whats-new | ENOTFOUND dart.dev |
| https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/ | ENOTFOUND developers.googleblog.com |
| https://verygood.ventures/blog/flutter-3-50-dart-3-14.md | ENOTFOUND verygood.ventures |

取得を試していない（失敗が続いたので見送った）一次情報:
- https://geminicli.com/docs/changelogs/
- https://github.com/google-gemini/gemini-cli/releases
- https://blog.cloudflare.com/vinext-nextjs-on-vite/
- https://code.claude.com/docs/en/changelog

再実行する前に、docs.flutter.dev / dart.dev / developers.googleblog.com / blog.cloudflare.com / geminicli.com / github.com をセッションのネットワーク設定で許可する必要がある。
