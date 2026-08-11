# #00037 README用インフラ構成図の追加

- 対象リポジトリ: root
- ステータス: 完了
- type: docs
- 関連ドキュメント: [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

`README.md`はプロジェクト紹介用（ポートフォリオ）リポジトリのトップページであるにもかかわらず、現状は図が一切なくテキストの技術スタック一覧のみとなっている。8章アーキテクチャ（8.1採用技術一覧・8.2全体アーキテクチャ図・8.3AWSインフラ構成図）で設計として確定しているAWSインフラ構成を、視覚的な構成図として`README.md`に追加する。

- 8.2・8.3のMermaid内容（VPC内でPublic Subnet＝ALB/NAT Gateway、Private Subnet＝ECS Fargate（web/api）/RDS PostgreSQL、Cognito・S3・Polly・OpenAI API・Internetとの接続関係）を1枚に統合し、Mermaid `flowchart`として`README.md`に直接追加する（GitHub上でそのままレンダリングされるため、追加の画像生成・書き出し作業は不要）
  - 当初はAWS公式アイコンによる`.drawio`＋SVG画像案（`aws-drawio-diagram`スキル使用）で検討したが、正確な`resIcon`名を1つずつdraw.io上で確認する調査コストが大きくトークンを消費するため、既存の8.2/8.3と同じMermaid形式に変更した（ユーザー判断）
- システム要件定義書側のMermaidテキスト図（8.2/8.3）はそのまま残し、重複した設計変更は行わない（README側は同内容を1枚に統合した表示に特化）

## 受け入れ条件

- [x] `README.md`にインフラ構成のMermaid `flowchart`を追加する
- [x] 図にはVPC／Public Subnet（ALB, NAT Gateway）／Private Subnet（ECS Fargate x2: web/api, RDS PostgreSQL）／Cognito／S3／Polly／OpenAI API／Internetを含み、8.2・8.3の内容と整合する
- [x] `README.md`に図を埋め込み、GitHub上で正しく表示されることを確認する
- [x] markdownlintが通る

## 作業ログ

- 2026-08-11 チケット起票
- 2026-08-11 当初のAWS公式アイコン構成図（draw.io）案は、正確なアイコン名の調査にトークンを要するためユーザー判断でMermaid図に変更。`README.md`に「インフラ構成」節を新設し、システム要件定義書8.2/8.3を統合したMermaid `flowchart`を追加。markdownlint通過を確認
