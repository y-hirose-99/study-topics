# practitioner 調査メモ 2026-11-26（JST）

- 対象期間: reports/2026-11-25.md（11/25 作成）以降
- 観点: 明日から業務で試せるか（新しさより持ち帰れるか）
- **結論: 新ネタは少ない。新規は 3 件だけ**（Claude Code 2.1.332 から 2 件、gh v2.106.0 から 1 件）。Flutter と GitHub Actions ランナーには、11/25 以降の新しい一次情報が見つからなかった。無理に 5 件までは埋めていない。
- 重複確認: backlog.md の候補・却下と、CLAUDE.md の過去テーマを確認済み。`/code-review`、VS Code 拡張、auto mode、`gh event listen`、Flutter 3.50、CocoaPods、runner-images、Backlog MCP とは重ならない。

---

## 新規候補

### P1. skill の `` !`cmd` `` を hook で検査する: `PreSkillShellCommand` で「配られた skill」を安全に使う

- **リリース日**: Claude Code 2.1.332（2026-11-25）。changelog と hooks docs で確認済み。
- **概要**:
  - skill に書いた `` !`gh pr diff` `` のような埋め込みコマンドは、skill を読み込むときに実行される。これまでは `PreToolUse` hook が発火しないので、チェックできなかった（skills docs に明記）。
  - 2.1.332 で `PreSkillShellCommand` hook が追加された。exit 2 か `permissionDecision: "deny"` を返すと、そのコマンドだけを止められる。skill 自体は読み込まれ、出力の位置には `[not run: blocked by hook: <reason>]` が入る。
  - 入力には `command_name`、`command_source`（`projectSettings` / `plugin` など）、`invoked_by` が入るので、「プラグイン由来の skill の rm を止める」といった条件で書ける。
- **参加者が持ち帰れるもの**: チームで共有する `.claude/settings.json` に入れる「skill の破壊的コマンドを止める hook」の雛形 1 つ
- **15 分構成案**:
  1. （4 分）skill の `!` 埋め込みは何か。実行が読み込み時なので `PreToolUse` では止められない、という盲点を説明する
  2. （7 分）デモ: docs のテスト手順どおりに、1 行の `.claude/skills/hook-test/SKILL.md`（`` !`echo 'rm -rf'` ``）と `block-rm.sh` を用意し、`/hook-test` で `Not run in /hook-test: ... (blocked by hook: ...)` が出ることを見せる
  3. （4 分）チームで使うときの設定
     - matcher は `Bash|PowerShell`、`onFailure: "block"` を付ける。async の hook は使わない
     - auto mode では、読み込み後に Claude がツールとして再実行すると `PreToolUse` 側で発火するので、同じスクリプトを両方の hook に登録する
     - 2.1.331 以下に設定だけ入れると起動時に警告ダイアログが出る。`requiredMinimumVersion` で最低バージョンを揃える
- **注意**:
  - 許可（allow）には使えず、ブロック専用。
  - 既出の「許可確認との付き合い方（auto mode + 無人実行の deny 設定）」と隣り合うテーマ。リードの判断で、その候補の 1 パートに統合してもよい（推測）。
- **根拠 URL（すべて取得・確認済み）**:
  - https://code.claude.com/docs/en/changelog （2.1.332）
  - https://code.claude.com/docs/en/hooks.md （PreSkillShellCommand 節。入力フィールド、決定の制御、テスト例）
  - https://code.claude.com/docs/en/skills.md （`!` 埋め込み、`disableSkillShellExecution`）
- **自己採点（参考）**: 新しさ 5 / 実務への効き 3 / 15 分で話せるか 4 / デモ映え 5 = 17
  - 実務への効きは、skill を使っている人に限られるので 3 とした。

### P2. Skills over MCP（public beta）: MCP サーバー経由で skill を配る仕組みと、Team 組織での有効化・制限

