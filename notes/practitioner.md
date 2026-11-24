# practitioner（実務適用）調査メモ

- 調査日: 2026-11-24（JST）
- 対象期間: 前回レポート（reports/2026-11-23.md、対象 2026-10-20〜2026-11-23）以降。前回から 1 日しか経っていないため、前回の調査で拾えていなかった 11 月中旬以降の実務ネタも含める
- 観点: 「明日から業務で試せるか」。新しさより持ち帰りやすさを優先
- 重複チェック: CLAUDE.md の過去テーマ 3 件、backlog.md の候補・却下（/code-review、auto mode・無人権限設定、CocoaPods trunk、Gemini CLI→Antigravity CLI、Vite+/Vinext、/usage・idle compaction、/loop）と照合済み
- 結論: 新規候補は 3 件（うち 1 件は一次情報の本文を確認できておらず条件付き）。5 件には満たないため、無理に埋めていない

## 注意（ネットワーク制約）

前回と同じく nulab.com / workspaceupdates.googleblog.com / github.blog は DNS 解決できなかった（ENOTFOUND、セッションのネットワーク許可外と推測）。
一次情報を本文まで確認できたのは code.claude.com と github.com（cli/cli の Releases）だけ。
Google Workspace 系の候補は、公式 URL は検索結果に出ているものの本文は未確認（検索結果の要約のみ）。

---

## 候補 1: VS Code の中で Claude Code を「エラー → 修正」「コンフリクト解消」に直結させる（2.1.330 / 2.1.331 の VS Code 拡張の新機能）

- **タイトル案**: 「Fix with Claude Code」と「Resolve Merge Conflicts」— VS Code 拡張の右クリックだけで Claude に仕事を渡す
- **概要**: 2.1.331（2026-11-23）で、VS Code のエラー・警告のクイックフィックスと、失敗したテスト（test peek / Test Results ビュー）に「Fix with Claude Code」が追加され、問題の内容がそのままチャット欄に入るようになった。同じ版で、コンフリクトのあるファイルのタイトルバーと Source Control の Merge Changes に「Resolve Merge Conflicts」、変更ファイルの右クリックに「Add to Chat」が付いた。2.1.330（2026-11-20）では `/fork` や、Escape で止めない `claudeCode.escapeStopsClaude` などの設定が増えている。
- **参加者が持ち帰れるもの**: エラー・失敗テスト・コンフリクトを「コピペせずに」Claude に渡す 3 つの右クリック操作と、おすすめ設定 3 つ（`escapeStopsClaude` / `pinPrompt` / `contextUsageThreshold`）
- **15 分構成案**:
  1. （3 分）ターミナル版との違い: VS Code 拡張だとエディタの情報（診断・テスト結果・Source Control）をそのまま渡せる
  2. （8 分）デモ: (a) 型エラーのクイックフィックスから「Fix with Claude Code」 (b) 失敗テストの Test Results から修正 (c) わざと作ったマージコンフリクトを「Resolve Merge Conflicts」で解消し、差分を確認して Accept
  3. （4 分）2.1.331 で diff の保存動作が変わった点（右側の提案を触ってから保存しないと accept されない）と、おすすめ設定の紹介
- **30 分以内で準備できるデモ案**: Web（TypeScript）の小さなリポジトリに、型エラー 1 つ、失敗するテスト 1 つ、2 ブランチで同じ行を変えたコンフリクトを用意する。拡張機能を 2.1.331 以上に更新しておく
- **想定対象者**: VS Code を使う Web / モバイル（Flutter を VS Code で書いている人）/ インフラ（Terraform など）
- **根拠 URL（取得・確認済み）**:
  - https://code.claude.com/docs/en/changelog （2.1.330 = 2026-11-20、2.1.331 = 2026-11-23 の [VSCode] 項目）
- **重複チェック**: backlog の /code-review・auto mode・/loop・/usage とは別機能。過去テーマ「Claude を使った Flutter モバイル開発の実践」と見た目が重ならないよう、デモは Flutter 以外のリポジトリにする
- **推測部分**: 「Fix with Claude Code」が Dart / Flutter の Analyzer 診断や Flutter のテストビューでも同様に出るかは未確認（VS Code の標準の診断・テスト API に乗っていれば出ると推測）。Android Studio 利用者には効かない

---

## 候補 2: `gh event listen` で「CI 終わった？レビュー来た？」のポーリングをやめる（GitHub CLI v2.105.0、preview）

