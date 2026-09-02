# #00071 本番SupabaseのRLS未設定エラーを解消

- 対象リポジトリ: backend
- ステータス: 未対応
- type: fix
- 関連ドキュメント:

## 実現すること

本番環境のSupabase Dashboard（Security Advisor）で、`public`スキーマ配下の全13テーブルに対して「RLS Disabled in Public」がErrorとして13件検出されている。

原因: `ielts-creater-backend`のFlywayマイグレーション（`V1__init.sql`, `V3__...sql`, `V4__add_guest_mode_support.sql`）に、テーブル作成時にRow Level Security (RLS)を有効化するSQLが一度も含まれていない（[#00037](./00037_インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加.md) でのRDS→Supabase移行時からの実装漏れ）。

backendはSupabaseのJS SDK/PostgRESTを使わず、JDBC経由で`postgres.<project-ref>`（Supabaseの`postgres`スーパーユーザーをSession Pooler経由で使用）として直接接続する構成のため、通常のアプリケーション利用ではRLSは関与しない。一方、Supabaseプロジェクトはデフォルトでpublicスキーマの全テーブルをPostgREST API経由でも公開しており、RLSが無効なままだと外部から直接テーブルを読み書きされうるセキュリティリスクとして残っている。

対応方針: 対象13テーブルに`ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`を発行するFlywayマイグレーションを追加する。backend接続ロール（`postgres`）はRLSを常にバイパスするため、`CREATE POLICY`の追加は不要で、既存機能への影響なく解消できる。

対象13テーブル: `app_user`, `flyway_schema_history`, `question_set`, `passage`, `listening_script`, `audio_segment`, `question_group`, `question`, `answer_option`, `acceptable_answer`, `attempt`, `attempt_answer`, `guest_ip_quota`

## 受け入れ条件

- [ ] 対象13テーブル全てで`rowsecurity = true`になっている
- [ ] dev/prod両方のSupabaseプロジェクトで、Security Advisorの当該13件のErrorsが解消されている
- [ ] 既存のAPI・DBアクセス機能に影響がないことをdev環境で確認済み
- [ ] backendでtest/buildが通る

## 作業ログ

- 2026-09-02 起票
