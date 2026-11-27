# critic レビュー（2026-11-27 JST 実行）

- 対象: notes/trend-scout.md（新規 1・更新 2・参考 1）、notes/practitioner.md（新規 0・更新 2・参考 2）
- 照合: CLAUDE.md（評価基準・根拠ルール・過去テーマ 3 件）、backlog.md（候補 9・却下 9）、reports/2026-11-26.md
- critic が今回自分で取得して確認した URL は末尾にまとめた

## 結論サマリ

| 項目 | 判定 | 一言 |
|---|---|---|
| 新規: Vite v8.4.0（`server.fs.strict: 'extended'`） | **却下（単独枠として）** | 事実は正しい。ただし Web 限定で opt-in の細かい設定。前提説明が重く、デモも未検証。見立ては 12 点 |
| 更新: GitHub MCP Server v2.2.0 の skills 対応 → Skills over MCP | **点は据え置き（14・保留）** | サーバー側だけでなく**クライアント側のフラグ（`MCP_SKILLS=true`）も必要**と PR に明記されている。trend-scout はこれを「テストの条件」としてしか書いていない。Claude Code で受け取れるかは未確認のまま |
| 更新: `/loop` の上限 7 日 → 30 日（2.1.330） | **条件付きで TOP1 に 1 行だけ補足してよい** | 事実は正しい（changelog と scheduled-tasks.md の両方で確認）。ただし対象は**セッション内のタスク（`/loop` と `CronCreate`）だけ**で、cloud scheduled task の話ではない。TOP1 は前回から用語を「cloud scheduled task」に揃えたので、混同しないこと |
| 更新: runner-images #15368 Close | **判断の変更なし。追跡もやめる** | issue を立てた本人が、タイトルを "It outdated" に変えて自分で閉じただけ。メンテナが対応した形跡はない。Announcement でもない |
| 更新: claude-code-action v1.0.289 / v1.0.290 | **判断の変更なし（却下のまま）** | どちらも修正 1 件ずつ |
| 参考 → 再検討: Claude アプリ 11 月のリリースノート | **条件付き（Computer use 1 本に絞って保留で登録）** | 4 機能を束ねる案は不可。**Monthly recap は Team が対象外**（対象プランは Free / Pro / Max / Ultra）。Computer use は Owner が有効にするまで既定で OFF |
| 参考 → 再検討: managed `extraKnownMarketplaces` の自動更新（11/30 以降） | **勉強会ネタではなく周知事項** | 関係するのは managed settings の管理者だけ。期限まであと 3 日なので、勉強会を待たずに人間に確認する |

単独で 1 枠になる新候補は今回もない。TOP3 の入れ替えは不要。

---

## 1. 新規: Vite v8.4.0（`server.fs.strict: 'extended'` ほか）

### 既出チェック
- 過去テーマ 3 件とは重ならない。
- 却下済みの「Vite+ 安定版 / Vinext 1.0」（11/23）とは別物。ただし却下理由の 1 つ「Web 限定」が、この候補にもそのまま当てはまる。create-vite@9.4.0 の `vp` 対応に触れると、却下済みのネタを持ち込むことになる。触れないこと。

### 根拠チェック（一次情報。critic が取得して確認）
- リリースページ: 「26 Nov」に公開。ページに年の表記はない。Features に "add `server.fs.strict: 'extended'`" と "add `environment.addSafeModule`"、Bug Fixes に "register references to safe modules only for `server.fs.strict: 'extended'`" がある。"deprecate `--configLoader=runner`"、"migrate to `dev.sourcemap.css` from `css.devSourcemap`"、"remove `optimizeDeps.rollupOptions`" もある。「Breaking Changes」の節はない。本文に "security" という語はない。**trend-scout の記述と一致する。**
- server-options.md: Type は `boolean | 'extended'`、既定は `true`。"Set to `'extended'` to also apply the access checks to modules `load`ed by plugins except with the IDs starting with `\0`." **一致する。**
- PR #23972: 11/12 にマージ。本文は "applies the checks to more requests" の 1 文だけ。advisory や issue への言及はない。ラベルは `p2-nice-to-have`。**後続の修正 PR #24206 が 11/25（stable の前日）にマージ**されている。出たばかりで、挙動がまだ固まっていない可能性がある。
- `addSafeModule`: server-options.md には記述がない。検索結果の要約によると、vite.dev の Environment Plugins ガイドに「dev 中に `this.environment.addSafeModule(id)` で、`server.fs.allow` の外にあるモジュールの読み込みを許可する」とある。**本文は未取得。**

