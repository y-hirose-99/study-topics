# critic レビュー（2026-11-26 JST 実行）

- 対象: notes/trend-scout.md（新規 2・更新 3）、notes/practitioner.md（新規 3・更新）
- 照合: CLAUDE.md（評価基準・根拠ルール・過去テーマ 3 件）、backlog.md（候補 8・却下 7）、reports/2026-11-25.md
- critic が今回自分で取得して確認した URL: 末尾の「確認した URL」10 件

## 結論サマリ

| ID | 候補 | 判定 | 一言 |
|---|---|---|---|
| T1 / P2（統合） | Skills over MCP（public beta） | **条件付き（現状は保留が妥当）** | 事実はすべて一次情報で裏付けられた。ただし社内で skills を返すサーバーが 1 つもなく、Team 組織では既定で OFF なので「明日試せる」が成り立たない。T1 の「`PreSkillShellCommand` で配られた skill を守る」は**誤り**（MCP skill では `!` が実行されない） |
| P1 | `PreSkillShellCommand` hook | **条件付き（既出「許可確認との付き合い方」に統合するのが第一案）** | 根拠・デモとも確実。単独枠では対象者が狭い。T1 への統合は筋が悪い |
| P3 | gh v2.106.0 の脆弱性修正と `gh auth logout` | **却下（単独枠として）** | 中身は「上げよう」の 1 行。勉強会を待たずに社内チャットで周知すべき内容。保留中の `gh event listen` 枠に載せるのも不適 |
| T2 | claude-code-action `allow_fork_prs` 既定 false | **却下** | 社内で action を使っているか未確認で、private 中心なら fork の影響はほぼない。TOP1 に足すと要点が 3 つを超える |

新規候補で、そのまま採用できるものはない。

---

## T1 / P2: Skills over MCP（public beta）

### 既出チェック
- 過去テーマ 3 件とは重ならない。
- 却下済みの「Gem → skills」（Gemini アプリ）、「Dart package skills」（pub 同梱）とは仕組みが別。ただし「skills」を名前に含む候補はこれで 3 つ目。リードはタイトルで区別がつくようにすること（「MCP サーバーから配られる skill」など）。
- backlog の「Backlog MCP Server」（保留）と隣接する。ただし Backlog MCP は skills 非対応なので、統合してもデモはつながらない。trend-scout の判断は妥当。

### 根拠チェック（一次情報。critic が取得して確認）
- changelog 2.1.332（2026-11-25）の Added に "Added skills from MCP servers (beta). They show as `server:skill` in the `/` menu" があることを確認した。**2.1.331 の項目に MCP skills の記載はない**。
- mcp.md の "Use skills from MCP servers" 節に、次の記述があることを確認した。
  - "MCP skills are in public beta and require Claude Code v2.1.331 or later."
  - "With `/login` on a Team or Enterprise plan, they're off until your organization turns them on."
  - 有効化の手順。Owner が Organization settings > Connectors で **Skills over MCP** をオンにし、Skills 設定もオンにする。connector 以外のサーバーは `disableDirectMcpSkills: false`
  - `Skill(mcp__docs__*)` / `Skill(docs:pdf)` のルール表、「Allow this version」、スキャンの対象（Directory の第三者 connector と、Enterprise のカスタム connector だけ）
  - 非対応の環境（cloud sessions、`claude -p`、GitHub Actions、Agent SDK、クラウドプロバイダ、HIPAA / ZDR）
  - **両ノートとも書いていない条件が 2 つある**: (a) Fable、または Opus 4.8 / Sonnet 5 / Haiku 5.5 以降のモデルが必要 (b) Anthropic に直接接続し、feature flag fetching をオンにする必要がある（`DISABLE_TELEMETRY` などを設定していると動かない。`/status` の Features 行で確認）。
  - "During the beta, Anthropic keeps skills off for some connectors, and no setting changes that."
