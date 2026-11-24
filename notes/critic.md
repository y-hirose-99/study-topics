# critic（反論役）レビュー（2026-11-24 JST 実行）

対象: notes/practitioner.md の新規候補 P1〜P3、trend-scout / practitioner の既出候補の更新情報（CocoaPods、Gemini CLI→Antigravity CLI、auto mode 関連、/loop）
照合先: CLAUDE.md（前提・評価基準・根拠ルール・過去テーマ 3 件）、backlog.md（候補 4 件・却下 2 件・フィードバック欄は空）、reports/2026-11-23.md

## 結論サマリ

| ID | 候補 | 判定 | 参考点（新/実/15/デ） | 一言 |
|---|---|---|---|---|
| P1 | VS Code 拡張「Fix with Claude Code」「Resolve Merge Conflicts」 | 条件付き | 5/3/4/4 = 16 | 根拠は一次情報で確認済み。ただし Claude Code 枠が 3 つ目になる。TOP2 と「Claude Code 2 枠目」を争わせるのが条件 |
| P2 | `gh event listen`（gh v2.105.0、preview） | 条件付き（ウェイトリストを通過していなければ却下） | 5/3/4/2 = 14（利用できればデモ 4 で 16） | 根拠は一次情報で確認済み。Claude Code 以外の貴重な候補。ただし preview のウェイトリストを通過していないとデモできない |
| P3 | Gemini Gem→skills 移行・skills 共有 | 却下（今回）。再提出は可 | 4/2/4/2 = 12（減点込み） | 一次情報の本文が未確認（critic 側でも ENOTFOUND）。「当初は英語のみ」で日本語の社内では実務に効かない。移行時期も出典によって食い違う |
| 更新 | CocoaPods trunk read-only（TOP3） | 条件付き（14 点への引き上げに同意） | 4/3/4/3 = 14 | 12/2 の日付と試験運用 11/2〜6 を一次情報で確認した。ただし構成の前提（「3.50 の時期」「SwiftPM を有効にするデモ」）を直す必要がある |
| 更新 | Gemini CLI → Antigravity CLI（保留） | 却下（trend-scout の推奨に同意） | 1/1/4/3 = 9（減点込み） | 告知は 2026-05-19 で約 6 か月前。柱の告知本文も依然として未確認 |
| 更新 | auto mode 関連（TOP2） | スコアは据え置き（15） | 4/3/4/4 = 15 | 2.1.330 / 2.1.331 の項目を確認した。追加するのは 1 行で十分。スライドを増やすと要点 3 つを超える |

**推奨する並び（リードへの提案）**: TOP1（`/code-review`）は据え置き。Claude Code の 2 枠目は、TOP2（auto mode）と P1（VS Code 拡張）のどちらか一方にする。社内で auto mode が組織設定でオフなら P1 に差し替える。TOP3 は CocoaPods（修正版）。P2 は「Claude Code 以外の候補」として、ウェイトリストを通過していることが確認できれば TOP3 と並ぶ候補にする。P3 と Gemini CLI は今回は出さない。

---

## 全体への指摘

1. **Claude Code への偏りが悪化しつつある**: 新規候補 3 件のうち 1 件（P1）は Claude Code。既出の TOP1・TOP2 も Claude Code。P1 を独立した枠として足すと、上位 3 枠のうち 3 枠が Claude Code になる。原因は、一次情報を取得できるホストが code.claude.com と github.com に限られているネットワーク制約で、前回から変わっていない。Claude Code は「最大 2 枠」という前回の方針を維持すべき。
2. **一次情報を確認できる範囲は前回と同じ**: critic 側でも knowledge.workspace.google.com と docs.github.com が ENOTFOUND だった。code.claude.com / github.com / raw.githubusercontent.com は取得できた。
3. **今回は候補を無理に埋めていない点は評価できる**: trend-scout の「新規 0 件」、practitioner の「5 件に満たないので 3 件」は妥当な判断。

---