### 誇張の指摘
- 「Web フロントの全員に関係し、設定 1 行で強化できる」は**言い過ぎ**。
  - 既定（`true`）のままで `/@fs/` 経由の配信はすでに制限されている。`'extended'` が足すのは「plugin の `load` が返すモジュール」への検査だけ。
  - 外部に dev サーバーを公開していない（`server.host` を使っていない）チームにとって、実害は小さい（推測）。
  - 1 行で済むとは限らない。ワークスペース外のファイルを正当に読む plugin は壊れる可能性がある。だから `addSafeModule` が同時に追加されている。「1 行で強化」ではなく「有効にすると plugin 側の対応が要ることがある」が正しい。
- 「セキュリティ修正とは言わない」という trend-scout の注意は妥当。根拠にも脆弱性への言及はない。

### 時間チェック
- 話すには前提が 3 つ要る。(a) `server.fs.allow` とワークスペース root の自動検出、(b) plugin の `resolveId` / `load` フック、(c) `\0` で始まる仮想モジュールの慣習。Vite の plugin を書いたことがない人には、この説明だけで 5〜7 分かかる。
- 残りの改善（manifest に worker chunk、CSS preload の再試行、SSR bundled dev、WASM source phase import）は、互いに関係のない小ネタの羅列になる。要点が 3 つに収まらない。

### 対象チェック
- モバイル（Flutter 中心）とインフラには関係しない。Web の中でも、Vite を使っていて plugin を書く・選ぶ人に限られる。社内で Vite を使っているかも未確認。

### デモチェック
- 「`true` では配信され、`'extended'` では 403」は docs から組み立てた推測で、誰も動かしていない。ワークスペース外を読む plugin をわざわざ書いて見せるのは不自然で、「自分のプロジェクトで何をすればいいか」に繋がらない。

### 結論: 却下（単独枠として）
- 点数の見立て（critic 案）: 新しさ 5 / 実務への効き 2 / 15 分で話せるか 3 / デモ映え 2 = **12**
  - 新しさ: 11/26 の stable なので 5
  - 実務への効き: Web 限定かつ opt-in。社内の Vite 利用も未確認なので 2
  - 15 分: 前提説明が重く、小ネタが散らばるので 3
  - デモ: 未検証で、作為的な最小構成しかないので 2
- 出し直しの条件: 社内の Web プロジェクトが Vite 8 を使っていて、`'extended'` を有効にすると実際に壊れる plugin が見つかった場合。その場合は「Vite 8.4 へ上げるときの確認点（`'extended'`、`optimizeDeps.rollupOptions` の削除、`css.devSourcemap` の移行）」という移行ネタにすれば 14 程度にはなる。

---

## 2. 既出候補への更新

### 2-1. GitHub MCP Server v2.2.0 の MCP skills 対応 → Skills over MCP（14・保留）

#### 根拠チェック（PR #3685 を critic が取得して確認）
- 11/13 にマージ。18 skills。"replaces server instructions entirely"。仕様は "aligned with the beta Skills extension"。protocol revision の明記はない。Go SDK は v1.8.0。**Claude Code への言及はない。** 「対話端末での有効化は検証範囲外（"interactive-terminal activation is not claimed"）」とも書かれている。
- **trend-scout の記述で足りない点**: PR には "Both client `MCP_SKILLS=true` and server `--features=skills`/`--insiders` are required." とある。**クライアント側の opt-in も必須**ということ。`MCP_SKILLS=true` は Copilot CLI 側の設定で、Claude Code に同じ役割の設定があるのか、Claude Code の Skills extension の交渉にこのサーバーが応えるのかは分からない。trend-scout はこれを「動作確認の環境」としてしか書いておらず、「サーバー側は opt-in で返せる」だけが残ると楽観的に読める。
- 副作用もある。有効にすると server instructions が丸ごと skills catalog に置き換わる。
- 仕様リポジトリ名の食い違い: `modelcontextprotocol/ext-skills` も実在する（Beta、base revision 2026-07-28）。前回の根拠の `experimental-ext-skills` と同じ記述。改名・リダイレクトの記載はページにない。どちらを根拠に書いても内容は同じだが、関係は未確認のまま。