- **版ずれ**: changelog は 2.1.332、docs は 2.1.331 以上。2.1.331 の changelog に記載がない以上、スライドとデモは「2.1.332 以上」で統一すべき。「2.1.331 から使える」と言ってはいけない。
- skills.md で「MCP skill では `!` コマンドが実行されない（"they don't run"）、`@` 参照は添付されない、`allowed-tools` や `hooks` などの frontmatter は無視される」ことを確認した。P2 の記述は正しい。
- **T1 の誤り（重要）**: T1 は構成案 (3) と「関連」で、`PreSkillShellCommand` を Skills over MCP の防御策として扱っている（「配られた skill が勝手にコマンドを実行しないか」）。しかし MCP skill では、もともと `!` が実行されない。`PreSkillShellCommand` が守るのは、ローカル・プラグイン・組み込みの skill とコマンドである。MCP skill の本当のリスクは、docs の警告にあるとおり「skill の指示文で Claude を誘導すること（prompt injection）」と「サーバー側がいつでも中身を変えられること」であり、hook では止められない。T1 の構成のままでは誤情報を発表することになる。
- **デモ用サーバー**: 両ノートの「見つからない」は更新できる。critic が github.com で確認したもの。
  - `modelcontextprotocol/experimental-ext-skills`: Skills extension（SEP-2640）の本体。base protocol は 2026-07-28 で、Beta 段階（"may make breaking changes"）。実行できるサンプルサーバーがあるかは、README からは確認できなかった。
  - `olaservo/skills-over-mcp-demo`: 個人のデモサーバー。v2 TypeScript SDK を使い、`dice-roller` などの skill が入っている。`npm install && npm run build && node dist/http.js --port 3000` でローカルに立てられる。公開エンドポイント（Hugging Face Space）もある。ただし README に Claude Code の記載はなく、**Claude Code で `server:skill` として表示されるかは未検証**。コミットは 7 件で、保守されるかも分からない。
  - したがってデモは「自作が必要」から「既存のデモサーバーで成立する可能性がある（要リハーサル）」に変わる。第三者の公開エンドポイントは社内デモで使わず、ローカルで立てること。

### 時間チェック
- P2 の構成（違い 4 分 / 管理者の設定 6 分 / 非対応環境 5 分）は、デモがほとんどなく、設定項目の読み上げになっている。15 分で話せはするが、CLAUDE.md の「デモできる」「すぐ試せる」に合わない。
- 非対応環境の一覧で 5 分使うのは多すぎる。1 枚の表にして 1 分で済ませるべき。

### 対象チェック
- **ここが一番弱い**。社内で使っている MCP サーバー（Backlog MCP、GitHub MCP）は、どちらも skills を返すことが確認できていない。つまり、組織で有効にしても今日の時点で受け取る skill はない。「有効にするか決める判断材料」は、実際には Owner（管理者）1〜数名向けの話で、参加者の多くには「将来こうなる」で終わる。
- Team 組織では Owner が有効にするまで OFF なので、参加者が翌日に試すこともできない（個人の Pro / Max に切り替えれば試せるが、会社の端末で個人プランを使うことは社内ルールに関わる）。

### 結論: 条件付き（現状は保留が妥当）
直す点（すべて満たせば採用を検討してよい）。
1. **`PreSkillShellCommand` をこの枠から外す**。もし触れるなら「MCP skill では `!` が動かないので、この hook は関係しない」と正しく説明する。
2. 対応版は「2.1.332 以上」で統一する。モデルと feature flag fetching の条件を追加する。
3. デモは `olaservo/skills-over-mcp-demo` をローカルで立て、次を事前にリハーサルで確認する: `/` に `server:skill` が出るか → `/permissions` で `Skill(mcp__<server>__*)` を ask / deny にしたときの違い → サーバー側で SKILL.md を書き換えて「Allow this version」の再承認が出るか。最後の再承認は「サーバーが中身を変えられる」リスクを見せられるので、一番価値のあるデモになる。
4. 人間に確認する: 社内 Team 組織の Owner が、デモのため（またはデモ後に）Skills over MCP を有効にする意思があるか。なければ、参加者は持ち帰って試せない。
- 採点の目安（critic 案）: 新しさ 5 / 実務への効き 2〜3（現時点で社内の配布元がない）/ 15 分 4 / デモ映え 2（リハーサルで確認できれば 3）= **13〜15**。現在の TOP3（18 / 17 / 16）には届かない。T1 の自己評価「本命」は甘い。

---

## P1: `PreSkillShellCommand` で skill の `!` を検査する

### 既出チェック
- 過去テーマとは重ならない。
- backlog の「Claude Code の許可確認との付き合い方（auto mode + 無人 deny 設定）」（15 点・次点）と同じ領域（どこで止めるか）にある。重複ではないが、別々の枠にすると、参加者から見ると同じ話を 2 回聞くことになる。