- **リリース日**: changelog では 2.1.332（2026-11-25）で "Added skills from MCP servers (beta)"。一方、mcp docs / skills docs には「v2.1.331 以上が必要」とあり、記載が食い違う。どちらも取得・確認済み。
- **概要**:
  - MCP サーバーが tool と一緒に skill も提供できるようになった。skill は `/` メニューに `<server>:<skill>` の名前で出る（claude.ai コネクタは `claude_ai_<名前>:`、プラグインは `plugin_<plugin>_<server>:` という接頭辞が付く）。
  - **Team / Enterprise で `/login` している場合は、組織が有効にするまで OFF**。有効にするには、Owner が Organization settings > Connectors で「Skills over MCP」を ON にし、さらに組織の Skills 設定も ON にする必要がある。
  - permission ルールで制御できる。例: `Skill(mcp__docs__*)` を deny / ask / allow、`Skill(docs:pdf)` を deny。
  - MCP skill では、`!` 埋め込みの実行、`@` 参照、`allowed-tools` などの frontmatter が無効になる。
- **参加者が持ち帰れるもの**: 「社内 Team 組織で MCP skill を有効にするか、するならどのルールで絞るか」の判断材料。管理者向けチェックリスト 1 枚
- **15 分構成案**:
  1. （4 分）ローカル skill / プラグイン skill / MCP skill の違い。MCP skill で無効になる機能（`!`、`@`、`allowed-tools`）
  2. （6 分）Team 組織での有効化の手順と、制限の選択肢
     - コネクタの「Restrict to Ask / Blocked」
     - `disableDirectMcpSkills`
     - deny ルール
     - Anthropic の security scan の対象は一部のコネクタだけ
  3. （5 分）使えない環境を確認する
     - `claude -p`、GitHub Actions、Agent SDK、クラウドセッションでは使えない
     - トラブル時は `claude --debug=mcp` と `/status` の Features 行を見る
- **注意（デモ不安）**:
  - 実際に skill を提供している MCP サーバーを、今回は確認できていない。仕様ページ（modelcontextprotocol.io）は ENOTFOUND だった。社内でよく使う Backlog MCP / GitHub MCP が skill を提供しているかも未確認。
  - このためデモは、設定画面と `/permissions` を見せる程度になる見込み。デモ映えは低めに見る。
  - 社内 Team 組織で Owner が有効化できるかを、人間に確認する必要がある。
- **根拠 URL**:
  - https://code.claude.com/docs/en/changelog （2.1.332、取得済み）
  - https://code.claude.com/docs/en/mcp.md （"Use skills from MCP servers" 節、取得済み）
  - https://code.claude.com/docs/en/skills.md （MCP skill の制限、取得済み）
  - https://modelcontextprotocol.io/extensions/skills/overview （**取得できず**、ENOTFOUND）
- **自己採点（参考）**: 新しさ 5 / 実務への効き 3 / 15 分で話せるか 4 / デモ映え 2 = 14

### P3. gh v2.106.0 にすぐ上げる理由: 脆弱性 7 件の修正と、`gh auth logout` がトークンを失効させるようになった件

- **リリース日**: gh v2.106.0（2026-11-25、GitHub Releases で Latest 表示）。取得・確認済み。
- **概要**:
  - 脆弱性 7 件を修正した。リリースノートには "update `gh` to version `v2.106.0` as soon as possible" とある。内容は次のとおり。
    - 制御文字の無害化漏れ（C1 制御文字、verbose ログ、`gh codespace ports`、`gh agent-task view --log`、`gh skill install/search`）
    - `gh skill update` が、同じ名前を使う別リポジトリの skill を入れてしまう問題（GHSA-q2rq-2r2v-2jm5）
    - `gh codespace jupyter` が localhost の cookie を漏らす問題
  - `gh auth logout` の挙動が変わった。ローカルの認証情報を消す前に OAuth トークンを revoke し、Git の credential helper に保存された HTTPS 認証情報も削除する。従来どおりにしたい場合は `--no-revoke` を付ける。
