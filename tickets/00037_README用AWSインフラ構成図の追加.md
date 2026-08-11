# #00037 README用AWSインフラ構成図の追加

- 対象リポジトリ: root
- ステータス: 未対応
- type: docs
- 関連ドキュメント: [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

`README.md`はプロジェクト紹介用（ポートフォリオ）リポジトリのトップページであるにもかかわらず、現状は図が一切なくテキストの技術スタック一覧のみとなっている。8章アーキテクチャ（8.1採用技術一覧・8.2全体アーキテクチャ図・8.3AWSインフラ構成図）で設計として確定しているAWSインフラ構成を、AWS公式アイコンを用いた視覚的な構成図として`README.md`に追加する。

- `aws-drawio-diagram`スキルを使い、8.2・8.3のMermaid内容（VPC内でPublic Subnet＝ALB/NAT Gateway、Private Subnet＝ECS Fargate（web/api）/RDS PostgreSQL、Cognito・S3・Polly・OpenAI API・Internetとの接続関係）を統合した`.drawio`を作成し、SVG（必要ならPNGも）として書き出す
- 図ファイルは`docs/diagrams/`配下に格納し、`README.md`から画像として参照する
- システム要件定義書側のMermaidテキスト図（8.2/8.3）はそのまま残し、重複した設計変更は行わない（README側は視覚表現の追加のみ）

## 受け入れ条件

- [ ] `docs/diagrams/`配下にAWS公式アイコンを用いた`.drawio`ファイルとSVG（またはPNG）書き出しを追加する
- [ ] 図にはVPC／Public Subnet（ALB, NAT Gateway）／Private Subnet（ECS Fargate x2: web/api, RDS PostgreSQL）／Cognito／S3／Polly／OpenAI API／Internetを含み、8.2・8.3の内容と整合する
- [ ] `README.md`に図を埋め込み、GitHub上で正しく表示されることを確認する
- [ ] markdownlintが通る

## 作業ログ

- 2026-08-11 チケット起票