### 根拠チェック（hooks.md の PreSkillShellCommand 節を critic が取得して確認）
- "requires Claude Code v2.1.332 or later"。2.1.331 では `Unknown hook event "PreSkillShellCommand" was ignored` の警告が出る。`requiredMinimumVersion` についての記述も確認した。
- matcher は `Bash|PowerShell`。async の hook はブロックできない。`onFailure: "block"` を推奨。
- exit 2 / `permissionDecision: "deny"` / `decision: "block"` でブロックすると、skill は読み込まれ、出力の位置には `[not run: blocked by hook: <reason>]` が入る。**`ask` / `defer` / `updatedInput` も deny と同じ扱いになる**（P1 の「ブロック専用」は正しい）。
- 入力の `command_name` / `command_source` / `invoked_by` も確認した。ただし docs の `command_source` の例は `userSettings` / `plugin` / `builtin`。P1 の書いた `projectSettings` は docs の例にはない（存在しそうだが、未確認として扱うこと）。
- auto mode で、読み込み後に Claude がツールとして再実行すると PreToolUse 側で発火する。そのため「keep your script on both events」とあり、P1 の記述と一致する。
- テスト手順（`.claude/skills/hook-test/SKILL.md`、`/hook-test`、期待される出力 `Not run in /hook-test: echo 'rm -rf' (blocked by hook: ...)`）も docs どおり。**デモが確実に成立する**のが、この候補の一番の強み。
- 補足（P1 に書かれていない点）: skills.md には、以前から `disableSkillShellExecution: true` で `!` の実行そのものを止める設定がある。「これまでは止められなかった」という言い方は**不正確**。正しくは「全部止めるか、何も止めないかしか選べなかったのが、コマンドごとに検査できるようになった」。スライドで盲点を強調しすぎないこと。

### 時間チェック
- 4 / 7 / 4 分の構成は無理がない。デモの準備は docs の手順をなぞるだけで 15 分程度でできる見込み（推測）。
- ただし構成 (3) の「両方の hook に登録する」「requiredMinimumVersion」は細かく、前提として PreToolUse hook を知らない人には説明が必要になる。参加者が hooks を使ったことがなければ、前提の説明で 3〜4 分かかる。

### 対象チェック
- `!` 埋め込みを使う skill を書いている人、または他人の skill やプラグインを入れる人に限られる。モバイル / Web / インフラのどれにも同じように関係するが、全体の割合は小さい（推測）。実務への効き 3 は妥当で、甘くはない。

### T1 との統合案について
- **反対**。上で書いたとおり、MCP skill では `!` が実行されないので、P1 の hook は T1 の脅威に効かない。1 枠にすると「MCP で配られた skill を hook で守れる」という誤解を招く。

### 既出「許可確認との付き合い方」への統合案について
- **賛成（第一案）**。auto mode の deny 設定 → `/permissions` が無効なルールを拒否するようになった点（2.1.332、changelog で確認済み）→ skill の `!` は PreToolUse を通らないので `PreSkillShellCommand` も登録する、という流れで「止める場所の全体像」になる。既出候補に 11/25 の新要素が 2 つ加わり、新しさを補強できる。
- 条件: 要点が 3 つを超えないように、既出候補の「無人実行の deny 設定」の部分を短くし、`PreSkillShellCommand` を 4 分のパートとして入れる。デモは `/hook-test` の 1 本だけにする。

### 結論: 条件付き
- 第一案: 「許可確認との付き合い方」に 4 分パートとして統合する。統合後の既出候補は、新しさ +1 の余地がある（リードの判断）。
- 第二案（単独枠にする場合）: 「盲点」という言い方を「`disableSkillShellExecution`（全部止める）と `PreSkillShellCommand`（選んで止める）の使い分け」に直す。`projectSettings` の例は外すか、実機で確認する。自己採点 17 は高すぎる。新しさ 5 / 実務への効き 3 / 15 分 3（hooks の前提説明が要る）/ デモ映え 4（端末の 1 行が出るだけ）= **15** が妥当。

---

## P3: gh v2.106.0 の脆弱性 7 件修正と `gh auth logout` のトークン失効

### 既出チェック
- 既出の `gh event listen`（14 点・保留）と同じ gh だが、内容は無関係。v2.106.0 での event の変更は docs 1 行（「同じイベントが 2 回以上届くことがある」）だけ。

### 根拠チェック（リリースページを critic が取得して確認）
- 7 件の GHSA ID と内容、"update `gh` to version `v2.106.0` as soon as possible"、`gh auth logout` の revoke と `--no-revoke`、Git credential helper から HTTPS 資格情報を消す点は、すべて一致した。
- 深刻度はリリースページに書かれていない（P3 の注意書きのとおり）。`GH_TOKEN` で渡したトークンを revoke するかどうかも書かれていない。
- 一次情報としては十分。

