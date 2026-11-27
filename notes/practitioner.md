# practitioner 調査メモ 2026-11-27（JST）

- 対象期間: reports/2026-11-26.md（11/26 朝）以降
- 観点: 明日から業務で試せるか（新しさより持ち帰れるか）
- **結論: 新規候補は「なし」**。対象期間に出た一次情報のリリースは claude-code-action v1.0.290（11/25、修正 1 件）だけで、単独の枠にはならない。Claude Code、gh、Flutter、Dart、Firebase CLI、Backlog MCP、runner-images には、前回レポート以降の新しいリリースや Announcement がなかった。
- 重複確認: backlog.md の候補・却下と、CLAUDE.md の過去テーマを確認済み。

---

## 新規候補

なし

---

## 既出の更新

### 1. `/code-review` + `/loop`（TOP1）: `/loop` の上限が 7 日から 30 日に延びている（2.1.330、11/20）

- 期間外（11/20）の情報だが、backlog と前回までのレポートに記載がないので補足する。
- changelog 原文: "Changed recurring scheduled tasks and `/loop` to run up to 30 days instead of 7 while their session keeps running, and say when they end; tasks made before this release still end at 7 days"
- スライドへの反映案
  - 後半の「`/loop` で CI 見張り」で、「セッションが続く限り最長 30 日。終了時に通知される」と一言添える。
  - 2.1.330 より前に作ったタスクは 7 日で終わる。デモは 2.1.332 で作り直したタスクを使う。
- 点数への影響: なし（補足のみ）
- 根拠: https://code.claude.com/docs/en/changelog （2.1.330、取得・原文確認済み）

### 2. claude-code-action（11/26 却下）: v1.0.289（11/23）、v1.0.290（11/25）

- v1.0.289: ログをプレーンテキストで出し、溜めていたコメントを run step 内で投稿する（#2122）。
- v1.0.290: セッション終了時に `GITHUB_OUTPUT` を元に戻す（#2121）。
- 却下の判断は変わらない。社内で使っている場合は、v1.0.288 の `allow_fork_prs` の確認と合わせて、最新版に上げればよい。
- 根拠: https://github.com/anthropics/claude-code-action/releases （取得済み。ページに年の表示はないが、前後の版から 2026 年と判断）

### 3. その他（変化なし）

- **gh**: 最新は v2.106.0（11/25）のまま。`gh event`（v2.105.0）は preview のまま。
- **Flutter**: stable の最新タグは 3.50.1（11/19）、beta は 3.51.0-0.2.pre（11/18）のまま。whats-new の最新も 11/11（3.50）のまま。
- **Dart SDK**: CHANGELOG の最新リリースは 3.13.7（2026-10-22）。3.14.0 / 3.15.0 は "Unreleased"。
- **Firebase CLI**: 最新は v15.37.0（11/19、却下済み）のまま。
- **Backlog MCP Server**: 最新は v0.23.4（11/18）のまま。
- **runner-images**: 最新の Announcement は #15397（11/23）で既出。#15315 / #15314（Windows の D: ドライブ変更、11/30）も既出の「ランナーイメージ」候補の範囲内。

---

## 参考（候補にはしない）

- **Claude Code の managed プラグインマーケットプレイスが、11/30 以降に自動更新が既定になる（2.1.331、11/23 告知）**
  - 原文: "Changed in a future release, on or after November 30, 2026: plugin marketplaces in managed `extraKnownMarketplaces` will auto-update by default. To opt one out, set `"autoUpdate": false` on its entry"
  - 期間外であり、managed settings でマーケットプレイスを配っている組織にしか関係しない。勉強会のネタとしては薄い。
  - 社内で managed `extraKnownMarketplaces` を使っているなら、11/30 より前に `autoUpdate` の方針を決める必要がある（周知事項）。
  - 根拠: https://code.claude.com/docs/en/changelog （2.1.331、取得・原文確認済み）
- **Gemini in Gmail の対象拡大（11/10 から段階的に展開）**
  - 長いスレッドの AI 要約と「Help me write」が、より多くの Workspace ユーザーに既定 ON で展開される、という内容。
  - 期間外であり、公式ブログ（workspaceupdates.googleblog.com）は取得できず、検索結果の要約でしか確認していない。根拠のルール上、候補にはしない。
  - 社内の Workspace エディションが対象か、管理者が OFF にしていないかを確認できれば、「Gemini in Gmail で長いスレッドを読む」Tips として予備ネタになり得る（推測）。
  - 検索で見つかった URL（本文は未取得）: https://workspaceupdates.googleblog.com/2026/11/expanding-gemini-in-gmail-to-more-Google-Workspace-users.html?m=1

---

## 取得できなかった URL

DNS 解決エラー（ENOTFOUND）。セッションのネットワーク許可リストの外と推測。
- https://workspaceupdates.googleblog.com/
- https://workspace.google.com/blog/ja/product-announcements/november-workspace-drop-nano-banana-pro-in-slides-vids-and-more
- https://support.google.com/a/table/7314896?hl=en

上の 3 件が失敗したため、Google 系の他ホストは試していない。github.blog / nulab.com / modelcontextprotocol.io は前回 ENOTFOUND だったので、今回は試していない。

## 補足

- Google Workspace / Gemini の一次情報を見るには、workspaceupdates.googleblog.com、workspace.google.com、support.google.com の 3 ホストを許可リストに追加する必要がある。
- firebase/flutterfire の Releases ページには古い pre-release しか出ず、使えなかった（パッケージごとの CHANGELOG を見る必要がある）。
- dart-lang/sdk の Releases ページは空。Dart のリリースは raw の CHANGELOG.md で確認した。
- PostToolUse hook のエラー（`/root/.claude/hooks/wf-collector.py` が見つからない、`base64: invalid input`）は今回も出た。調査には影響なし。