#### 判断
- **点は 14 のまま、保留のまま。**
  - 保留理由の本命は「Team 組織の Owner が有効にするまで OFF」。これは何も変わっていない。
  - 「社内 MCP が skills を返すか」は、「GitHub 公式サーバーはフラグを立てれば返す。Claude Code が受け取れるかは未検証」に**文言だけ**更新する。
- 点を上げる条件: GitHub MCP Server をローカルで `--features=skills` 付きで起動し、Claude Code 2.1.332 の `/` メニューに `github:<skill>` が出ることをリハーサルで確認できた場合。そうなれば、デモ候補を個人リポジトリ（olaservo）から公式サーバーに替えられる。参加者全員が GitHub を使うので、実務への効き 2 → 3、デモ映え 3 → 4 で **16 程度**まで上がり得る（推測）。ただし Owner の有効化が前提なのは変わらない。
- 時期: v2.2.0 は 11/16。前回の対象期間より前で、見落としの補足という trend-scout の注記は正しい。

### 2-2. `/loop` と scheduled task の上限 7 日 → 30 日（2.1.330）

#### 根拠チェック
- changelog 2.1.330（November 20, 2026）: "Changed recurring scheduled tasks and `/loop` to run up to 30 days instead of 7 while their session keeps running, and say when they end; tasks made before this release still end at 7 days"。**practitioner の引用と完全に一致する。**
- scheduled-tasks.md "Recurring task expiry": "Recurring tasks automatically expire 30 days after creation. The task fires one final time, then deletes itself." "Before v2.1.330, the limit was 7 days, and recurring tasks created with `CronCreate` on those versions keep it." **一致する。**
- 小さな不整合: 同じページの Limitations のリンクは、まだ `#seven-day-expiry` を指している（docs の更新漏れ）。発表には影響しない。

#### 注意点
- **対象はセッション内のタスク（`/loop` と `CronCreate`）だけ**。cloud scheduled task や Desktop scheduled task の上限の話ではない。TOP1 は前回、用語を「cloud scheduled task」に揃える条件を付けた。そこに「scheduled task が 30 日に」と書くと、cloud 側の話だと誤解される。スライドでは「`/loop`（セッション内のタスク）は最長 30 日」と書くこと。
- 30 日動くのは**セッションが動き続けている間だけ**。ターミナルを閉じれば止まる（Limitations）。「30 日 CI を見張れる」と売り込むのは実態に合わない。CI 見張りの用途なら、数時間で十分。
- "say when they end" の訳は、practitioner の「終了時に通知される」より「いつ終わるかを表示する」のほうが原文に近い。終了時点で通知が来るのか、作成時に終了時期を表示するのかは、原文からは判別できない。断定しないこと。

#### 判断: 条件付きで可
- TOP1 の `/loop` パートに 1 行の補足として入れてよい。点は動かさない（practitioner の判断どおり）。
- 条件: (a)「セッション内のタスク」と明記し、cloud scheduled task と混同しない (b)「セッションが動いている間」を必ず添える (c) 2.1.330 より前に作ったタスクは 7 日のまま。デモ用のタスクは 2.1.332 で作り直す。

### 2-3. runner-images #15368 の Close

- critic の確認結果:
  - 11/19 に DynastyAhri が作成（本文なし）。11/20 に "[Windows-2025-vs2026] Update LLVM to 22" に改題。
  - **11/26 に本人が "It outdated" へ改題し、"completed" で Close**。
  - コメントは Contributor の v-dovchar による "Take a look"（11/19）の 1 件だけ。ラベルは feature request。PR #15370 はページ上で言及されていない。
  - 外部リポジトリ 2 件が 11/24 に「古い Clang を回避」「LLVM 22 を choco で入れる」コミットでこの issue を参照している。**利用者側で回避策を入れている状況で、イメージ側が直った証拠はない。**
- 判断: **変更なし（15 点・次点・条件付きのまま）**。trend-scout の「直ったとは扱わない」は妥当。ただし「閉じた人がメンテナか未確認」は、**作成者本人**と確定できた。Announcement でもない要望 issue なので、今後は追跡対象から外してよい。

### 2-4. claude-code-action v1.0.289 / v1.0.290

- リリースページで確認した内容: v1.0.289 は #2122（ログをプレーンテキストで出し、溜めていたコメントを run step で投稿する）、v1.0.290 は #2121（`GITHUB_OUTPUT` を元に戻す）。v1.0.290 より新しい版はない。ページに年の表記はない。
- 判断: **却下のまま**。どちらも小さな修正で、v1.0.288 の却下理由（社内利用が未確認、デモの見せ場がない）は変わらない。人間への確認事項（claude-code-action を使っているか、`pull-requests: read`）は前回から継続。

