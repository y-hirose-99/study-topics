# practitioner（実務適用）調査メモ

- 調査日: 2026-11-23（JST）
- 対象期間: 2026-10-20 以降（初回）
- 観点: 「明日から業務で試せるか」。新しさより持ち帰りやすさを優先
- 重複チェック: CLAUDE.md の過去テーマ 3 件、backlog.md（候補・採用・却下ともに空）と照合済み

## 注意（偏りについて）

今回のセッションでは github.blog / workspaceupdates.googleblog.com / verygood.ventures が DNS 解決できず（ネットワーク許可外）、
一次情報を取得・確認できたのは code.claude.com（Claude Code 公式ドキュメント・changelog）のみ。
そのため 5 件すべてが Claude Code 関連になっている。Flutter / Gemini / GitHub 側の話題は「未確認の手がかり」として末尾に記載する。
リード側で他エージェントの候補と組み合わせてバランスを取ってほしい。

---

## 候補 1: `/code-review` の新しい重要度ラベルと `--fix` で「PR 出す前のセルフレビュー」を定着させる

- **概要**: Claude Code の `/code-review` が、指摘を Important / Pre-existing / Nit に分類し、最後に修正案を出すようになった。`--fix` で作業ツリーに直接適用、`--comment` で GitHub PR にインラインコメント投稿できる。どのプランでも使え、バックグラウンドのサブエージェントで動くので作業を止めずに済む。
- **何が新しいか（日付）**: 2.1.328（2026-11-19）で重要度ラベルと修正案の提示に変わった。`/simplify` は見た目の整理専用として分けられた。関連して、マネージド版 Code Review（GitHub App）に Quick / Standard / Deep のレビューレベルが入り（2.1.304、2026-10-19）、`@claude review stop`（2.1.307、2026-10-21）と費用 CSV 出力（2.1.323、2026-11-12）も追加されている。
- **想定対象者**: モバイル / Web / インフラ全員（GitHub でPR を出す人すべて）
- **参加者が持ち帰れるもの**: `/code-review` → `--fix` → PR 作成、という手順と、Important 件数で CI を止める `gh api` + jq のワンライナー
- **15 分構成案**:
  1. （4 分）ローカル `/code-review` と、組織で有効化するマネージド版 Code Review の違い（費用・設定する人・レビューを起動するタイミング）
  2. （7 分）デモ: バグを仕込んだブランチで `/code-review` → 重要度ラベルを確認 → `/code-review --fix` で修正 → `HEAD~3..HEAD` のように対象を範囲指定する
  3. （4 分）チーム運用: プロジェクトのルールを CLAUDE.md に書いておく（`/code-review` も CLAUDE.md を読む）、Important 件数を check run から読み取って CI を止める方法
- **30 分以内で準備できるデモ案**: 小さな Flutter または Web のサンプルリポジトリに、null 参照や境界値のバグを 2〜3 個仕込んだ commit を作り、`/code-review` → `/code-review --fix` を実演する。準備はブランチ作成だけ。
- **根拠 URL（取得・確認済み）**:
  - https://code.claude.com/docs/en/code-review-local.md
  - https://code.claude.com/docs/en/code-review-pull-requests.md
  - https://code.claude.com/docs/en/changelog （2.1.304 / 2.1.307 / 2.1.323 / 2.1.328 の項目）
- **推測部分**: マネージド版 Code Review が Team プランで使えるかは、今回確認したページでは分からなかった。デモはどのプランでも動くローカル版 `/code-review` を前提にするのが安全。

---

## 候補 2: `/loop` と `loop.md` で「PR と CI の見張り」を Claude に任せる

- **概要**: `/loop 5m <prompt>` で決まった間隔、`/loop <prompt>` で Claude が間隔を自分で決めて、セッション内でプロンプトを繰り返し実行できる。`.claude/loop.md` にチーム共通の見張り手順（CI が赤なら原因を調べて最小修正、レビューコメントに対応、など）を書いておけば、引数なしの `/loop` だけで同じ動きを再現できる。
- **何が新しいか（日付）**: 2.1.326〜2.1.330（2026-11-16〜20）で、定期タスクと `/loop` の上限が 7 日から 30 日に延びた（changelog による）。2.1.329（2026-11-19）で `/tasks` に定期タスク・`/loop` の次回起動・goal・fork がまとめて表示されるようになり、`x` で止められる。2.1.319（2026-11-06）では終わったサブエージェントが `/tasks` に残り、あとから話しかけられるようになった。
- **想定対象者**: Web / モバイル（PR 対応）、インフラ（デプロイ完了待ち）
- **参加者が持ち帰れるもの**: そのまま使える `.claude/loop.md` のテンプレートと、`remind me at 15:03 ...` のような一回限りリマインダーの使い方
- **15 分構成案**:
  1. （3 分）クラウド / Desktop / `/loop` の 3 種類の定期実行の使い分け（公式の比較表を使う）
  2. （8 分）デモ: CI が落ちる PR に対して `/loop check whether CI passed and address any review comments` を起動 → `/tasks` で状態を確認 → 止める
  3. （4 分）`loop.md` をチームで共有する際の注意（権限確認を通ること、繰り返しタスクの起動時刻が最大 30 分ずれること、毎時 :00 / :30 を避ける）
