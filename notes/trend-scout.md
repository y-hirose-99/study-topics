# trend-scout 調査メモ（2026-11-26 JST 実行）

- 対象期間: 前回レポート（reports/2026-11-25.md）以降。実質 2026-11-25〜26 の 1 日分
- 重複チェックの対象: CLAUDE.md の過去テーマ 3 件、backlog.md の候補 8 件・却下 7 件
- 読めたホスト: code.claude.com / github.com / raw.githubusercontent.com。nulab.com・modelcontextprotocol.io は ENOTFOUND（末尾参照）

## 結論

- **新規候補は 2 件だけ**（5 件には届かない。無理に埋めていない）。
  - 新規 1: Claude Code の「MCP サーバーから skills を配る」（public beta、changelog 上は 2.1.332 / 11/25）。本命
  - 新規 2: claude-code-action v1.0.288 で fork PR が既定スキップに（11/23、破壊的変更）。社内での影響は限定的な可能性が高い
- **既出の更新が 3 件**: GitHub Actions ランナーイメージ（#15373 VS 2027）、`gh event listen` 枠（gh v2.106.0 のセキュリティ更新）、Backlog MCP Server（v0.23.4）。
- **前回の「未確認の手がかり」4 件**: VS 2027 は一次確認できた。Flutter 3.51 beta は中身がほぼ hotfix の cherry-pick だった。Backlog の新プランとプロジェクト横断ガントは、一次情報（nulab.com）が ENOTFOUND で確認できなかった（下の「未確認の手がかりの確認結果」）。

---

## 新規候補

### 新規 1: MCP サーバーから skills を配る: Claude Code の「Skills over MCP」（public beta）と、チームでの許可・制限のしかた

- **リリース日**: changelog では Claude Code **2.1.332（2026-11-25）** の Added に "Added skills from MCP servers (beta). They show as `server:skill` in the `/` menu" とある。一方、docs（mcp.md / skills.md）は「v2.1.331 以降が必要」と書いている。changelog と docs で版が 1 つずれている。デモは 2.1.332 以上で行うのが安全（推測）
- **概要**: MCP サーバーがツールと一緒に skills を提供できるようになった（MCP の Skills extension を実装したサーバーが対象）。`/` メニューや `/skills` に `<server>:<skill>` の名前で出て、`/docs:pdf report.pdf` のようにコマンドとして呼べる。権限ルールは `Skill(mcp__docs__*)` / `Skill(docs:pdf)` で deny / ask / allow を書ける。auto mode では classifier が読み込みの可否を決め、Manual では「Allow this version」で版ごとに承認する（サーバー側で skill が変わると承認し直し）。
- **社内（Claude Team プラン）に直結する点**: docs に "With `/login` on a Team or Enterprise plan, they're off until your organization turns them on." とある。connector の skills は Owner が Organization settings > Connectors で **Skills over MCP** をオンにする。それ以外のサーバーは managed settings で `disableDirectMcpSkills: false` にする。逆に止めたいときは `disableDirectMcpSkills: true`、connector 単位で「Restrict to Ask / Blocked」。
- **関連（同じ 2.1.332）**: skill を読み込むときに埋め込みのシェルコマンドが走る前に止められる `PreSkillShellCommand` フックが追加された（exit 2 でブロックし、skill はその出力なしで読み込まれる。docs の hooks.md の lifecycle 表で確認）。「配られた skill が勝手にコマンドを実行しないか」をチームで守る話としてまとめられる。
- **なぜ今話す価値があるか**: 11/25 に出たばかりの beta で、Team プランでは既定オフ。「社内で有効にするか、どう制限するか」を決める前に全員で知っておく価値がある。
- **15 分構成案（提案）**: (1) skills と MCP の役割分担、`server:skill` の呼び方（4 分） (2) デモ: skills を返す MCP サーバーを繋いで `/` から呼ぶ、`/permissions` で `Skill(mcp__<server>__*)` を ask / deny にしたときの違いを見せる（7 分） (3) Team での有効化手順、セキュリティスキャン対象の範囲（第三者 Directory connector など一部だけ）、`PreSkillShellCommand` での防御（4 分）
- **デモの課題（重要）**:
  - skills を返すには、サーバーが MCP の Skills extension と protocol revision 2026-07-28 以降に対応している必要がある。実際に対応している公開サーバーは今回確認できなかった（modelcontextprotocol.io が ENOTFOUND）。社内で使っている Backlog MCP Server（nulab/backlog-mcp-server）の Releases にも skills 対応の記載はない。デモ用の最小サーバーを自作する必要がある可能性が高く、準備 30 分に収まるかは不明（推測）。
  - Team プランでは Owner の操作が要る。個人の Pro / Max でのデモならすぐ使える。
  - 使えない環境: `claude -p`、cloud sessions、GitHub Actions、Agent SDK、Bedrock / Agent Platform（Vertex）などのクラウドプロバイダ経由、HIPAA / ZDR 組織。VS Code 拡張は「rolling out」。
