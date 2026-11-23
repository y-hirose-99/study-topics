# critic（反論役）レビュー（2026-11-23 JST 実行）

対象: notes/trend-scout.md（T1〜T3）、notes/practitioner.md（P1〜P5）の計 8 件
照合先: CLAUDE.md（前提・評価基準・根拠ルール・過去テーマ 3 件）、backlog.md（候補・採用・却下・フィードバックすべて空）

## 結論サマリ

| ID | 候補 | 判定 | 参考点（新/実/15/デ） | 一言 |
|---|---|---|---|---|
| T1 | Flutter 3.50 / Dart 3.14 と CocoaPods → SwiftPM | 条件付き | 4/3/4/3 = 14 | Flutter 3.50 のリリースは確認できた。ただし「CocoaPods 期限で iOS ビルドが通らなくなる」は言い過ぎ。テーマを絞る必要あり |
| T2 | Gemini CLI → Antigravity CLI 移行 | 条件付き（確認できなければ却下） | 3/3/4/3 = 13（未確認につき減点込み） | 核となる「移行」が未確認。確認できた小機能だけでは弱い |
| T3 | Vite+ 安定版 / Vinext 1.0 | 却下 | 1/2/3/2 = 8 | 対象期間外、帰属が誤っている可能性、本番非推奨、Web 限定 |
| P1 | `/code-review` 重要度ラベル + `--fix` | 採用可（修正点あり） | 5/4/4/5 = 18 | 根拠は一次情報で確認済み。Team プランでマネージド版も使える |
| P2 | `/loop` と `loop.md` で PR・CI を見張る | 条件付き（P1 に吸収を推奨） | 5/3/3/3 = 14 | セッションを開いている間しか動かない。単独で話すには弱い |
| P3 | `/auto-mode-setup` と `auto-mode critique` | 条件付き（P4 と統合） | 4/3/3/3 = 13 | Team で使えるのは確認済み。ただし設定の共有方法の説明が間違っている |
| P4 | 無人実行の権限設定まとめ | 条件付き（P3 と統合） | 5/2/3/4 = 14 | 根拠はすべて確認済み。単独では changelog の読み上げになり、対象も狭い |
| P5 | `/usage` 改善と idle compaction | 却下（単独枠として） | 4/3/4/2 = 13 | 中身が薄く、デモ結果も予測できない。fast mode 終了（11/30）は発表時点で終わっている可能性が高い |

推奨する並び: P1 採用 / P3+P4 統合で条件付き / T1 を絞って条件付き（モバイル枠）/ T2 は確認待ち。
Claude Code 関連は最大 2 枠（P1 と P3+P4）にする。P2・P5 は単独では出さない。

---

## 全体への指摘

1. **Claude Code に偏りすぎ**: practitioner の 5 件はすべて Claude Code。原因は「一次情報が取れたホストが code.claude.com だけ」というネットワーク制約で、ネタとしての価値で選ばれたわけではない。CLAUDE.md の好まれる傾向（AI 活用の具体例）には合うが、5 件並べると「Claude Code changelog 解説会」になる。過去テーマ 3 件のうち 2 件はすでに Claude 関連で、AI ツール寄りに飽和するおそれがある（backlog のフィードバック欄は空なので、飽和は推測）。
2. **Team プランで使えるか（今回確認した結果）**:
   - マネージド版 Code Review: Team / Enterprise 向け。Owner が有効化し、レビューごとに費用がかかる（code-review.md の Availability に記載）。practitioner が「分からない」としていた点は解消。
   - `/code-review`（ローカル版）: どのプランでも使える。
   - `/auto-mode-setup`: Pro / Max / Ultra / Team / Enterprise で使える（Team は v2.1.228 以上）。ただし auto mode 自体は Team / Enterprise で組織単位のオン・オフがあり、社内で有効かどうかは未確認。
   - `/loop`、各 permissions 設定、`/usage`: プランの制限は今回確認したページに書かれていない（推測では制限なし）。
3. **trend-scout は「一次情報なし」で減点が必要**: 根拠ルールに従い T1〜T3 は「新しさ」「実務への効き」を各 1 点減点して採点するのが妥当。ただし今回、github.com / raw.githubusercontent.com 経由で一部を critic 側で確認できた（下記）。

---

## T1: Flutter 3.50 / Dart 3.14 と CocoaPods → SwiftPM 移行

**判定: 条件付き**

