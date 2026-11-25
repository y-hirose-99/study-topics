# critic レビュー（2026-11-25 JST 実行）

- 対象: notes/trend-scout.md（新規 3・更新 2）、notes/practitioner.md（新規 3・更新表）
- 照合: CLAUDE.md（評価基準・根拠ルール・過去テーマ 3 件）、backlog.md（候補 5・却下 4）、reports/2026-11-24.md
- critic が今回自分で取得して確認した一次情報: 下記「確認した URL」の 17 件（すべて github.com / raw.githubusercontent.com）

## 判定一覧

| ID | 候補 | 判定 | 一言 |
|---|---|---|---|
| T1 | Flutter 3.50 material_ui / cupertino_ui 移行 | **採用可**（軽微な修正あり） | 一次情報と主張が一致。今回の本命。日付の書き方だけ統一する |
| P1 | Flutter 3.50.1 hotfix | **却下（単独枠として）→ T1 に統合** | 単独だと「CHANGELOG を読もう」以上の話にならない。T1 の 3 分パートにする |
| P2 | GitHub Actions ランナーの 11 月の変更 | **条件付き** | issue 番号は全件一致。Go の日付が不正確、タイトルが言い過ぎ |
| P3 | Backlog MCP Server | **条件付き（新しさ 1 点で採点）** | 初版は 2025-04-21。ニュースではなく実務 Tips として扱う |
| T2 | Dart package skills | **却下（再評価の条件つき）** | 新しさ不足（最新でも 10/16）。デモが成立するか未確認。出典の書き方にも誤りあり |
| T3 | Firebase CLI v15.37.0 / Crashlytics MCP | **却下** | 中核が二次情報だけ。リリース本体は小さな更新の寄せ集め |
| 更新 U1 | CocoaPods trunk read-only（TOP3） | 据え置き。「3.44 から SwiftPM が既定」の推測表記を外す | practitioner の「未確認」は誤り。critic も一次情報で確認した |
| 更新 U2 | `gh event listen`（保留） | 保留のまま | 日付の差（11/17 と 11/18）は告知日とリリース日の違いで、矛盾ではない |

---

## T1: Flutter 3.50 で非推奨になった Material / Cupertino を `material_ui` / `cupertino_ui` に移行する — 採用可

**一次情報との照合（critic が取得して確認）**
- 非推奨の文言 "The in-framework design libraries are formally deprecated in Flutter 3.50." → 移行ガイドの原文と一致。
- `dart fix --apply --code=migrate_design_widgets` → 一致。
- 経緯（3.44 で凍結 → 3.47 で独立パッケージ 1.0 → 3.50 で非推奨）→ 一致。ガイドには "Landed in version: 3.49.0-1.0.pre-295 / In stable release: 3.50" もある。
- `MaterialUiCompatibilityBridge`（`MaterialApp.builder` の中で使う一時的なラッパー）、gen-l10n の `header: '// ignore_for_file: deprecated_member_use'`、`ambiguous_import`、"No MaterialLocalizations found"、"Treat this migration as a major version bump" → すべて一致。
- 削除するバージョンはガイドに書かれていない → 「未定」とした扱いは正しい。

**3.50 のリリース日の食い違い（要修正）**
- trend-scout は「2026-11-11」（whats-new.md の "11 November 2026: Release 3.50"）、practitioner は「2026-11-09」（tags ページ）。critic が両方を確認した。**どちらも出典どおりで、タグの作成が 11/9、公式の告知（What's new / ブログ）が 11/11。**
- 発表とレポートでは「3.50 は 2026-11-11 告知（タグは 11/9）」と書き方を 1 つに揃えること。新しさは直近 1 か月以内なので、どちらの日付でも 5 点。

**社内 Flutter アプリへの影響の大きさ**
- Material か Cupertino を import している Flutter アプリは、ほぼ全部が対象になる。影響を受ける範囲は広い。
- ただし**現時点では非推奨の警告が出るだけで、ビルドは壊れない**（削除時期は未定）。発表では「今すぐ壊れる」と煽らないこと。ポイントは次の 2 つ。
  1. `dart fix` は 1 コマンドで終わるので、それ自体はデモ映えしない。落とし穴（gen-l10n、`flutter_localizations` との `ambiguous_import`、CupertinoApp の delegates、未移行の依存パッケージ）が中身になる。
  2. 社内で**自作の共通パッケージ**を持っていれば「major version bump 扱い」が効いてくる。ここが社内に最も刺さるはず（推測。社内パッケージがあるかは未確認）。