- **一次情報 URL**:
  - https://code.claude.com/docs/en/changelog （**取得・確認済み**。2.1.332 / November 25, 2026）
  - https://code.claude.com/docs/en/mcp.md （**取得・確認済み**。"Use skills from MCP servers" 節。"MCP skills are in public beta and require Claude Code v2.1.331 or later."）
  - https://code.claude.com/docs/en/skills.md （**取得・確認済み**。MCP skills では frontmatter の `allowed-tools` などや `!` コマンドが無視される）
  - https://code.claude.com/docs/en/hooks.md （**取得・確認済み**。ただし `PreSkillShellCommand` の詳細節は 10 万字以降で未読。lifecycle 表の記述のみ確認）
  - https://modelcontextprotocol.io/extensions/skills/overview （**未取得: ENOTFOUND**。docs からのリンク先。Skills extension の仕様）
- **重複チェック**: 却下済み「Gemini の Gem → skills 移行」（Gemini アプリ）、「Dart package skills」（pub パッケージ同梱）とは別物。ただし「skills」という言葉が 3 つ目なので、リードは区別して扱うこと。backlog の「Backlog MCP Server を Claude に繋ぐ最小構成」（保留）とは、MCP という点で隣接する。まとめるなら「MCP の新しい使い方」で 1 枠にする手もあるが、Backlog MCP は skills 非対応なのでデモはつながらない（提案）。「許可確認との付き合い方（auto mode）」とも権限ルールの話で一部重なるので、ここでは skills 固有の `Skill(...)` ルールに絞る。

### 新規 2: claude-code-action v1.0.288 の破壊的変更: fork からの PR を既定でスキップ（+ キャンセル時のトークン失効）

- **リリース日**: v1.0.288 は **2026-11-23**（PR #2114 のマージも 11/23）。同日の v1.0.287 で「実行がキャンセルされたらセッションを止めて app token を失効させる」。最新は v1.0.290（11/25）
- **概要**: `allow_fork_prs` の既定値が `"false"` になった。fork PR へのメンションやプロンプト、fork の `pull_request` / review / `workflow_run` イベントでは Claude が動かず、ステップは警告付きで「成功（緑）」になる。そのため、**この action だけを走らせる required check は、レビューなしで通ってしまう**。issues・`workflow_dispatch`・`schedule` は影響なし。戻すには `with: allow_fork_prs: "true"`（先に docs/security.md の該当節を読むよう PR に書かれている）。`@v1` で固定しているワークフローには次のリリースで自動的に入る。カスタム `github_token` に `pull-requests: read` がないと "Could not find out whether pull request #N is from a fork." で失敗するようになった点も要注意。
- **なぜ今話す価値があるか**: `@v1` を使っていれば、何もしなくても挙動が変わる。「緑なのにレビューされていない」は気づきにくい。
- **想定デモ**: テスト用リポジトリで fork から PR → Actions ログに警告が出て緑で終わるのを見せる → `allow_fork_prs: "true"` で動くのを見せる。準備は 30 分程度でできる見込み（推測。リポジトリと API キーの用意が必要）
- **評価上の注意（推測）**: 社内のリポジトリは private で、fork を使わない運用なら影響はほぼない。社内で claude-code-action を使っているかも未確認。実務への効きは低めになる見込みで、15 分枠よりも TOP1（`/code-review`）の枠で「CI 側の話」として 1 枚触れる程度が妥当かもしれない（提案）。
- **一次情報 URL**:
  - https://github.com/anthropics/claude-code-action/releases （**取得・確認済み**。v1.0.283〜v1.0.290 の日付）
  - https://github.com/anthropics/claude-code-action/releases/tag/v1.0.288 （**取得・確認済み**）
  - https://github.com/anthropics/claude-code-action/pull/2114 （**取得・確認済み**。挙動の詳細と opt-in 方法）