- **根拠の確からしさ（critic 側で確認）**
  - Flutter stable の CHANGELOG（https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md）に「Flutter 3.50 Changes」として 3.50.0 と 3.50.1 がある。**リリースは事実**。ただし日付と、3.50 での SwiftPM / CocoaPods の扱いは書かれていない。
  - Dart stable の CHANGELOG（https://raw.githubusercontent.com/dart-lang/sdk/stable/CHANGELOG.md）では 3.14.1 が 2026-11-17 リリース。3.14.0 は「Unreleased」のまま（ページ内で矛盾している）。trend-scout の「3.14 は 2026-11-11」は**確認できなかった**。
  - CocoaPods: 検索結果の要約によると、公式ブログ（blog.cocoapods.org。本文は取得していない）の計画では **trunk の read-only 化は 2026-12-02**。**既存のビルドは壊れない**が、trunk への新しい pod・新しいバージョンの追加が止まる。trend-scout の「CocoaPods が更新受付を止めるので iOS ビルドの移行が実務上の課題」は**誇張**。正確には「CocoaPods 経由で配布されているネイティブ依存が今後更新されなくなる。プラグインが SwiftPM に対応しているかがカギ」。
  - 3.50 の新機能と、SwiftPM がデフォルトになったかどうかは、引き続き未確認（docs.flutter.dev が取得できない）。
- **既出チェック**: 過去テーマ「Claude を使った Flutter モバイル開発の実践」とは観点が違う（AI ではなくビルド基盤の話）。重複なし。
- **時間チェック**: 「3.50 の新機能」と「SwiftPM 移行」の 2 本立てだと 15 分を超える。3.50 の新機能は確認もできていないので削る。
- **対象チェック**: モバイル（Flutter）が主。インフラは CI の macOS ランナーの範囲だけ。Web には関係ない。参加者の半数以上に届くかは微妙なので、実務への効きは 3。
- **直し方**:
  1. タイトルを「CocoaPods trunk 12/2 read-only: 社内 Flutter アプリで何が起きて何が起きないか」に絞る。
  2. 結論として「既存ビルドは通る」を先に言い、誤った危機感をあおらない。
  3. デモは、社内アプリの依存プラグインの SwiftPM 対応状況を一覧にする手順と、SwiftPM を有効にした iOS ビルド。Mac と Xcode が要るのでリハーサル必須。過去版の CHANGELOG に SwiftPM 有効時のビルド不具合（3.44.2 / 3.47.2）が載っているので、デモ失敗のリスクがある。
  4. 発表前に docs.flutter.dev の SwiftPM ページと blog.cocoapods.org の本文を取得して裏を取る。
- **参考点**: 新しさ 4 / 実務 3 / 15 分 4 / デモ 3 = 14

## T2: Gemini CLI → Antigravity CLI 移行と 11 月の更新

**判定: 条件付き（移行告知の本文が確認できなければ却下）**

- **根拠の確からしさ（critic 側で確認）**
  - https://github.com/google-gemini/gemini-cli/releases を取得できた。v0.68.0（11/10）に「add extension validation command」（#29980）、v0.69.0（11/19。preview は 11/10）に「/stats heatmap subcommand for daily token usage」（#30135）がある。trend-scout が「v0.69 は 11 月中旬」と書いた点はおおむね正しい。ただし `gemini extensions validate` というコマンド名そのものは確認できていない。
  - **Antigravity CLI への言及はリリースページに一切ない**。移行告知（developers.googleblog.com）は DNS エラーで取得できず、geminicli.com も critic 側で ENOTFOUND。候補の柱が**完全に未確認**。
- **既出チェック**: 「Copilot → Claude の乗り換え比較」と同じ型（AI ツールの乗り換え話）になりやすい。比較の構成は避けること。
- **時間チェック**: 移行の中身が分かれば 15 分に収まる。分からないままなら話すことがない。
- **対象チェック**: 「社内の公式 AI は Gemini」だが、それは Workspace の Gemini のこと。開発者が Gemini CLI を業務で使っているかは CLAUDE.md からは分からない（推測）。全員に関係するという主張は弱い。
- **却下ライン**: 確認できた extension validation と `/stats` heatmap だけでは 15 分のネタにならない（マイナーアップデートの紹介どまり）。
- **直し方**: developers.googleblog.com を許可して移行告知の本文（何が変わり、いつまでに、何をすべきか）を確認する。「Gemini CLI 利用者が今月中にやること」として 3 点に絞る。あわせて社内での Gemini CLI 利用実態を人間に確認する。
- **参考点**: 新しさ 3（未確認につき減点込み）/ 実務 3 / 15 分 4 / デモ 3 = 13