- **30 分以内で準備できるデモ案**: わざと失敗するテストを含む PR を GitHub 上に用意し、`/loop 1m` で CI の結果を見張らせる。待ち時間を短くするため、GitHub Actions は数十秒で終わるジョブにしておく。
- **根拠 URL（取得・確認済み）**:
  - https://code.claude.com/docs/en/scheduled-tasks.md
  - https://code.claude.com/docs/en/changelog （2.1.319 / 2.1.329 / 2.1.326〜330 の項目）
- **推測部分**: 確認した時点の scheduled-tasks ドキュメントは「7 日で期限切れ」のままで、changelog の「30 日」と食い違っている。ドキュメントの更新が遅れていると推測。発表前に `claude --version` と実際の動作で確認すること。

---

## 候補 3: 「auto mode が止まりすぎる」を解消する `/auto-mode-setup` と `claude auto-mode critique`

- **概要**: auto mode は、分類器が許可確認の代わりに安全チェックをするモード。初期状態では作業ディレクトリと remote しか信頼しないため、社内の GitHub org や社内ドメインへのアクセスが止められやすい。`/auto-mode-setup` は CLAUDE.md・git remote・最近実行したコマンドを読んで `autoMode.environment` の下書きを作る。`claude auto-mode critique` は自分で書いたルールのあいまいな箇所を指摘してくれる。
- **何が新しいか（日付）**: 2.1.304（2026-10-19）で `/auto-mode-setup` が追加され、feature flag を取得しない環境やサードパーティプロバイダでも使えるようになった。2.1.326（2026-11-16）と 2.1.328（2026-11-19）では、ルート・ホーム・作業フォルダを狙う `rm` の承認必須化など、auto / bypass モードの許可確認が厳しくなっている（アップデート後に「急に確認が増えた」という人向けの話にもなる）。
- **想定対象者**: Claude Code を使う全員。とくにインフラ（社内ドメイン、バケット）
- **参加者が持ち帰れるもの**: `git push` と `gh pr create` だけは人が確認する `permissions.ask` のレシピと、`autoMode.environment` の書き方テンプレート
- **15 分構成案**:
  1. （4 分）許可確認の多さと安全性のバランス。ask / deny / auto mode 分類器の評価順序
  2. （7 分）デモ: `/auto-mode-setup` で下書きを作る → `claude auto-mode config` で反映を確認 → `claude auto-mode critique` で講評をもらう
  3. （4 分）`/permissions` の Recently blocked タブで止められた操作を確認し、environment に足すか allow に足すかを判断する
- **30 分以内で準備できるデモ案**: 社内 GitHub org を remote に設定したダミーリポジトリで `/auto-mode-setup` を実行。社外の pastebin 風ドメインへの送信が止められる様子と、社内ドメインを environment に加えたら通る様子を対比する。
- **根拠 URL（取得・確認済み）**:
  - https://code.claude.com/docs/en/auto-mode-config.md
  - https://code.claude.com/docs/en/changelog （2.1.304 / 2.1.326 / 2.1.328 の項目）
- **推測部分**: Team プランで組織管理者が auto mode をオフにしているかどうかは社内設定次第で、今回は確認していない。ドキュメントには Team プランで組織単位の制御がある旨が書かれている。

---

## 候補 4: 無人実行・CI で Claude Code を安全に動かす権限設定（10〜11 月の新設定まとめ）

- **概要**: スケジュール実行や `claude -p` のような無人実行では、許可確認で止まる、または意図しない書き込みが起きることが課題になる。10〜11 月に、作業ディレクトリ外への書き込み禁止、確認待ちを拒否扱いにして処理を続ける設定、MCP hook の失敗時ブロックなどが追加された。
- **何が新しいか（日付）**:
  - `permissions.blockWritesOutsideWorkingDirectories`（2.1.314、2026-10-30）: どの権限モードでも Write / Edit が作業ディレクトリ外を拒否する
  - `permissions.denyInsteadOfPrompting`（2.1.319、2026-11-06）: 確認待ちになる操作を拒否し、ターンを続ける
  - `claude auth status` の `loginExpiresAt` と `claude -p` JSON の `credential_expires_at`（2.1.318、2026-11-04）: 認証期限を監視できる
  - `mcp_tool` hook の `"onFailure": "block"`（2.1.328、2026-11-19）
  - `claude mcp add-json <name> -`（2.1.327、2026-11-17）: トークンをシェル履歴に残さずに MCP を登録できる