- **タイトル案**: `gh event listen` — PR のレビュー・CI 完了を「待つ」シェルスクリプトとエージェント
- **概要**: GitHub CLI v2.105.0（2026-11-18）で、GitHub Event Streams（preview）を使う `gh event` コマンド群が追加された。`gh event listen` で PR に対する特定のイベント（レビュー提出、check suite 完了、PR の状態・head の変更、マージコンフリクト）をポーリングなしで待てる。既定では最初のイベントで終了し、`--count` / `--follow` / タイムアウト / `--jq` を指定できる。出力は CloudEvent 形式の JSON。`gh run view` / `gh run watch` からも check suite イベントが提案される。
- **参加者が持ち帰れるもの**: 「PR の CI が終わったら通知 / 次の処理へ」をポーリングなしで書く 1 行のシェルレシピ
- **15 分構成案**:
  1. （3 分）`gh run watch` や `sleep` ループのポーリングとの違い、preview の制約（ウェイトリスト）
  2. （8 分）デモ: PR を push → 別ターミナルで `gh event listen`（check suite 完了を待つ）→ CI 完了の瞬間に JSON が出て、`--jq` で結論だけ取り出して `notify-send` や Google Chat の Webhook に渡す
  3. （4 分）応用: レビュー提出を待って Claude Code などのエージェントに次の作業を渡す、`gh event subscription list` で購読を管理する
- **30 分以内で準備できるデモ案**: 数十秒で終わる GitHub Actions を持つテスト用リポジトリで PR を作り、`gh` を v2.105.0 以上に更新して `gh event listen` を実行する。**ただし preview のウェイトリストに通っていることが前提**
- **想定対象者**: GitHub で PR を出す全員、CI を整備するインフラ担当
- **根拠 URL（取得・確認済み）**:
  - https://github.com/cli/cli/releases/tag/v2.105.0 （2026-11-18）
  - https://github.com/cli/cli/releases （v2.103.0 の expiring OAuth トークン preview、v2.104.0 の `gh repo delete` の確認強化も記載）
- **重複チェック**: backlog の `/loop` で CI を見張る案（TOP1 に吸収済み）と目的が近い。`/loop` は Claude セッション内のポーリング、こちらは GitHub 側からのイベント通知で、仕組みも持ち帰るものも違う。ただしリードの判断で「CI 見張り」枠として TOP1 と統合する余地はある
- **推測部分・条件**:
  - リリースノートは「GitHub Event Streams is in preview」とし、ウェイトリストへのリンクがある。**社内アカウント / org で使えるかは未確認**で、使えなければデモ不可
  - 有効化手順・必要な権限・料金は、リリースノートからリンクされた Event Streams のドキュメント（docs.github.com / github.blog と推測。未取得）を確認する必要がある
  - リリースノートの全文は引用制限のため要約で確認した。具体的なフラグ名（イベント種別の指定方法など）は `gh event listen --help` で確認すること

---

## 候補 3（条件付き）: Gem から skills への移行に備える — Gemini アプリの skills 共有と Drive ファイル添付

- **タイトル案**: 社内の Gem、来年どうなる？ Gemini の skills への移行準備と、チームで skills を共有する方法
- **概要**: Google Workspace の管理者ヘルプ「Prepare for the transition to skills」（最終更新 2026-11-09）で、Gemini アプリの Gem が skills に移行する方針と、管理者・作成者向けの準備手順（不要な Gem の削除、重要な Gem の手動での作り直し、Workspace Studio の「Ask a Gem」ステップの置き換え）が示されている。2026-11-16 の Workspace Updates では、Gemini アプリの skills を View / Edit 権限で共有できるようになり、Drive・Gemini Notebook・Google Photos のファイルを skills に添付できるようになったとされる。
- **参加者が持ち帰れるもの**: 自分が作った Gem の棚卸しチェックリストと、よく使う Gem を skills に作り直して同僚に共有する手順
- **15 分構成案**:
  1. （4 分）Gem と skills の違い、移行のスケジュール（自動移行の 30 日以上前に管理者へ通知、とされる）
  2. （7 分）デモ: よく使う Gem（例: Backlog 課題の文面整形、議事録要約）を skills として作り直し → Drive の社内ガイドラインを添付 → 同僚に View 権限で共有
  3. （4 分）注意点: 当初は英語のみ、Gemini アプリと Workspace アプリの skills は同期しない、Drive ファイルは受け取り側にもアクセス権が必要
- **30 分以内で準備できるデモ案**: 既存の Gem 1 つを skills に移し、Drive のドキュメントを 1 つ添付して別アカウントに共有する。**社内テナントで skills が展開済みであることが前提**
- **想定対象者**: Gemini を使う全員（エンジニア以外にも効く）。Workspace 管理者
- **根拠 URL（いずれも本文は未取得。検索結果の要約のみ）**:
  - https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/prepare-for-the-transition-to-skills （2026-11-09 更新）
  - https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/about-the-transition-to-skills
  - https://workspaceupdates.googleblog.com/2026/11/share-skills-add-files-from-Drive-Notebook-Photos-to-skills-Gemini-app.html （2026-11-16）