## T3: Vite+ 安定版（Cloudflare）/ Vinext 1.0

**判定: 却下**

- 理由:
  1. **新しさ**: Vinext 1.0 は 2026-09-28 で対象期間（10/20 以降）の外。trend-scout 自身も認めている。
  2. **帰属が誤っている可能性**: 「Cloudflare Vite+」という表現はタイトルからの推測。critic の知識では Vite+ は VoidZero のツールチェーンで、Cloudflare の製品ではない（推測。一次情報は未確認）。InfoQ の記事も未取得。
  3. **成熟度**: https://github.com/cloudflare/vinext の README は「Under active development」「not yet a drop-in replacement for every application or production workload」と書いている。リリース欄は空で、1.0 であることも確認できなかった。「Next.js を Vercel 以外に載せる現実的な選択肢」は言い過ぎ。
  4. **対象**: Web チームのうち Next.js を使っている人だけ。社内で Next.js を使っているかどうかの情報もない。モバイルとインフラにはほぼ関係しない。
  5. **デモ**: Cloudflare アカウントと Workers の準備が要り、「ビルド時間を比べる」は 30 分で信頼できる数字が出ない。
- **参考点**: 新しさ 1 / 実務 2（二次情報のみで減点済み）/ 15 分 3 / デモ 2 = 8

---

## P1: `/code-review` の重要度ラベルと `--fix`

**判定: 採用可（以下の修正をすること）**

- **根拠の確からしさ（critic 側で再確認、すべて一次情報）**
  - 2.1.328（11/19）: 「mark each finding Important, Pre-existing or Nit ... end each summary with a suggested fix」「report only bugs and CLAUDE.md violations; run `/simplify` for ...」。一致。
  - 2.1.329（11/19）: 引数なしの `/code-review` で、ブランチ全体をデフォルトブランチと比べてレビューするようになった。`<pr number>` を指定すると `gh pr diff` で読むよう修正された。**practitioner のメモには入っていないが、デモに直接関係する**。
  - 2.1.304 のレビューレベル（Quick/Standard/Deep）、2.1.307 の `@claude review stop`、2.1.323 の CSV ダウンロード: いずれも一致。
  - マネージド版は **Team / Enterprise で使える**（code-review.md）。Owner が有効化し、利用ごとに費用がかかる。
  - `--fix` と `--comment` の詳しい挙動（code-review-local.md）は critic 側では再取得していない（practitioner は確認済み）。
- **修正すべき点**
  1. 「Important 件数で CI を止める `gh api` + jq」は**マネージド版の check run（`bughunter-severity` 行）が前提**で、ローカル版 `/code-review` には当てはまらない。持ち帰りとしてまとめて書くと誤解を招く。マネージド版を社内で有効化していないなら、この部分は削るか「有効化した場合」と明記する。
  2. マネージド版の check run は「never concludes as a failure」。CI を止めたいなら自前のスクリプトが必要、と正確に伝える。
  3. デモ用リポジトリに **Flutter サンプルを使わない**こと。過去テーマ「Claude を使った Flutter モバイル開発の実践」と見た目が重なる。Web かインフラのリポジトリにする。
  4. 「Copilot のレビューと比べてどうか」という構成にしない（過去テーマ「乗り換え比較」と重なる）。
- **既出チェック**: 過去テーマとの実質的な重複は小さい（Flutter 開発の実践はおそらく実装中心、乗り換え比較はツール選定。どちらもレビュー運用そのものではないと推測）。
- **時間チェック**: 要点 3 つ（ローカル版とマネージド版の違い / デモ / チーム運用）に収まる。
- **対象チェック**: GitHub で PR を出す全員が対象。参加者の半数以上に届く。
- **参考点**: 新しさ 5 / 実務 4 / 15 分 4 / デモ 5 = 18

## P2: `/loop` と `loop.md` で PR・CI を見張る

**判定: 条件付き（単独枠ではなく P1 の後半に 3 分ほど入れることを推奨）**