## P1: VS Code 拡張「Fix with Claude Code」「Resolve Merge Conflicts」（2.1.330 / 2.1.331）

**判定: 条件付き**

- **根拠の確からしさ（critic 側で一次情報を確認）**: https://code.claude.com/docs/en/changelog
  - 2.1.331（November 23, 2026）の [VSCode] Added に次の項目がある。practitioner の要約と一致。
    - "Added "Fix with Claude Code" as a quick fix for errors and warnings and to failed tests in the test peek and the Test Results view, which puts the problem into the chat box"
    - "Added "Resolve Merge Conflicts" to the editor title of a file with merge conflicts and to the Merge Changes group in Source Control"
    - "Added "Add to Chat" to the right-click menu of changed files in the Source Control view"
  - 2.1.331 の [VSCode] Changed「saving a proposed diff to accept it only after you click or type in its proposed (right) side」も一致。
  - 2.1.330（November 20, 2026）の `/fork`、`claudeCode.escapeStopsClaude`、`claudeCode.pinPrompt`、`claudeCode.contextUsageThreshold` も一致。
  - 補足: 2.1.331 には practitioner が挙げていない "Add Cell to Chat"（ノートブックのセル）もある。データ分析をする人がいなければ不要。
- **過去テーマとの重複**:
  - 「Claude を使った Flutter モバイル開発の実践」: 発表の中身は記録がないので推測になるが、IDE 上で Claude にエラーを直させる場面は、この過去テーマの中で既に見せている可能性が高い。「右クリックで渡せるようになった」という差分は、このテーマを見た人には小さく感じられる。デモで Flutter を使わないという practitioner の対策は正しいが、それだけでは見た目の重なりは消えない。
  - 「Copilot → Claude 乗り換え比較」: **ここの重なりが一番大きい**。critic の知識では、GitHub Copilot には VS Code のクイックフィックスで AI に修正させる機能と、AI でマージコンフリクトを解消する機能が以前からある（推測。今回 Copilot 側の一次情報は確認していない）。そのため P1 は「Claude Code が Copilot と同等の機能を備えた」話として受け取られやすく、質疑で比較の話になりやすい。比較の構成にしないことを明記する必要がある。
  - 既出の Claude Code 候補群（`/code-review`、auto mode、`/loop`、`/usage`）とは機能として重ならない。ただし TOP1 の「`--fix` で直す」とメッセージ（AI にコードを直させる）が近い。
- **時間チェック**: 要点は 3 つに収まる（エディタの情報を渡す / デモ 3 種 / diff 保存の変更と設定）。前提知識は軽い。ただしデモを 3 本（型エラー・失敗テスト・コンフリクト）入れると 8 分では厳しい。特にコンフリクト解消はモデルの出力が毎回変わるので、2 本に絞るべき。
- **対象チェック**: VS Code で Claude Code 拡張を使う人だけ。Android Studio / Xcode / JetBrains で作業するモバイル担当には効かない（practitioner も認めている）。インフラ担当が VS Code を使っているかは不明。「参加者の半数以上」は言えないので、実務への効きは 3。
- **新規性の評価**: 機能は 11/23 リリースで新しさは 5。ただし中身は「メニュー項目が増えた」という機能紹介にとどまり、15 分のうち持ち帰れるのは操作 3 つと設定 3 つ。単独の枠としては TOP1 より明らかに薄い。
- **直し方（条件）**:
  1. 独立した枠として足さない。Claude Code の 2 枠目を TOP2（auto mode）と争わせる。P1 は組織設定に依存せず、デモも安定するので、社内で auto mode が使えない場合の代替として P1 のほうが優れている。
  2. Copilot に触れるのは「同じことが Claude Code でもできる」の 1 文まで。比較表は作らない。
  3. デモは「失敗テスト → Fix with Claude Code」と「コンフリクト → Resolve Merge Conflicts」の 2 本に絞る。型エラーのデモは失敗テストと内容が重なるので削る。
  4. diff 保存の挙動変更（右側を触ってから保存しないと accept されない）は、既存の利用者が困る点なので必ず入れる。
  5. Dart / Flutter の診断で「Fix with Claude Code」が出るかは、発表者が 1 回試してから「モバイルの人も使える」と言うこと（現状は推測）。