- **重複チェック**: backlog の「Gemini CLI → Antigravity CLI」とは別製品（こちらは Gemini アプリ / Workspace）
- **推測部分・条件**:
  - 上の URL は公式ドメインだが、本文を取得できていない。根拠ルール上は「一次情報が未確認」として、リード側で新しさ・実務への効きの減点を検討してほしい
  - **skills は当初英語のみ**とされている。日本語運用の社内で実用になるかは要確認で、ここが最大のリスク
  - 「Skills will replace Gems in 2027」とする記述もあり、移行時期は出典によって食い違う。発表前に管理者ヘルプで最新日付を確認すること
  - 社内の Workspace エディションで skills が使えるか（検索要約では Workspace 側の skills は Business Starter / Standard / Plus が対象）は未確認

---

## 既出候補の更新情報

| 既出候補 | 新情報 | 根拠 | 評価への影響（推測） |
|---|---|---|---|
| `/code-review`（TOP1） | 2.1.330 / 2.1.331 に `/code-review` 関連の変更なし | https://code.claude.com/docs/en/changelog | 変化なし |
| `/code-review` に吸収した `/loop` | 2.1.331（11/23）で「auto mode が `/loop` の停止を拒否し、次の起動が走ってしまう」不具合が修正された。デモで auto mode + `/loop` を使う場合は 2.1.331 以上を使うこと | 同上 | デモの安定性がわずかに上がる |
| auto mode・無人権限設定（TOP2） | 2.1.330（11/20）で、ルート・ホーム・トップレベル・作業フォルダを狙う `rm` の承認必須化がさらに拡大され、「応答できる人がいない場合は拒否」と明記された。2.1.331（11/23）では `PermissionDenied` hook の `retry: true` の意味が変わり、managed の `extraKnownMarketplaces` のプラグインマーケットプレイスが 11/30 以降の版で既定で自動更新になる予定（`"autoUpdate": false` で除外） | 同上 | 無人実行の話に「11/30 以降の自動更新」を 1 枚追加できる。スコアは据え置きと推測 |
| CocoaPods trunk read-only（TOP3） | 検索要約では、Flutter 公式ドキュメント（SwiftPM for app developers）にも「CocoaPods registry は 2026-12-02 に read-only」「将来 SwiftPM を無効化できなくなる」と書かれているとされる。また SwiftPM の既定化は **3.50 ではなく 3.44 から**とされている。RevenueCat の移行ガイドも同じ日付 | https://docs.flutter.dev/packages-and-plugins/swift-package-manager/for-app-developers （未取得・検索要約）、https://www.revenuecat.com/docs/sdk-guides/flutter-cocoapods-to-spm-migration （未取得・検索要約） | 日付の裏付けが 1 段強くなるが、一次情報は依然として本文未確認。レポートの「3.50 の時期に」という書き方は「3.44 から既定」に直すべき可能性がある |
| Gemini CLI → Antigravity CLI（保留） | 新情報なし（今回は追加調査していない） | — | — |

## 取得できなかった URL

| URL | エラー |
|---|---|
| https://nulab.com/whats-new/backlog/ | getaddrinfo ENOTFOUND nulab.com（ネットワーク許可外と推測） |
| https://workspaceupdates.googleblog.com/2026/11/ | getaddrinfo ENOTFOUND workspaceupdates.googleblog.com |
| https://github.blog/changelog/month/11-2026/ | getaddrinfo ENOTFOUND github.blog |

上記 3 ホストが ENOTFOUND で失敗したため、ツールの指示に従い、それ以外の新しいホスト（knowledge.workspace.google.com、docs.flutter.dev、www.revenuecat.com、docs.github.com など）は取得を試していない。候補 3 と CocoaPods の更新情報は検索結果の要約に基づく。

## 未確認の手がかり（候補化していない）

- Backlog: ITmedia（2026-07-24）によると、プロジェクト横断ガントチャートが「2026 年秋ごろ」リリース予定。2026-12-31 に現行プランの新規契約が終わり、2027-01-01 から新プラン（Economy / Business / Professional）に移る。11 月にリリースされたかは nulab.com が取得できず未確認。リリースされていれば Backlog 利用者向けの実務ネタになり得る（出典: https://atmarkit.itmedia.co.jp/ait/articles/2607/24/news048.html 、未取得）
- Google Workspace: Gemini in Slides の複数スライド生成の上限緩和が少なくとも 2026-12-01 まで、AI Inbox の有料 Workspace への拡大（いずれも検索要約のみ）
- GitHub CLI v2.103.0（2026-10-21）の expiring / auto-refreshing OAuth トークン（preview）: 無人実行のトークン管理の話として、TOP2 の補足に使える可能性