### 時間チェック
- 15 分は持たない。7 件の GHSA のうち 4 件は「端末の制御文字のエスケープ漏れ」で、同じ話の繰り返しになる。デモの「logout でトークンが失効する」は画面上の変化が小さく、`GH_TOKEN` の挙動が未確認のまま実演するのは危険。

### 対象チェック
- gh を使う人は多い（GitHub を全員が使う前提は妥当）。ただし参加者がやることは「アップデートする」だけで、15 分の発表は要らない。

### `gh event listen` 枠に入れる案について
- **反対**。`gh event listen` は preview・ウェイトリスト制で保留中であり、いつ発表されるか分からない。セキュリティ更新を、発表されるか分からない枠の冒頭に載せると、周知が遅れるだけ。

### 結論: 却下（単独枠として）
- 理由: 持ち帰れるものが「v2.106.0 に上げる」「`gh auth logout` を使うスクリプトを洗い出す」の 2 行で、15 分の内容がない。
- 代わりの提案: 勉強会とは別に、**すぐに社内チャットで周知する**（「gh を v2.106.0 以上に。`gh auth logout` はトークンを失効させるようになった。旧挙動は `--no-revoke`」）。どうしても勉強会で触れるなら、skills 系の枠（P1 統合後の許可確認枠など）で「`gh skill update` が同名の別リポジトリの skill を入れる問題（GHSA-q2rq-2r2v-2jm5）」を 1 枚、サプライチェーンの例として使う程度にとどめる。

---

## T2: claude-code-action v1.0.288 の `allow_fork_prs` 既定 false

### 既出チェック
- 既出の `/code-review` は Claude Code 本体（ローカル / マネージド）で、プロダクトが違う。過去テーマとも重ならない。

### 根拠チェック（リリースページと PR #2114 を critic が取得して確認）
- v1.0.288（11/23）の breaking change は "Skip pull requests from forks by default"（#2114、11/23 にマージ）の 1 件だけ。
- PR #2114 で、次の点を確認した。
  - スキップしたときはステップが成功（緑）になり、警告をログに出すだけ
  - "A required check that only runs the action passes without a review"
  - issues / `workflow_dispatch` / `schedule` には影響しない
  - `@v1` には次のリリースで入る
  - `allow_fork_prs: "true"` で元に戻せる
  - カスタム `github_token` に `pull-requests: read` がないと失敗する
- **T2 に書かれていない点**: `pull-requests: read` のエラーは fork に限らない。PR #2114 には、コメントで起動したときに PR を調べる処理が「same-repository branches でも」行われるとある。つまり、**private リポジトリで fork を使っていなくても**、カスタムトークンの権限が足りなければ、これまで動いていたワークフローが失敗する。社内への影響があるとすれば、fork よりもこちら。
- PR は private リポジトリについて、「fork のイベントは skip の前に write / bot のチェックを通り、拒否されると失敗する」と書いているだけで、「private なら影響なし」とは書いていない。「private 中心なら影響小」は推測として扱うこと（org で private リポジトリの fork を許可しているかによる）。

### 時間チェック
- デモには fork 用の別アカウントと API キーが必要で、見せられるのは「緑で終わるログ」だけ。15 分の枠としては内容が足りない。

### 対象チェック
- 社内で claude-code-action を使っているか**未確認**。使っていなければ対象者は 0 人。使っていても、関係するのはワークフローを管理する数人。

### TOP1（`/code-review`）に 1 枚足す案について
- **反対**。TOP1 はすでに「`/code-review` + `--fix` + `/loop` の CI 見張り」で要点が 3 つある。別プロダクトの CI 設定を足すと 4 つ目になり、「15 分で話せるか」を下げる。

### 結論: 却下
- 理由: 社内での利用が未確認で、対象者がほとんどいない可能性が高い。デモの見せ場がない。15 分の内容がない。
- 代わりの提案: 社内で claude-code-action を使っているか人間に確認する。使っていれば、勉強会ではなく運用の周知で「`@v1` 固定なら挙動が変わった。カスタム `github_token` に `pull-requests: read` があるか確認」と伝える。

---

## 既出候補の更新が評価を変えるか