- **根拠の確からしさ**:
  - 30 日への延長は **2.1.330（11/20）** の 1 項目で、「2.1.326〜330」ではない。条件として「while their session keeps running」と「tasks made before this release still end at 7 days」が付いている。practitioner のメモはこの条件を落としている。
  - 2.1.329 の `/tasks` 集約と `x` で止める操作、2.1.319 の「Earlier in this session」行: いずれも一致。
  - 2.1.329 で「旧バージョンで保存した定期タスクは、新しくサインインしたセッションではほぼ再設定が必要」という互換性の注意も入っている。発表で触れるべき。
- **弱点**:
  1. セッションを開いている間しか動かない。「Claude に任せる」と言うには、端末を開きっぱなしという制約が大きい。
  2. 3 種類の定期実行（クラウド / Desktop / `/loop`）の使い分けを説明するだけで 3 分以上かかり、前提説明が重い。
  3. デモが CI の実行時間とモデルの判断に左右され、15 分の枠で安定しない。
- **既出チェック**: 重複なし。
- **対象チェック**: PR を出す人。インフラの「デプロイ完了待ち」は根拠がなく、こじつけ気味。
- **直し方**: P1 を「PR を出す前は `/code-review`、出した後は `/loop` で CI を見張る」の流れにして、後半に 3 分のおまけとして入れる。単独で出すなら、3 種類の比較を削り、`loop.md` のテンプレートとセッション依存の制約だけに絞る。
- **参考点**: 新しさ 5 / 実務 3 / 15 分 3 / デモ 3 = 14

## P3: `/auto-mode-setup` と `claude auto-mode critique`

**判定: 条件付き（P4 と統合して 1 枠にする）**

- **根拠の確からしさ**:
  - auto-mode-config.md で `/auto-mode-setup` と `claude auto-mode critique` / `config` / `defaults` / `reset` が存在することを確認した。Team プランは v2.1.228 以上で使える。2.1.304 の記述（Bedrock / Vertex / telemetry 無効環境でも使える）も一致。2.1.328 の「recent auto mode denials を見るよう修正」も一致。
  - 2.1.330 の「`rm` の承認必須化を広げた」は確認済み。ただし 2.1.326 側に該当項目は見当たらなかった（critic が見た範囲では 2.1.326 は該当なし）。
- **誤りがある箇所**:
  - `/auto-mode-setup` が書き込むのは `~/.claude/settings.json`（個人設定）。**分類器はプロジェクトの `.claude/settings.json` から `autoMode` を読まない**（ドキュメントに明記）。「`autoMode.environment` の書き方テンプレートをチームに配る」なら、managed settings（Owner が配布）経由になる。持ち帰りとして「リポジトリに置けば共有できる」と誤解されないようにすること。
- **弱点**:
  1. 前提知識が重い。auto mode とは何か、分類器、ask / deny の評価順序。参加者のうち Claude Code の auto mode を日常的に使っている人の割合は不明で、前提説明だけで時間切れになる危険がある。
  2. Team プランでは組織単位で auto mode をオフにできる。社内で有効になっていなければデモ自体が成立しない。発表前に確認が必須。
  3. 「pastebin 風のドメインがブロックされる」デモは分類器の判定に依存し、再現性が保証されない。
- **既出チェック**: 重複なし。
- **参考点**: 新しさ 4 / 実務 3 / 15 分 3 / デモ 3 = 13

## P4: 無人実行・CI で Claude Code を安全に動かす権限設定

**判定: 条件付き（P3 と統合）**

- **根拠の確からしさ**: changelog の 5 項目はすべて critic 側で原文を確認した。版と日付も一致。
  - 2.1.314（10/30）`blockWritesOutsideWorkingDirectories`: Write / Edit / NotebookEdit が対象。Bash 経由の書き込みは対象外と読める。デモの「`/tmp` 以外の範囲外書き込みが拒否される」は、`claude -p` が Bash で書いた場合には拒否されない可能性がある（推測）。settings-reference での確認が必須。
  - 2.1.319 `denyInsteadOfPrompting`、2.1.318 `loginExpiresAt` / `credential_expires_at`（claude.ai サインインの期限）、2.1.328 `mcp_tool` hook の `onFailure: block`、2.1.327 `mcp add-json <name> -`: いずれも一致。
- **弱点**:
  1. 5 つの設定を並べた構成は changelog の読み上げで、要点 3 つ以内に収まっていない。
  2. 対象が狭い。Claude Code を無人実行・CI で回している人は、社内ではインフラや一部の人に限られると推測。実務への効きは 2。
  3. `credential_expires_at` は claude.ai サインインの話で、API キーで動く CI には関係しない。対象をさらに狭めている。
