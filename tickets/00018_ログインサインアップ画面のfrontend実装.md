# #00018 ログイン／サインアップ画面（S-02）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 未着手
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-02_login-signup.html](../docs/screen-drafts/s-02_login-signup.html) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ) / [frontendリポジトリ docs/画面設計書/S-02_ログインサインアップ画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-02_ログインサインアップ画面.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-02_login-signup.html`）と画面設計書の機能仕様に基づき、ログイン／サインアップ画面（S-02）を`ielts-creater-frontend`に実装する。

- ログイン／エラー／サインアップ／確認コード入力の4状態をUIとして実装する（ネイビー×ドット柄のブランドパネル＋フォームの2カラムレイアウト）
- 認証基盤はAmazon Cognito（NextAuth.jsのCognitoプロバイダ、システム要件定義書8章）を前提とし、サインイン開始・コールバック・セッションまわりの型定義とコンポーネント構造を用意する。実際のCognito User Pool疎通（infra側の環境払い出し・環境変数設定）や実APIとの接続は対象外とし、別チケットに委ねる
- クライアントサイドバリデーション（メール形式・パスワード要件・確認コード桁数等）を実装する

## 受け入れ条件

- [ ] ログイン／エラー／サインアップ／確認コード入力の4状態がHTML叩き台の仕様通りに実装されている
- [ ] NextAuth.js Cognitoプロバイダ連携を想定した型定義（セッション、サインイン関数インターフェース等）が定義されている
- [ ] クライアントサイドバリデーション（メール形式・パスワード要件・確認コード桁数）が実装されている
- [ ] `npm run build`が通る
- [ ] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [ ] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- YYYY-MM-DD 着手