- **重複チェック**: backlog の `/code-review`（ローカル / マネージド版）とは別のプロダクト。過去テーマ「Copilot から Claude への乗り換え比較」とも重ならない。

---

## 既出の更新

### 更新 1: GitHub Actions ランナーイメージの 11 月の変更【backlog: 15 点・次点・条件付き】

- **#15373（11/20 起票、11/23 に pin）**: Windows Server 2025 と Windows 11 Arm64 のイメージが Visual Studio 2027 になった。VS 2026 のラベル `windows-2025-vs2026` / `windows-11-vs2026-arm` は **12/1 から非推奨化が始まる**。移行先は `windows-latest` / `windows-2025` / `windows-11-arm`。前回の「未確認の手がかり」はこれで一次確認できた。
- **注意**: 11/22 に「VS 2027 の公式ライフサイクル情報が見当たらない」というコメントがある（ランナー側の告知としては一次情報だが、VS 2027 そのものの位置付けは未確認）。11/25 に「VS 2027 イメージで `clang-cl` が壊れた（STL が Clang 22 以上を要求、イメージは Clang 20）」という報告があり、関連 issue #15368（11/19、LLVM 22 への更新要望、Open、PR #15370 待ち）に紐づけられている。回避策は LLVM 22 を手動で入れるステップ（#15368 の記載）。
- **評価への影響（提案）**: 社内の Windows / C++ ビルドは少ないと思われる（推測）ので、実務への効きは上がらない。ただし「`-latest` ラベルの棚卸し」という同候補の軸に、12/1 という具体的な日付が 1 つ増える。
- **URL**: https://github.com/actions/runner-images/issues/15373 （**取得・確認済み**）/ https://github.com/actions/runner-images/issues/15368 （**取得・確認済み**）/ https://github.com/actions/runner-images/issues?q=is%3Aissue+label%3AAnnouncement+sort%3Acreated-desc （**取得・確認済み**。11/25 以降の新しい Announcement はなし。最新は #15397 Xcode 27 GA、11/23）

### 更新 2: `gh event listen` 枠【backlog: 14 点・保留】に gh v2.106.0（11/25）

- `gh event` 自体の変更は、docs に「Event Streams は同じイベントを 2 回以上届けることがある」と追記されただけ（#15094）。preview・ウェイトリスト制は変わらず。
- 同じリリースで **セキュリティ修正 7 件**（GHSA-45h6-2fh4-pr93 ほか。多くは端末制御文字の未エスケープ、`gh skill update` が同名の別リポジトリの skill を入れてしまう GHSA-q2rq-2r2v-2jm5 など）。リリースノートは "Users are advised to update `gh` to version `v2.106.0` as soon as possible." と書いている。
- `gh auth logout` が OAuth トークンを**失効させてから**ローカル資格情報を消すようになった（`--no-revoke` で旧挙動）。Git credential helper に保存された HTTPS 資格情報も消す。
- **評価への影響（提案）**: 単独枠にするほどではない。`gh event listen` を話すなら「v2.106.0 以上で」と添える。全社向けの周知（gh を上げる）としては有用。
- **URL**: https://github.com/cli/cli/releases （**取得・確認済み**）/ https://github.com/cli/cli/releases/tag/v2.106.0 （**取得・確認済み**）

### 更新 3: Backlog MCP Server【backlog: 14 点・保留】

- v0.23.4（11/18）が出ていた。中身は依存更新（backlog-js 1.1.0）と Actions のピン留め、docs の修正だけで、新機能はない。新規 1 の Skills over MCP には対応していない（Releases に記載なし）。
- CVE-2026-111768 は Releases の v0.23.3 の記載にも同じ番号で出ている。ただし NVD / GHSA での確認はまだ（github.com の advisory ページは今回見ていない）。
- **URL**: https://github.com/nulab/backlog-mcp-server/releases （**取得・確認済み**）

### 参考: Claude Code 2.1.332（11/25）で既出候補に関係するもの