- **既出チェック**: 重複なし。
- **参考点**: 新しさ 5 / 実務 2 / 15 分 3 / デモ 4 = 14

### P3 + P4 統合案

タイトル案は「Claude Code の許可確認との付き合い方: 対話は auto mode、無人は deny 設定」。要点は次の 3 つ。
1. 対話: `/auto-mode-setup` で信頼する範囲を教え、`permissions.ask` で push と PR 作成だけは人が確認する
2. 無人: `denyInsteadOfPrompting` と `blockWritesOutsideWorkingDirectories` で「止まらない・はみ出さない」
3. 確認: `/permissions` の Recently blocked と `claude auto-mode critique`

認証期限・MCP hook・`add-json` は 1 枚のスライドで補足する程度に落とす。統合後の参考点は 4/3/4/4 = 15 程度。社内で auto mode が有効かどうか（Team の組織設定）を確認できることが条件。

## P5: `/usage` 改善と idle compaction

**判定: 却下（単独枠として）。1〜2 分の Tips としてほかの枠に入れるのは可**

- **根拠の確からしさ**: 2.1.305（10/20）の `f` でまとめて修正、2.1.299（10/14）の idle compaction と `"idleCompaction": false`、2.1.314 の VS Code / Desktop への拡大、2.1.316 の Opus 4.8 fast mode の非推奨化（11/30 で削除）: すべて一致。`/skill-doctor`（Week 36、9 月）は critic 側では未確認で、対象期間外。
- **却下理由**:
  1. **中身が薄い**: `f` キー 1 つと、何もしなくても自動で動く idle compaction。15 分を埋める要点がない。
  2. **デモ結果が予測できない**: practitioner 自身が「何が出るかは事前に分からない」と書いている。CLAUDE.md の「30 分以内に準備できるデモ」の基準を満たす保証がない。
  3. **すぐ古くなる**: fast mode の削除は 2026-11-30。勉強会の開催日は不明だが、それ以降なら話題として成立しない。
  4. **前提が推測**: 「すぐ上限に達するのは Team プラン利用者に共通の悩み」には根拠がない（CLAUDE.md にも backlog にも記載なし）。
  5. idle compaction の対象は 200K トークン以上の会話だけで、多くの利用者には関係が薄い。
- **既出チェック**: 重複なし。ただし Claude 関連が 5 件目になる偏りを強める。
- **参考点**: 新しさ 4 / 実務 3 / 15 分 4 / デモ 2 = 13

---

## 取得できなかった URL / 未確認のもの

critic 側で取得に失敗したもの:
- https://geminicli.com/docs/changelogs/ （ENOTFOUND。セッションのネットワーク許可外）

ネットワーク制約により critic 側で試していないもの（ほかのエージェントが ENOTFOUND を報告済み、または同じ系統）:
- https://docs.flutter.dev/release/release-notes/release-notes-3.50.0
- https://dart.dev/guides/whats-new
- https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/
- https://verygood.ventures/blog/flutter-3-50-dart-3-14.md
- https://blog.cloudflare.com/vinext-nextjs-on-vite/
- https://infoq.com/news/2026/10/cloudflare-viteplus-stable
- https://blog.cocoapods.org/CocoaPods-Trunk/ ほか（read-only の日付は検索結果の要約のみ）

critic 側で取得・確認できたもの:
- https://code.claude.com/docs/en/changelog （offset 0〜400000。2.1.299〜2.1.330 の該当項目）
- https://code.claude.com/docs/en/code-review.md
- https://code.claude.com/docs/en/code-review-pull-requests.md
- https://code.claude.com/docs/en/auto-mode-config.md
- https://github.com/google-gemini/gemini-cli/releases
- https://github.com/cloudflare/vinext
- https://raw.githubusercontent.com/flutter/flutter/stable/CHANGELOG.md
- https://raw.githubusercontent.com/dart-lang/sdk/stable/CHANGELOG.md （main ブランチ版も参照）

critic 側で再取得していないもの（practitioner は確認済みとしている）: code-review-local.md、scheduled-tasks.md、whats-new.md。settings-reference.md はどちらも未確認。

再実行の提案: github.com / raw.githubusercontent.com は取得できるので、trend-scout は一次情報の代わりとして GitHub 上の CHANGELOG やリリースを使えば根拠を補強できる。