| 既出候補 | 更新 | critic の判断 |
|---|---|---|
| ランナーイメージ（15 点・次点・条件付き） | #15373（11/20、pin 済み）。`windows-2025-vs2026` / `windows-11-vs2026-arm` は 12/1 から非推奨化。critic が取得して確認した | **点は上げない**。issue の本文自体が「ラベルの更新は不要」と「`runs-on` を更新せよ」で矛盾している。さらに 11/22 のコメントに「VS 2027 のライフサイクルページは 404 で、最新のイメージもまだ VS 2026」という指摘があり、内容が固まっていない。社内の Windows / C++ ビルドは少ないと思われる（推測）。12/1 の日付は 1 行の補足にとどめ、「VS 2027 になった」と断定しない |
| 許可確認との付き合い方（15 点・次点） | 2.1.332 の `/permissions` が、次回起動時に無視されるルール（例: ツール名のない `rm -rf`）を拒否して理由を出す。changelog で確認した | **上げる方向**。P1 を統合すれば、11/25 の新要素が 2 つ入り、新しさを補強できる。統合した場合はリードが再採点すること（目安 +1） |
| `/code-review`（TOP1） | 2.1.332 で「routine」が「cloud scheduled task」に言い換えられた（`/routines` は引き続き使える）。changelog で確認した | **点は変わらない**。`/loop` パートのスライドの用語を「cloud scheduled task」に直すことだけ条件に追加する |
| VS Code 拡張（TOP3） | 2.1.332 で、サイドバーの `/rewind` が self-paced `/loop` を止めるときの警告が追加された。changelog で確認した | **点は変わらない**。デモで `/rewind` を使わない限り関係ない |
| `gh event listen`（保留） | v2.106.0 での変更は docs 1 行だけ | **変わらない**。保留のまま。P3 を載せる案には反対（上記） |
| Backlog MCP Server（保留） | v0.23.4 は依存更新だけ（trend-scout の記述。critic は未確認） | **変わらない**。Skills over MCP には非対応なので、T1 との統合でも点は上がらない。CVE-2026-111768 は引き続き NVD / GHSA で確認が必要 |

---

## リードへの申し送り

1. 新規候補から TOP3 を入れ替える必要はない。今回の一番の収穫は、P1 を「許可確認との付き合い方」に統合して既出候補を強くすること。
2. T1 / P2 は統合し、P2 の構成をもとにする（P2 は `!` が無効なことを正しく書いている）。T1 の `PreSkillShellCommand` パートは必ず削除する。人間の確認（Owner が有効化するか）が取れるまでは保留にする。
3. P3 は勉強会の候補ではなく、全社への周知として人間に提案する。
4. 人間に確認すること（追加分）
   - 社内 Team 組織の Owner が Skills over MCP を有効にするつもりがあるか
   - 社内で claude-code-action を使っているか。使っているなら、カスタム `github_token` を使っているか
   - 社内に gh を使っている共用マシンや CI スクリプトがあるか（`gh auth logout` の挙動変更の影響）

---

## 確認した URL（critic が今回取得したもの）

- https://code.claude.com/docs/en/changelog （2.1.332 の Added / Changed / Improvements。2.1.331 に MCP skills の記載がないこと）
- https://code.claude.com/docs/en/mcp.md （"Use skills from MCP servers" 節の全文。10 万字以降を読んだ）
- https://code.claude.com/docs/en/hooks.md （PreSkillShellCommand 節。10 万〜20 万字の範囲）
- https://code.claude.com/docs/en/skills.md （MCP skill で無効になる機能、`disableSkillShellExecution`）
- https://github.com/anthropics/claude-code-action/releases/tag/v1.0.288
- https://github.com/anthropics/claude-code-action/pull/2114
- https://github.com/cli/cli/releases/tag/v2.106.0
- https://github.com/actions/runner-images/issues/15373
- https://github.com/modelcontextprotocol/experimental-ext-skills （Skills extension 本体、Beta、base revision 2026-07-28）
- https://github.com/olaservo/skills-over-mcp-demo （デモサーバーの候補。Claude Code での動作は未検証）

検索のみ（WebSearch の結果で見ただけ。本文は未取得）: modelcontextprotocol.io/extensions/skills/overview、modelcontextprotocol.io/seps/2640-skills-extension、aaif.io/blog/skills-over-mcp、pkg.go.dev/github.com/panyam/mcpkit/ext/skills

## 取得できなかった URL

- なし（critic が取得を試みた URL はすべて取得できた）。
- 取得を試していないもの: modelcontextprotocol.io（両ノートで ENOTFOUND の報告。仕様の一次情報は、代わりに github.com/modelcontextprotocol/experimental-ext-skills で確認した）、https://github.com/nulab/backlog-mcp-server/releases（v0.23.4 の記述は trend-scout の確認のまま）、https://github.com/actions/runner-images/issues/15368
- hooks.md の 20 万字以降と skills.md の 10 万字以降は読んでいない。PreSkillShellCommand が MCP skill に対して発火するかは、hooks.md の該当節には書かれていなかった（skills.md では MCP skill の `!` は実行されないとあるので、発火する対象がない）。