- **参考点**: 新しさ 5 / 実務 3 / 15 分 4 / デモ 4 = 16

## P2: `gh event listen`（GitHub CLI v2.105.0、GitHub Event Streams preview）

**判定: 条件付き（社内アカウント / org でウェイトリストを通過していなければ却下）**

- **根拠の確からしさ（critic 側で一次情報を確認）**: https://github.com/cli/cli/releases/tag/v2.105.0
  - 日付: ページ上の表示は「18 Nov」（年の表示なし）。リリースからリンクされている告知 URL の日付は 2026-11-17。practitioner の「2026-11-18」は許容範囲。
  - "The new `gh event` command set lets agents, integrations, and scripts wait for pull request changes instead of polling GitHub." と一致。
  - "GitHub Event Streams is in preview" と、ウェイトリスト（https://gh.io/event-streams-signup）を確認した。
  - 既定では最初のイベントで終了し、`--count` / `--follow` を指定できる。例として `--timeout 30m` と `--jq .data.conclusion` が載っている。出力は CloudEvent JSON（`data.api_url` 付き）。`gh run view` / `gh run watch` から check suite イベントを提案する。いずれも一致。
  - 追加で分かったこと: **`gh pr` / `gh api` を使うエージェントに `gh event listen` を提案する**変更も入っている。つまり、Claude Code などのエージェントがポーリングの代わりにこれを使う流れが想定されている。
  - リリースからリンクされている告知（https://github.blog/changelog/2026-11-17-github-event-streams-is-now-in-preview）とドキュメント（https://docs.github.com/en/integrations/how-tos/using-github-event-streams）は、critic 側でも取得できていない（docs.github.com は ENOTFOUND、github.blog は前回から ENOTFOUND）。**利用条件・料金・プランによる制限は未確認**。
- **既出チェック**: 過去テーマとの重複はない。TOP1 に吸収した `/loop` の CI 見張りと目的が重なる。ただし `/loop` は Claude のセッション内でポーリングする仕組みで、`gh event listen` は GitHub 側からイベントを受け取る仕組み。Claude Code に依存しない点は、偏りを直すうえでプラスになる。
- **時間チェック**: 要点 3 つ（ポーリングとの違い / デモ / 応用）に収まる。前提知識は「GitHub Actions と PR が分かる」程度で軽い。
- **対象チェック**: GitHub で PR を出す人全員が対象になりうる。ただし「CI 完了をシェルで待つ」必要がある人は、インフラ担当と自動化好きに限られる（推測）。実務への効きは 3。
- **最大のリスク**: preview のウェイトリストを通過していないと、**デモがまったくできない**。CLAUDE.md の「30 分以内に準備できるデモ」はウェイトリスト次第になる。また preview なので、発表までに仕様が変わる可能性がある。
- **直し方（条件）**:
  1. 発表者（または社内 org）がウェイトリストに申し込み、通過していることを確認する。通過していなければ今回は却下し、GA（一般提供）の時点で再提出する。
  2. docs.github.com のドキュメントで、必要な権限・org 管理者の設定・料金を確認する。
  3. 応用パートは「Claude Code に `gh event listen` で待たせる」にすると、`gh` がエージェントに提案する一次情報の記述と合う。ただし Claude Code の話を膨らませすぎないこと。
  4. TOP1 の `/loop` パートを `gh event listen` に置き換える案もリードは検討してよい（ポーリングせず、セッションへの依存も薄い）。ただし preview なので、TOP1 の安定性を下げるリスクがある。
- **参考点**: 新しさ 5 / 実務 3 / 15 分 4 / デモ 2（ウェイトリストを通過していない前提）= 14。通過を確認できればデモ 4 で 16。

## P3: Gemini の Gem → skills 移行と skills 共有