- 「非推奨の警告が出始めたばかりの今がちょうどよい」は妥当。

**15 分 / デモ**
- 要点は「経緯・dart fix・落とし穴」の 3 つで、15 分に収まる。前提知識は Flutter 開発者なら不要。
- デモは `flutter create` + l10n + `dart fix` で、30 分以内に準備できる。ただし発表者の環境に 3.50 系が必要（**3.50.1 を使うこと**。P1 参照）。gen-l10n の警告を見せるには、l10n の設定を最初から入れておく。
- `material_ui` / `cupertino_ui` の pub.dev ページは未取得。パッケージの最新版や pub points は、発表者が事前に確認すること。

**対象チェック**: モバイル（Flutter）のみ。Web / インフラの参加者には効かない。モバイルが中心の会社なので許容範囲だが、「実務への効き」は 4 が上限（半数以上かどうかは社内の構成次第）。

**推測の扱い**: 「AGP 9.x、最低 9.1.1」「Android 17 対応」は PR タイトルの要約で、未確認。**スライドに事実として書かないこと**（メモでは推測と明記されており、問題ない）。

**重複**: 過去テーマ「Claude を使った Flutter モバイル開発」とは重ならない（AI ではなく SDK の移行の話）。ただし、デモで Claude に移行させると見た目が重なるので、`dart fix` だけで見せる。

**採点の目安（critic 案）**: 新しさ 5 / 実務 4 / 15 分 4 / デモ 4 = 17

---

## P1: Flutter 3.50.1 hotfix — 単独枠としては却下、T1 に統合

**一次情報との照合**
- stable CHANGELOG の 3.50.1 は 11 件で、issue 番号（196412、196444、193695、dart-lang/webdev/2883、195885、196452、196119、196118、196121、196559、195006）と症状は practitioner の記述と一致。
- tags ページ: 3.50.1 = 11/19、3.50.0 = 11/9 → 一致。
- **見落とし**: tags ページによると 3.51.0-0.2.pre（11/18、beta）にも split APK の versionCode の修正が入っている。beta チャネルを使っている人には関係が薄い、という補足が必要。

**却下理由（単独枠として）**
1. **話の寿命が短い**。社内アプリが 3.50.0 に上がっていなければ「上げるなら 3.50.1」の 1 行で終わる。practitioner 自身も、社内アプリが 3.50.0 か未確認と書いている。
2. **要点が「バグ 4 件の列挙」**で、学びが「hotfix の CHANGELOG を読め」に集約される。15 分の枠を持たせるには弱い。
3. **「Flutter の hotfix は最新の stable だけに出る」には出典がない**。事実として書かれているので、出典を付けるか推測と明記すること。
4. デモ（3.50.0 と 3.50.1 で 2 回ビルドし、`unzip -l` と `aapt2 dump badging` で比べる）は再現性があり、30 分で準備できる。**ここは良い**。T1 に入れるなら、`.env` のアセット欠落だけを 2〜3 分で見せるのが効率的。

**統合提案**: T1 を「Flutter 3.50 に上げるときに踏むもの」にして、構成を次のようにする。
- (1) 経緯 2 分、(2) `dart fix` と落とし穴のデモ 8 分、(3) 「上げるなら 3.50.1」: `.env` と `_` 始まりのアセットが欠ける件と、split APK の versionCode の件を 3 分、(4) まとめ 2 分。
- **CocoaPods（U1）はまとめない方がよい**。3 つにすると要点が 3 つを超え、15 分に入らない。CocoaPods は 12/2 という日付に縛られた別の話で、iOS / Mac が必要なのでデモの環境も違う。入れるとしても最後に 1 枚（「iOS 側では 12/2 に CocoaPods が read-only になる。SwiftPM は 3.44 から既定」）にとどめる。

---

## P2: GitHub Actions ランナーの 11 月の変更 — 条件付き