- **想定対象者**: インフラ、CI を整備する人、Claude を定期実行している人（この勉強会ネタ調査自体も無人実行）
- **参加者が持ち帰れるもの**: 無人実行用の `settings.json` 雛形（上の 2 つの permissions と allow ルールを組み合わせたもの）
- **15 分構成案**:
  1. （3 分）無人実行でよくある失敗（確認待ちで止まる、範囲外への書き込み、認証切れ）
  2. （8 分）デモ: `denyInsteadOfPrompting` を付けた `claude -p` で、確認が必要な操作が止まらず拒否されて処理が続く様子。`blockWritesOutsideWorkingDirectories` で `/tmp` 以外の範囲外書き込みが拒否される様子
  3. （4 分）`credential_expires_at` を監視して認証切れを事前に検知する、`mcp add-json -` で秘密情報を扱う
- **30 分以内で準備できるデモ案**: 空ディレクトリで `.claude/settings.json` に 2 設定を書き、`claude -p "../outside.txt に書いて"` を実行して拒否の挙動を見せる。JSON 出力から `credential_expires_at` を jq で取り出す。
- **根拠 URL（取得・確認済み）**:
  - https://code.claude.com/docs/en/changelog （2.1.314 / 2.1.318 / 2.1.319 / 2.1.327 / 2.1.328 の項目）
  - https://code.claude.com/docs/en/auto-mode-config.md （ask / deny と分類器の関係）
- **推測部分**: 各設定の詳しい挙動は changelog の 1 行説明しか確認していない（settings-reference の該当項目は未取得）。デモ前に https://code.claude.com/docs/en/settings-reference.md で確認すること。

---

## 候補 5: Claude Team プランの使用量を節約する: `/usage` のワンキー改善とアイドル時の自動要約（idle compaction）

- **概要**: 「すぐ上限に達する」は Team プラン利用者に共通の悩み。`/usage` はスキル・サブエージェント・プラグイン・MCP ごとに使用量の内訳を出し、10 月からは改善案を `f` キーで適用できる。長い会話を放置するとプロンプトキャッシュが切れる前に自動で要約する idle compaction も入った。
- **何が新しいか（日付）**:
  - `/usage` のワンライン改善を `f` で適用（2.1.305、2026-10-20）
  - ターミナルの idle compaction（2.1.299、2026-10-14。`"idleCompaction": false` で無効化できる）、VS Code と Desktop の Code タブにも拡大（2.1.314、2026-10-30）
  - 関連: Opus 4.8 の fast mode は 2026-11-30 で終了し、Opus 5.5 への移行が案内されている（2.1.316、2026-10-31）。月末の切り替えを前に話題にしやすい
- **想定対象者**: Claude を使う全員
- **参加者が持ち帰れるもの**: 自分の `/usage` を見て、使われていない MCP やスキルを外すチェックリスト
- **15 分構成案**:
  1. （3 分）使用量を食う要因（コンテキストの肥大化、キャッシュ切れ、常時読み込まれる MCP やスキル）
  2. （8 分）デモ: 自分の環境で `/usage` を開く → 改善案を `f` で適用 → `/skill-doctor`（2026 年 9 月追加）でスキルごとのコストを確認
  3. （4 分）idle compaction の動きと無効化方法、fast mode 終了（11/30）への対応
- **30 分以内で準備できるデモ案**: MCP サーバやスキルを多めに入れた環境を用意して `/usage` を開き、改善案の適用前後を見せる。
- **根拠 URL（取得・確認済み）**:
  - https://code.claude.com/docs/en/changelog （2.1.299 / 2.1.305 / 2.1.314 / 2.1.316 の項目）
  - https://code.claude.com/docs/en/whats-new.md （/usage は Week 21、/skill-doctor は Week 36）
- **推測部分**: `/usage` が出す改善案の具体的な中身は環境によって変わるため、デモで何が出るかは事前に分からない。リハーサルで確認すること。

---

## 取得できなかった URL

| URL | 状況 |
|---|---|
| https://github.blog/changelog/month/10-2026/ | DNS 解決できず（ENOTFOUND。セッションのネットワーク許可外） |
| https://verygood.ventures/blog/flutter-3-50-dart-3-14.md | DNS 解決できず（ENOTFOUND） |
| https://workspaceupdates.googleblog.com/2026/11/expanding-gemini-in-gmail-to-more-Google-Workspace-users.html?m=1 | DNS 解決できず（ENOTFOUND） |

上の 3 ホストで失敗したため、ツールの指示に従い、それ以外の新しいホスト（docs.flutter.dev、workspace.google.com など）への取得は試していない。

## 未確認の手がかり（検索結果の要約のみ。候補には採用していない）

ネットワークの許可が出たら、一次情報を確認したうえで候補化を検討する。
- Flutter 3.50 / Dart 3.14: 2026 年 11 月の stable 予定。第三者記事（2026-11-11）では公開済みとされている。モバイル（Flutter）チーム向けとして本来は有力
- Gemini in Gmail の対象拡大: Workspace Updates ブログ（2026-11-10）とされるが、本文は未確認。既存機能の対象拡大で、新機能ではないとの報道もある
- GitHub: stacked pull requests の GA、secret scanning の検出対象追加（2026 年 10 月上旬とされる）。新しさの面では対象期間の少し前