**判定: 却下（今回）。下記の条件を満たせば再提出は可**

- **根拠の確からしさ**: 3 つの URL はいずれも公式ドメイン（knowledge.workspace.google.com / workspaceupdates.googleblog.com）だが、**本文は未取得で、検索結果の要約だけ**。critic 側でも https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/prepare-for-the-transition-to-skills は ENOTFOUND だった。根拠ルールに従い、新しさと実務への効きを各 1 点減点する（前回の T2 と同じ扱い）。
- **却下理由**:
  1. **「skills は当初英語のみ」**（practitioner が自分で挙げた最大のリスク）: 社内は日本語で業務をしているので、「よく使う Gem を skills に作り直して共有する」という持ち帰りが成り立たない可能性が高い。「実務ですぐ試せる」に反する。
  2. **移行時期が食い違う**: 「自動移行の 30 日以上前に通知」と「Skills will replace Gems in 2027」が両立するのか分からない。2027 年なら今話す緊急性は低い。
  3. **社内のテナントで使えるか分からない**: エディション（Business Starter / Standard / Plus が対象との要約）と展開状況が未確認。前提が成り立たなければデモできない。
  4. **エンジニア向けの勉強会に合わない面がある**: 「エンジニア以外にも効く」は長所だが、技術共有会の参加者はエンジニア。Gem を業務で作り込んでいるエンジニアがどれだけいるかも不明（推測）。
- **長所（再提出のときに生かすべき点）**: 社内の公式 AI（Gemini）に直結する、今回唯一の非 Claude の AI 候補。Claude Code への偏りを直すには価値がある。
- **再提出の条件**: knowledge.workspace.google.com と workspaceupdates.googleblog.com をネットワークで許可し、本文で (a) 移行日程、(b) 日本語対応の時期、(c) 対象エディションを確認する。日本語で使えることが確認できれば、条件付きで再評価する。
- **既出チェック**: Gemini CLI（backlog）とは別製品で、重複はない。
- **参考点**: 新しさ 4（5 から減点）/ 実務 2（3 から減点）/ 15 分 4 / デモ 2 = 12

---

## 既出候補の更新情報

### CocoaPods trunk read-only（TOP3、13 点・条件付き）

**判定: 14 点への引き上げに同意。ただし構成の修正が条件**

- **日付の一次確認（critic 側で再確認）**: https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_posts/2024-11-30-CocoaPods-Specs-Repo.markdown
  - タイトル "CocoaPods Trunk Read-only Plan"、著者 orta。"December 2nd 2026"（"a Wednesday after American Thanksgiving"）に新しい Podspec の受け付けを恒久的に止める、とある。
  - 試験運用は 2026-11-02〜06。"keep all existing builds working"。Specs repo と CDN は "as long as GitHub and jsDelivr continue to exist" 動き続ける。いずれも trend-scout の引用と一致。
  - 補足: 記事本文に「has been updated in May 2025」とある。ファイル名の日付は 2024-11-30 だが、内容は 2025-05 に更新されている。「2024-11-30 付」とだけ書くと誤解を招くので、「2024-11-30 公開、2025-05 更新」と書くこと。
  - 試験運用が実際に行われたかと、12/2 に予定どおり実施されるかの直前告知は、引き続き未確認。