**日付と issue 番号の照合（critic がすべて取得して確認）**
| 主張 | 一次情報 | 判定 |
|---|---|---|
| #14748 ubuntu-latest → 26.04、10/19 から段階移行、11/19 完了予定 | タイトル "`ubuntu-latest` label will use Ubuntu 26.04 in November 2026"、9/17 起票、"beginning October 19, 2026"、完了目標 November 19, 2026。open のまま | 一致 |
| カーネル 6.17 → 7.0、systemd 255 → 259、Java 17 → 25 | 本文の表と一致。ただし**コメントに転記された表では Java 17 が既定のまま**になっていて、版によって食い違う | 概ね一致（Java は「本文の表では」と付ける） |
| #15397 Xcode 27 GA、11/23、`runs-on: xcode-27` | タイトル "[macOS] Xcode 27 is now generally available"、11/23 起票、`xcode-27` / `xcode-27-xlarge` | 一致 |
| #15315 Windows の D: が ReFS に、11/30 | 11/12 起票、"Image deployment will start on November 30, 2026 and will take 2-3 days" | 一致（「11/30 から 2〜3 日かけて」が正確） |
| #15253 Go 1.24 の tool cache 削除、2026-11-18 | 11/4 起票、"no earlier than November 18, 2026"（数日ずれる可能性あり）。**既定の Go が 1.26 に上がる点が抜けている** | **不正確**。「11/18 以降」と書き、既定の Go の変更を足す |
| 移行の完了 | メンテナーによる完了の確認はない。ユーザーからは 11 月中旬の時点で 26.04 になっていたという報告のみ | 「完了予定」の表現は正しい |

**問題点**
1. **タイトル「11 月に一斉に変わった」は言い過ぎ**。ReFS は 11/30 開始でまだ起きていない。Ubuntu の移行完了も未確認。「11〜12 月に順に変わる」が正確。
2. **4 つの変更を 15 分で並べると散漫になる**。Ubuntu 26.04 を主役にし、Xcode 27 と Windows ReFS は社内で使っている場合だけ各 1 枚にする。
3. **デモで使う `ubuntu-26.04` ラベルが今使えるかは、critic も未確認**。デモの前提なので、発表者が事前に 1 回走らせて確かめること。
4. issue のコメントにある不具合報告（Docker 29、bwrap、pip の TLS）はユーザー報告にすぎない。practitioner が「報告がある」にとどめているのは正しい。
5. 社内の CI が GitHub Actions か未確認（GitHub は社内ツールにあるが、CI まで Actions とは限らない）。ここ次第で「実務への効き」が 2〜4 に振れる。

**15 分 / デモ**: `-latest` の洗い出しから 24.04 / 26.04 の matrix 実行まで、30 分以内に準備できる。ただし実行結果の差は `java -version` などの地味な出力なので、デモ映えは 3 が妥当。

**重複**: 過去テーマ・backlog とは重ならない。Xcode 27 の部分は CocoaPods 候補（U1）と少し重なるので、どちらか一方に寄せる。

**条件**: (a) タイトルを修正、(b) Go の日付を「11/18 以降」に直し、既定が 1.26 になる点を追記、(c) `ubuntu-26.04` ラベルを事前に確認、(d) 社内の CI が Actions であることを確認。

**採点の目安（critic 案）**: 新しさ 5 / 実務 3（Actions 利用が確認できれば 4）/ 15 分 4 / デモ 3 = 15

---

## P3: Backlog MCP Server — 条件付き（新しさ 1 点で採点）

**新しさの確認（今回の主な指摘）**
- practitioner は「2025 年から公開されていたと記憶している（推測）」と書いていた。critic がリリースページの最終ページを確認したところ、**Release 0.0.1 が 2025-04-21**。約 19 か月前から公開されている。**評価基準では「1 年以上前から定番」に当たり、新しさは 1 点**（practitioner の 2〜3 点は甘い）。
- 直近（10/13〜11/18）の変更は、破壊的変更 2 件（v0.21.0 の応答の圧縮、v0.22.0 の `get_document` のプレーンテキスト化）、セキュリティ修正（v0.23.3）、依存更新（v0.23.4）。目玉の新機能はない。日付と内容はリリースページと一致した。
- CVE 番号 "CVE-2026-111768" はリリースページの表記と一致した。ただし CVE の採番としては桁が大きく見慣れない。**発表で触れるなら、NVD か GHSA で実在を確認してから**にすること。

**それでも残す理由**
- 社内ツールに Backlog と Claude Team がそろっていて、「課題の要約 → 子課題の起票」のデモはモバイル / Web / インフラの全員（エンジニア以外も）に効く。実務への効きは今回の候補で最も高い（4〜5）。
- デモの準備（テスト用プロジェクト、API キー、`npx backlog-mcp-server`）は 30 分以内でできる。
- 過去テーマ「Copilot から Claude への乗り換え比較」とは重ならない。