- VS Code 拡張（backlog: TOP3 条件付き）: 提案された diff を閉じて編集が適用されなかったときの通知が増えた。サイドバーで `/rewind` すると自分のペースで回る `/loop` が止まる旨の警告が追加された。デモの挙動に小さく影響する程度。
- `/loop`・`/schedule` の表示で「routine」が「cloud scheduled task」に言い換えられた（`/routines` は引き続き使える）。TOP1 の `/loop` パートのスライドの用語に注意。
- `/permissions` が「次回起動時に無視されるルール」を拒否し、理由を表示するようになった。許可確認の候補（次点）の補足になる。
- 出典: https://code.claude.com/docs/en/changelog （**取得・確認済み**。2.1.332 の Changed 約 30 件・Bug fixes 93 件のうち、主要部分のみ確認）

---

## 未確認の手がかりの確認結果（前回レポートから）

| 手がかり | 結果 |
|---|---|
| Flutter 3.51 beta（3.51.0-0.2.pre、11/18） | beta ブランチの 11/18 のコミットは、3.50.1 と同じ Android ビルド修正（split APK の versionCode #196924、dotfile アセット #196747 など）と flutter_tools の修正の cherry-pick だけ。3.51 固有の新機能は、docs の whats-new・breaking-changes（"Not yet released to stable" 節はコメントアウトされていて空）・beta の CHANGELOG（3.47 までしか載っていない）のどこにも記載がない。**勉強会ネタになる中身はなし**。タグは 3.51.0-0.1.pre（11/10）と 0.2.pre（11/18）のみで、11/19 以降のタグはない |
| runner-images #15373（VS 2027） | 一次確認済み。上の「更新 1」 |
| Backlog の新プラン（2027-01-01） | nulab.com が ENOTFOUND で一次情報は取れず。検索要約では「現行プランの新規契約は 2026-12-31 で終了、2027-01-01 からエコノミー / ビジネス / プロフェッショナルの 3 プラン、既存ユーザーは 2027-01-01 以降最初の契約更新日に移行」（二次情報、@IT 2026-07-24）。技術ネタではないので候補にしない |
| Backlog のプロジェクト横断ガントチャート | 今回も確認できず（検索でも出てこない） |

## 調べたが候補にしなかったもの

- **Dart 3.15**: main の CHANGELOG では Unreleased。dev タグ 3.15.0-181.0.dev（11/26）まで。`this` の型昇格、import/export の `show`/`hide` を 1 つまでに制限（言語版 3.15 以降はエラー）などがあるが、stable はまだ。stable が出たら候補になる。
- **Vite**: v8.4.0-beta.2（11/25）。stable の最新は v8.3.8（11/11）。beta のみなので見送り。
- **Next.js**: 11/16〜23 は v16.5.0-canary だけ。
- **TypeScript**: 11/12 以降のリリースなし（7.1 Beta は 10/20）。
- **Gemini CLI**: 11/24 の v0.71.1 / v0.72.0-preview.0 以降のリリースなし。
- **Firebase CLI**: v15.37.0（11/19）以降のリリースなし。
- **GCP / Google Workspace**: 一次情報のホストが前回同様に読めないため、今回は試していない。

---

## 取得できなかった URL

| URL | エラー |
|---|---|
| https://nulab.com/whats-new/backlog/ | `getaddrinfo ENOTFOUND nulab.com` |
| https://modelcontextprotocol.io/docs/2026-07-28/develop/build-with-agent-skills.md | `getaddrinfo ENOTFOUND modelcontextprotocol.io` |

ENOTFOUND が出たため、ツールの指示に従って他の外部ホストは試していない。次は検索結果に出ただけで未取得: modelcontextprotocol.io/extensions/skills/overview（docs 内リンク）/ docs.flutter.dev / atmarkit.itmedia.co.jp / codezine.jp。

その他の注意:
- https://raw.githubusercontent.com/flutter/flutter/beta/CHANGELOG.md は取得できたが、3.47 までしか載っておらず 3.51 の記載はなかった。
- https://code.claude.com/docs/en/hooks.md は 30 万字あり、`PreSkillShellCommand` の詳細節（10 万字以降）は未読。

**運用上の提案**:
- ネットワーク許可が必要なホストに **modelcontextprotocol.io** を追加する（新規 1 の Skills extension 仕様の一次情報）。nulab.com は前回からの継続。
- 今回も PostToolUse フックのエラー（`/root/.claude/hooks/wf-collector.py` が見つからない、`base64: invalid input`）が毎回出た。調査には影響なし。