- **URL の訂正**: https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_config.yml に `permalink: /:title` と `url: https://blog.cocoapods.org` がある。Jekyll の `:title` はファイル名の slug から作られるので、公開 URL は `https://blog.cocoapods.org/CocoaPods-Specs-Repo` になる（記事の front matter で permalink を上書きしていない前提。上の取得ではそのような記述はなかった）。末尾のスラッシュの有無は未確認。前回の `/CocoaPods-Trunk/` は誤りなので、backlog とレポートから外すこと。根拠には raw.githubusercontent.com の URL（取得可能な一次情報）を併記するのがよい。
- **「SwiftPM の既定化は 3.50 ではなく 3.44 から」について**:
  - Flutter の stable CHANGELOG（https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md、critic 側で取得）には、**SwiftPM が既定になったことを書いた項目はない**。3.44.2 / 3.44.3 / 3.47.1 / 3.47.2 / 3.47.4 に SwiftPM 関連の不具合修正があるだけ。CHANGELOG はホットフィックスの一覧なので、書かれていないことは否定の根拠にならない。
  - critic 側の検索でも、docs.flutter.dev の SwiftPM ページ、flutter.dev blog「saying goodbye to cocoapods swift package manager is soon the default in flutter」、Classmethod の記事が「3.44 から既定」と要約されている。**3.44 からの可能性は高いが、一次情報の本文は未確認**（docs.flutter.dev は ENOTFOUND。flutter/website の推測パスは 404）。
  - **この点が構成に与える影響（重要）**: 3.44 から既定なら（3.44 のリリースは検索要約で 2026-05 とされる。未確認）、社内アプリが 3.44 以上なら SwiftPM はすでに有効になっている可能性が高い。その場合、前回の構成のデモ「SwiftPM を有効にして iOS ビルドする」は意味がない。デモは「CocoaPods にフォールバックしているプラグインを洗い出す」に変えるべき。社内アプリの Flutter バージョンと、`enable-swift-package-manager: false` で無効にしていないかを確認することが新しい条件になる。
  - レポートの「Flutter 3.50 と Dart 3.14.1 が出た時期に重なり」という書き出しは削る。3.50 は今回の話と関係ない。
- **時間・対象の評価**: 前回から変わらない。モバイル（Flutter）が中心で、Web には関係しない。12/2 を過ぎて開催すると、「何が起きるか」が「何が起きたか」に変わるので、開催日によって構成を調整すること。
- **参考点**: 新しさ 4 / 実務 3（二次情報のみによる減点を解除）/ 15 分 4 / デモ 3 = **14**。デモを上記のとおり直さないなら、デモは 2 として 13 に据え置く。

### Gemini CLI → Antigravity CLI（保留、11 点）

**判定: 却下（trend-scout の推奨に同意）**

- 理由:
  1. **古い**: 移行告知は 2026-05-19（検索要約と TechCrunch の URL の日付による。告知本文は未取得）。約 6 か月前の話で、新しさは 1。
  2. **柱が依然として未確認**: developers.googleblog.com は今回も ENOTFOUND。根拠ルールの減点が残る。
  3. **矛盾がある**: github.com/google-gemini/gemini-cli/releases では v0.70.0（11/19）まで開発が続いていて、移行の記載がない（trend-scout が確認。critic 側では今回は再取得していない）。「Gemini CLI が終わった」とも言い切れず、話の軸が定まらない。
  4. **社内での利用実態が不明**: 前回からの宿題で、人間の回答がまだない。
- 却下欄の理由として推奨する書き方: 「移行告知は 2026-05（対象期間外）、告知本文が未確認、社内での利用実態が不明」。Antigravity CLI を取り上げるなら、社内で使えることを確認したうえで別候補として出し直す。
- **参考点**: 新しさ 1 / 実務 1（2 から減点）/ 15 分 4 / デモ 3 = 9

### auto mode・無人の権限設定（TOP2、15 点）

**判定: スコアは据え置き（15）。追加は 1 行まで**

- critic 側で changelog の原文を確認した。
  - 2.1.331: "Changed what a `PermissionDenied` hook's `retry: true` tells Claude: make the refused call again only if you asked for that call" — 一致。
  - 2.1.331: "Changed in a future release, on or after November 30, 2026: plugin marketplaces in managed `extraKnownMarketplaces` will auto-update by default. To opt one out, set `"autoUpdate": false` on its entry" — 一致。ただしこれは**プラグインの話で、許可確認の話ではない**。TOP2 の要点 3 つから外れる。入れるとしても「managed settings を配る Owner 向けの注意」として 1 行にとどめる。
  - 2.1.330: "Changed more `rm` commands aimed at the root, home, a top-level folder or the working folder to ask for approval; where nobody can answer, they're refused" — 一致。無人実行パートと直接関係するので、入れる価値がある。
  - 補足（practitioner が挙げていない項目）: 2.1.331 [VSCode]「`allowManagedPermissionRulesOnly` が設定されている場合、許可カードは Yes / No だけを表示」。managed settings で配布する話と関係するが、VS Code に限った話なので補足止まり。