**条件**
1. 新しさは 1 点で採点し、「最新情報」ではなく「定番の実務 Tips」として出す。TOP3 に入れるなら、ほかに新しい候補がない回に限る。
2. 社内で Backlog の API キー発行と、外部 MCP サーバーの利用が許可されているかを、人間に確認する（README には「無保証」とある）。
3. Backlog 本体の AI アシスタントとの使い分け（検索要約のみ）を質問される可能性がある。発表前に一次情報を確認するか、「調べていない」と明言する。

**採点の目安（critic 案）**: 新しさ 1 / 実務 5 / 15 分 4 / デモ 4 = 14

---

## T2: Dart の package skills（`dart run skills@ get`）— 却下（再評価の条件つき）

**一次情報との照合**
- `dart run skills@ get`、`get` / `add` / `list` / `prune` / `remove`、`-a/--all`、`.agents/skills/`、`.config/dart_skills/` → site-www の package-skills.md と一致。
- `create` サブコマンドは docs になく、CHANGELOG（1.0.0-beta.2 で追加）にある → 実在するが、出典は CHANGELOG。
- **出典の誤り**: trend-scout は「CHANGELOG によると Claude Code、Cursor、GitHub Copilot、Codex、Antigravity などに対応」と書いているが、CHANGELOG に出てくるのは Claude Code、Codex、Antigravity だけ（1.0.0 の "generic (antigravity, codex)"）。**Cursor と Copilot は docs 側の記載**（docs には Cursor、Gemini、Claude Code、Cline、Copilot がある）。結論は大きく変わらないが、出典を書き分けること。
- リリース日: skills v1.0.1 が 9/4、v1.0.2 が 9/29、v1.0.3 が 10/2、v1.0.4 が 10/16 → 一致。docs の追加は 8/31（trend-scout の確認。critic は未取得）。

**却下理由**
1. **新しさが不足している**。1.0 系は 8 月末〜9 月初めに出ていて、最新の 1.0.4 も 10/16。「Flutter 3.50 の docs で整理された」は docs の再編にすぎず、機能が新しくなったわけではない。新しさは 2 点。
2. **デモが成立するか未確認**。社内で使っている主要パッケージ（Firebase など）が `skills/` を同梱しているかは誰も確認していない。同梱がなければ `get --all` の結果は空になり、デモにならない。
3. 「スキルあり / なしで、エージェントの出力が変わる」比較は毎回結果が変わる。15 分の枠では再現性に賭けることになり、デモ映えは 2〜3。
4. 過去テーマ「Claude を使った Flutter モバイル開発」との重なりを避けるには、Claude 以外のエージェントでデモする必要がある。社内の公式ツールは Gemini（Workspace）で、Gemini CLI / Antigravity を社内で使えるかも未確認。

**再評価の条件**: 社内の pubspec の直接依存のうち `skills/` を同梱しているパッケージが 2 つ以上あると確認できたら、T1 の補足（「移行後の material_ui にもスキルがあれば」など）か、AI 活用枠として再提出してよい（material_ui にスキルが同梱されているかは未確認で、推測）。

---

## T3: Firebase CLI v15.37.0 / Crashlytics MCP — 却下

**一次情報との照合**
- v15.37.0（11/19 02:43 UTC）: Node.js 26 ランタイム（Preview）、`@modelcontextprotocol/sdk` を 1.32.2 に上げた critical / high の修正、`deploy --dry-run` が Firestore DB を作ってしまう不具合の修正、`get_report` への `filterExpression` 追加 → すべて一致。
- v15.36.0: `list_events` への `filterExpression` 追加 → 一致。**日付は 11/11 23:58 UTC なので、JST では 11/12**。軽微だが修正すること。

**却下理由**
1. **中核の「Crashlytics MCP の使い方」が二次情報だけ**（firebase.google.com は ENOTFOUND）。根拠ルールで新しさと実務を各 1 点減点する。
2. リリースで新しいのは `filterExpression` の追加という小さな拡張だけ。Crashlytics MCP 自体は v15.27.0（8/13）より前からある。Node.js 26 は Preview のランタイム追加で、話の軸にならない。trend-scout 自身も「中身が薄い」と書いている。
3. **デモに実データが必要**。実際のクラッシュがある Firebase プロジェクトへのアクセスと、社内アプリが Crashlytics を使っているかの確認が必要で、30 分で準備できるかは不明。本番のクラッシュデータを社内勉強会の画面に出してよいかの確認も要る。
4. セキュリティ修正による「CLI を更新しよう」は、Slack の 1 行で済む話で、15 分枠にする理由にならない。