- **参加者が持ち帰れるもの**: 「手元とスクリプトの gh を v2.106.0 に上げる」ことと「`gh auth logout` を使っているスクリプトの洗い出し」の 2 つ
- **15 分構成案**:
  1. （4 分）7 件の GHSA を「端末の制御文字」「skill の取り違え」「codespace」の 3 群にまとめる。`gh skill` を使っている人への影響を説明する
  2. （6 分）デモ: `gh --version` → アップデート → `gh auth status`。テスト用アカウントで `gh auth logout` を実行し、トークンが失効することと、`git credential` から消えることを確認する。`--no-revoke` との違いも見せる
  3. （5 分）運用チェック
     - 共用マシンや CI のスクリプトで `gh auth logout` を使っていないか
     - 複数の端末で同じトークンを使い回していないか
     - Homebrew / apt / winget それぞれの更新方法
- **注意**:
  - 各 GHSA の深刻度は、リリースページに書かれていない（未確認）。
  - revoke の対象は "GitHub CLI OAuth token" と書かれている。`GH_TOKEN` 環境変数で渡した PAT も失効するかは書かれていない（推測では対象外だが、未確認）。デモの前に試すこと。
  - 中身は「アップデートしよう」なので、単独の 15 分枠としてはやや薄い。LT や、既出の `gh event listen` 枠の冒頭 3 分に使う案もある。
- **根拠 URL（取得・確認済み）**:
  - https://github.com/cli/cli/releases/tag/v2.106.0
  - https://github.com/cli/cli/releases
- **自己採点（参考）**: 新しさ 5 / 実務への効き 4 / 15 分で話せるか 4 / デモ映え 3 = 16
  - 実務への効きは、GitHub を全員が使う前提で 4 とした。

---

## 既出の更新（短く）

- **`/code-review`（TOP1）**
  - 2.1.332 に `/code-review` 自体の変更はない。
  - `/loop` については、VS Code のサイドバーで `/rewind` すると自分のペースで回している `/loop` が止まる、という警告が追加された。デモで `/rewind` を使うなら注意。
- **許可確認との付き合い方（次点）**
  - 2.1.332 で `/permissions` が「次回起動時に無視されるルール（ツール名のない `rm -rf` など）」を拒否し、理由を表示するようになった。deny 設定のデモで使える。
  - 2.1.332 では、Desktop の Code タブと VS Code の plan mode も変わった。auto mode の安全チェックが判定できないコマンドを、拒否せずに承認を求めるようになった。
  - Bedrock / Foundry などで Sonnet 5 / 5.5 を使うと auto mode で始まる変更は、社内は Team プラン（Anthropic API の想定）なので、おそらく無関係（推測）。
  - P1（PreSkillShellCommand）はこの候補に統合してもよい。
- **VS Code 拡張（TOP3）**
  - 2.1.332 で、提案された diff を閉じて未適用になったときの通知が増えた。セッションの一部が保存できなかったときの警告も追加された。
  - 拡張のパネルの起動と、長いセッションの表示が速くなった。デモの体感が良くなる程度で、論点は変わらない。
- **`gh event listen`（保留）**
  - v2.106.0 に event 関連の変更はない。preview のまま。
- **Flutter 3.50 / CocoaPods**
  - stable の CHANGELOG の最新は 3.50.1 のまま。whats-new の最新も 11/11（3.50）のまま。
- **runner-images**
  - 11/21 以降の Announcement は #15397（Xcode 27 GA、11/23）だけで、これは既出。

---

## 取得できなかった URL

- https://github.blog/changelog/month/11-2026/ （ENOTFOUND。github.blog はネットワーク許可リストの外と推測）
- https://modelcontextprotocol.io/extensions/skills/overview （ENOTFOUND）

## 補足

- WebSearch で "GitHub changelog November 2026" を検索したが、11 月分は出てこなかった（10 月上旬まで）。github.blog は直接取得できないので、GitHub 本体の 11 月の変更は確認できていない。
- Claude Code の changelog は 2.1.332（11/25）が最新（11/26 時点の取得結果）。
- PostToolUse hook のエラー（`/root/.claude/hooks/wf-collector.py` が見つからない、`base64: invalid input`）は今回も出た。調査には影響なし。