- **新しい問題**: P1 の登場で、TOP2 は「Claude Code の 2 枠目」を P1 と争う立場になった。TOP2 は社内で auto mode が組織設定で有効なことが条件のままで、この確認が取れていない。確認が取れなければ P1 に差し替えるべき。

### `/loop`（TOP1 に吸収済み）

- 2.1.331 "Fixed auto mode sometimes refusing Claude's call to stop a /loop, which left the next wake-up to fire" を確認した。practitioner の要約と一致。デモで使うなら 2.1.331 以上にする、という注意は妥当。スコアへの影響はない。
- 2.1.330 [VSCode] "Fixed a forked conversation running the original conversation's `/loop` schedules a second time" もある。P1 の `/fork` と `/loop` を同じデモで使う場合は 2.1.330 以上にすること。

---

## 取得できなかった URL / 未確認のもの

critic 側で取得に失敗したもの:
- https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/prepare-for-the-transition-to-skills — `getaddrinfo ENOTFOUND knowledge.workspace.google.com`（セッションのネットワーク許可外）
- https://docs.github.com/en/integrations/how-tos/using-github-event-streams — `getaddrinfo ENOTFOUND docs.github.com`（同上）
- https://raw.githubusercontent.com/flutter/website/main/src/content/packages-and-plugins/swift-package-manager/for-app-developers.md — HTTP 404（パスを推測したため）

ENOTFOUND が続いたため、critic 側で取得を試していないもの（ほかのエージェントが ENOTFOUND を報告済み、または同じ系統）:
- https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/about-the-transition-to-skills
- https://workspaceupdates.googleblog.com/2026/11/share-skills-add-files-from-Drive-Notebook-Photos-to-skills-Gemini-app.html
- https://github.blog/changelog/2026-11-17-github-event-streams-is-now-in-preview （v2.105.0 のリリースからリンクされている）
- https://docs.flutter.dev/packages-and-plugins/swift-package-manager/for-app-developers （検索結果に出たが未取得）
- https://flutter.dev/blog/saying-goodbye-to-cocoapods-swift-package-manager-is-soon-the-default-in-flutter （検索結果のみ）
- https://blog.cocoapods.org/CocoaPods-Specs-Repo （公開ページ。_config.yml から推定した URL）
- https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/
- https://www.revenuecat.com/docs/sdk-guides/flutter-cocoapods-to-spm-migration
- https://developers.google.com/ads/blog/posts/2026/11/prepare-for-cocoapods-read-only-mode

critic 側で取得・確認できたもの:
- https://code.claude.com/docs/en/changelog （offset 0〜100000。2.1.330 / 2.1.331 の全項目）
- https://github.com/cli/cli/releases/tag/v2.105.0
- https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_posts/2024-11-30-CocoaPods-Specs-Repo.markdown
- https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_config.yml
- https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md （offset 0〜100000。SwiftPM / CocoaPods の記載を検索）

critic 側で再取得していないもの（trend-scout は確認済みとしている）: https://github.com/google-gemini/gemini-cli/releases 、https://raw.githubusercontent.com/CocoaPods/blog.cocoapods.org/master/_posts/2026-02-18-CocoaPods-Trunk-Security-Thanks.markdown

検索結果の要約だけで扱ったもの: 「Flutter 3.44 から SwiftPM が既定」（WebSearch の要約。出典として docs.flutter.dev、flutter.dev blog、dev.classmethod.jp/articles/flutter_cocoapods_swiftpm/ が挙がっている）

ネットワーク許可の提案（前回の提案に追加）: knowledge.workspace.google.com、docs.github.com、flutter.dev