**出し直すなら**: firebase.google.com のドキュメントを一次情報で読めるようになり、社内アプリで Crashlytics を使っていると確認できたら、「Crashlytics × MCP で上位クラッシュを AI にトリアージさせる」として新規提出する（その場合も新しさは 2〜3）。

---

## 既出候補の更新

### U1: CocoaPods trunk read-only（TOP3）
- critic も flutter/website の for-app-developers.md を取得して、"As of the 3.44 release, Flutter's Swift Package Manager (SwiftPM) support is on by default." と "the CocoaPods registry permanently becomes read-only on December 2, 2026." を確認した。**trend-scout の確認が正しい。practitioner の更新表にある「3.44 からの既定化は今回も一次情報で確認できていない」は誤り**。リードは backlog とレポートの「推測」表記を外してよい。
- 一方で、**フォールバックしているプラグインを一覧にする方法は docs にない**。docs にあるのは、Xcode で SwiftPM が使われているかを確認する方法だけ。デモ「フォールバックの洗い出し」は Podfile.lock などを手で見るもので、見た目は地味（推測）。デモ映え 3 は据え置きか、2 に下げる余地がある。
- practitioner が見つけた 3.47.2 の修正（flutter/190846、Xcode 27 で SwiftPM 経由の add-to-app ビルドが失敗する）は CHANGELOG と一致した。add-to-app 構成でなければ関係ないので、補足にとどめる。
- 12/2 を過ぎた後に発表する場合の言い換えが必要、という前回の条件はそのまま。

### U2: `gh event listen`（保留）
- v2.105.0 のリリースページの表示は "18 Nov 00:18"（タイムゾーンの表記なし）、告知は 2026-11-17。**告知日とリリース日の違いで、矛盾ではない**。backlog は「告知 11/17、リリース 11/18」と両方を書けばよい。
- 現在も preview で、ウェイトリストのリンクがある。保留の判断は変わらない。

### その他
- practitioner の VS Code 拡張（2.1.331 の Add Cell to Chat など）の補足は、critic は今回確認していない。使う場合は changelog の原文で確認すること。
- 「Backlog の横断ガントチャート」「料金プラン」は二次情報のみ。候補にしなかったのは正しい。

---

## 統合提案のまとめ

1. **T1 + P1 を 1 枠に**: 「Flutter 3.50 に上げるときに踏むもの（material_ui 移行 + 3.50.1 で直った Android ビルドの罠）」。critic 案では 17 点前後で、Flutter 枠の本命。
2. **CocoaPods（U1）は統合しない**。要点が 3 つを超えるうえ、iOS / Mac 環境が必要になる。最後に 1 枚の言及にとどめるか、別枠のまま。
3. **P2 の Xcode 27 部分は U1 に回し**、P2 は Ubuntu 26.04 中心の「`-latest` 固定戦略」に絞る。

## 人間に確認したいこと（critic からの追加）
- 社内 Flutter アプリの Flutter バージョン（T1 / P1 / U1 共通。前回からの持ち越し）
- 社内で自作の共通 Flutter パッケージを持っているか（T1 の「major version bump」パートが効くか）
- 社内の CI が GitHub Actions か（P2）
- Backlog の API キー発行と、外部 MCP サーバーの利用が社内で許可されているか（P3）

## 確認した URL（critic が今回取得）
- https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/release/breaking-changes/material-ui-and-cupertino-ui.md
- https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/release/whats-new.md
- https://raw.githubusercontent.com/flutter/website/main/sites/docs/src/content/packages-and-plugins/swift-package-manager/for-app-developers.md
- https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md （先頭 10 万字）
- https://github.com/flutter/flutter/tags
- https://github.com/actions/runner-images/issues/14748
- https://github.com/actions/runner-images/issues/15397
- https://github.com/actions/runner-images/issues/15315
- https://github.com/actions/runner-images/issues/15253
- https://github.com/nulab/backlog-mcp-server/releases
- https://github.com/nulab/backlog-mcp-server/releases?page=5
- https://github.com/dart-lang/ai/releases
- https://raw.githubusercontent.com/dart-lang/ai/main/pkgs/skills/CHANGELOG.md
- https://raw.githubusercontent.com/dart-lang/site-www/main/src/content/ai/package-skills.md
- https://github.com/firebase/firebase-tools/releases
- https://github.com/cli/cli/releases/tag/v2.105.0

未確認のまま残っているもの: pub.dev の material_ui / cupertino_ui、`ubuntu-26.04` ラベルが使えるか、CVE-2026-111768 の NVD 登録、firebase.google.com の Crashlytics MCP のドキュメント。
