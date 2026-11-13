# study-topics

勉強会ネタを Claude Code の Cloud スケジュールタスクで定期調査するリポジトリ。

## セットアップ
1. このリポジトリを GitHub に push（private 推奨）
2. CLAUDE.md の TODO を埋める
3. Claude Code CLI でこのリポジトリを開き `/schedule`、または claude.ai/code/routines で新規作成
   - プロンプト: TASK_PROMPT.md の区切り線の間を貼る
   - リポジトリ: このリポジトリ
   - スケジュール: プリセットで平日 9:07（ローカル時刻で入力）
   - ネットワーク: Custom で調査先ドメインを許可、または Full
   - コネクタ: Google Chat など投稿先だけ残し、他は外す
4. 詳細ページの Run now で試運転し、History からトランスクリプトを確認

## 運用
- 発表に採用したネタは backlog.md の「採用済み」へ
- 感想・方針は backlog.md の「フィードバック」へ（次回から採点に反映される）
