# #00015 Spring Boot基盤の実装規約策定

- 対象リポジトリ: root / backend
- ステータス: 完了
- type: docs
- 関連ドキュメント: [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ) / [backendリポジトリ docs/実装規約.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/実装規約.md) / [backendリポジトリ docs/API一覧.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API一覧.md)

## 実現すること

backendリポジトリにSpring Bootの実プロジェクト（`build.gradle`・`src/`）を構築する前段として、実装ルールを確定し`実装規約.md`として文書化する。パッケージ構成・命名規約・ビルド構成・DBマイグレーション・テスト方針・ロギング・設定プロファイル・非同期方針を対象とする。実際のプロジェクト雛形作成（scaffolding）は別チケットで行う。

## 受け入れ条件

- [x] パッケージ構成の方針（レイヤード構成 or 機能単位構成 or マルチモジュール）を決定する
- [x] ビルド構成（Gradle DSL種別、主要プラグイン・依存関係）を決定する
- [x] DBマイグレーション管理方式（Flyway / Liquibase / ddl-auto）を決定する
- [x] テスト戦略（Unit/Integrationの手法・配置・タグ運用）を決定する
- [x] Lombok使用方針・コードフォーマッタ/静的解析ツールを決定する
- [x] backendリポジトリに`docs/実装規約.md`を作成し、`CLAUDE.md`・`docs/API一覧.md`から参照させる

## 作業ログ

- 2026-08-10 着手。AskUserQuestionでパッケージ構成（機能単位）・Gradle DSL（Groovy）・DBマイグレーション（Flyway）・テスト戦略（Mockito+Testcontainers）・Lombok（使用する）・コード規約ツール（Spotless/Google Java Format）を決定
- 2026-08-10 backendリポジトリに`docs/実装規約.md`を作成し、`CLAUDE.md`・`docs/API一覧.md`付録を更新。完了
- 2026-08-10 補足: 着手時点で並行して別ブランチ（backend側`docs/ticket-00014`、未マージ）が`docs/API一覧.md`の同一箇所（付録の実装構成テーブル）を編集中だったため、チケット番号を#00014から#00015へ繰り上げて採番。当該ブランチのマージ時に軽微なコンフリクトが発生し得るため、マージ担当者は付録セクションの扱い（実装規約.mdへの集約）に注意すること