---

## 3. 参考扱いだった項目の再検討

### 3-1. Claude アプリのリリースノート（11/20・11/24）

#### 根拠チェック（release-notes.md と Computer use の設定記事を critic が取得して確認）
- **11/20 Computer use（beta）Team / Enterprise**: 新しい Claude 体験の Claude Desktop で使える。Web / Mobile からは「Computer access」がオンのときに使える。組織ごとに段階的に展開。
- 設定記事（中国語版。英語版は未取得）:
  - **既定で OFF**。有効にできるのは Owner / Primary Owner。「組織設定 > 機能」で chat と Cowork の統合をオンにしたうえで、Claude Desktop の Computer use をオンにする。
  - **Team はカスタムロールがないので、有効にすると全員に適用される**。
  - Desktop は macOS 14 以降と Windows。Web / Mobile から使うには、対象のコンピュータで Desktop を開いておく必要がある。
  - **Claude Code と、Cowork オプションで起動したタスクでは動かない**。
  - **Claude と操作対象のアプリの間にサンドボックスがなく**、接続フォルダの制限も適用されない。macOS では「アクセシビリティ」と「画面収録」の権限が要る。
  - 設定の反映には約 1 時間かかる。MDM の `computerUseEnabled` で無効にできる。
- **11/24 Monthly recap の正式版**: 対象プランは **Free / Pro / Max / Ultra で、Team は書かれていない**。trend-scout のメモは「Team / Enterprise 向けの Computer use、Monthly recap の正式版、…」と並べていて、Team で使えるように読める。**社内では使えない可能性が高い。候補から外すこと。**
- **11/24 artifact のアプリ化（beta）**: Mac / Windows。対象プランの記載がない。Team で使えるかは未確認。
- **11/24 connected apps へのファイル送信・保存（beta）**: Team / Enterprise は Owner が有効にした場合だけ使える。

#### 既出チェック
- 過去テーマ・backlog とは重ならない（Claude Code ではなく Claude アプリの機能）。

#### 時間・対象チェック
- 4 機能を束ねると「11 月の新機能紹介」の羅列になる。うち 1 つは Team 対象外、1 つは対象が不明。束ねる形は不可。
- Computer use 1 本なら成立する。
  - 対象は Claude Team を使う全員で、Claude Code の候補よりむしろ広い（モバイル / Web / インフラを問わない）。
  - 要点も 3 つに収まる: 何ができるか → 権限とリスク（サンドボックスなし、アプリの許可・ブロックリスト） → 社内でどう使うか。
  - デモ映えは高い。
- 一番の問題は Owner の有効化。Team では有効にした瞬間に全員に適用され、しかもサンドボックスがない。会社として有効にするかどうかの判断が先で、勉強会のネタとしてはその後になる。

#### 判断: 条件付き（Computer use 1 本に絞り、backlog に「保留」で登録する）
- 形: 「Claude Team で使えるようになった Computer use（beta）: 何を任せてよく、何を任せてはいけないか」
- 点数の見立て（critic 案）: 新しさ 5（11/20 で 1 か月以内）/ 実務への効き 2（有効化されるまで誰も使えない）/ 15 分 4 / デモ映え 2（有効化されるまで、デモは公式の説明をなぞるだけ）= **13**
  - Owner が有効化すれば、実務への効き 3 / デモ映え 4 で **16**（推測）
- 条件:
  1. 人間に確認する: Owner が Computer use を有効にする（または検証用に有効にする）意思があるか。サンドボックスがないので、情報システム側の判断が要る
  2. デモ機は macOS 14 以降、または Windows。業務アカウントでログイン済みのアプリは操作対象にしない（検証用のアプリだけを許可リストに入れる）
  3. Monthly recap はスライドに入れない。artifact のアプリ化は Team で使えると確認できた場合だけ、おまけで 1 枚
  4. 「Claude Code でも使える」と言わない（設定記事に、Claude Code では動かないとある）
- 期間外（11/20・11/24）という点は、backlog で一度も検討していないので、却下の理由にはしない。

### 3-2. Claude Code 2.1.331 の managed `extraKnownMarketplaces` 自動更新（11/30 以降）

- 根拠チェック: changelog 2.1.331（November 23, 2026）に "Changed in a future release, on or after November 30, 2026: plugin marketplaces in managed `extraKnownMarketplaces` will auto-update by default. To opt one out, set `"autoUpdate": false` on its entry"。**practitioner の引用と一致する。**
  - 「11/30 に変わる」ではなく「11/30 以降の、いずれかの将来のリリースで変わる」。日付は確定していない。
- 判断: **勉強会ネタではなく周知事項**（practitioner の判断に同意）
  - 関係するのは managed settings でマーケットプレイスを配っている管理者だけで、参加者の大半には無関係。15 分の内容も、デモの見せ場もない。
  - 一方で、自動更新が既定になると、配っているマーケットプレイスの変更が利用者の端末に審査なしで入る。サプライチェーン上の影響はあり、期限は 3 日後。勉強会の枠を待たずに、レポートの「人間に確認したいこと」の上位に入れるべき。
  - 確認する内容: 社内で managed settings（`extraKnownMarketplaces`）を使っているか。使っているなら、各エントリを自動更新にするか、`"autoUpdate": false` を明示するか。
  - 「許可確認との付き合い方」（TOP3）に 1 枚足す案には**反対**。あの枠は利用者向けの設定の話で、要点がすでに 3 つある。

---

## リードへの申し送り

1. TOP3 はそのままでよい。Vite は却下。
2. TOP1 に `/loop` の 30 日を 1 行だけ足すのは可。「セッション内のタスク」「セッションが動いている間」の 2 つの限定を必ず付けること。cloud scheduled task の話にしない。
3. Skills over MCP は 14・保留のまま。保留理由に「クライアント側の opt-in（GitHub MCP の PR では `MCP_SKILLS=true`）が Claude Code で何に当たるか未確認」を加える。公式サーバーでリハーサルが通れば 16 まで上がり得る。
4. Claude アプリの Computer use を、新規に**保留（13 点、Owner が有効化すれば 16）**として backlog に登録することを提案する。Monthly recap は Team 対象外なので入れない。
5. 周知事項（急ぎ）: managed `extraKnownMarketplaces` の自動更新（11/30 以降）。前回の gh v2.106.0 と並べて、人間への確認事項にする。
6. runner-images #15368 は追跡対象から外す。

## 確認した URL（critic が今回取得したもの）

- https://github.com/vitejs/vite/releases/tag/v8.4.0
- https://raw.githubusercontent.com/vitejs/vite/main/docs/config/server-options.md
- https://github.com/vitejs/vite/pull/23972
- https://github.com/github/github-mcp-server/pull/3685
- https://github.com/modelcontextprotocol/ext-skills
- https://code.claude.com/docs/en/changelog （先頭の 10 万字。2.1.330 / 2.1.331 / 2.1.332 の該当文、2.1.333 がないこと）
- https://code.claude.com/docs/en/scheduled-tasks.md （全文）
- https://github.com/actions/runner-images/issues/15368
- https://github.com/anthropics/claude-code-action/releases
- https://support.claude.com/en/articles/12138966-release-notes.md
- https://support.claude.com/zh-CN/articles/18483812-为-team-和-enterprise-计划设置计算机使用 （Computer use の設定記事。中国語版）

検索結果で見ただけ（本文は未取得）: vite.dev/guide/api-environment-plugins（`addSafeModule` の説明）、support.claude.com/en/articles/16635803（Cowork のブラウザ使用の設定）、code.claude.com/docs/en/computer-use.md

## 取得できなかった・試していない URL

- 英語版の Computer use 設定記事 "Set up computer use for Team and Enterprise plans": 検索で URL が特定できず、未取得。代わりに中国語版（同じ記事 ID 18483812）で確認した。
- vite.dev（`addSafeModule` の公式ガイド）: 試していない。説明は検索結果の要約のみ。
- https://github.com/vitejs/vite/releases （create-vite@9.4.0、v8.4.0-beta.2 の日付）: critic は再確認していない。trend-scout の確認のまま。
- https://github.com/github/github-mcp-server/releases/tag/v2.2.0 と v2.3.0: critic は再確認していない（PR #3685 で中身は確認した）。
- changelog の 10 万字以降は読んでいない（最新 3 版の範囲は読んだ）。
- github.blog / nulab.com / modelcontextprotocol.io: これまで ENOTFOUND だったので試していない。
- 補足: claude-code-action のリリースページには年の表記がない。v1.0.288〜v1.0.290 の年は前後の版から推定したもの（判断には影響しない）。
