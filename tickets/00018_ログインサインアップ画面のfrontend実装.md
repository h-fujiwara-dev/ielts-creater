# #00018 ログイン／サインアップ画面（S-02）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-02_login-signup.html](../docs/screen-drafts/s-02_login-signup.html) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ) / [frontendリポジトリ docs/画面設計書/S-02_ログインサインアップ画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-02_ログインサインアップ画面.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-02_login-signup.html`）と画面設計書の機能仕様に基づき、ログイン／サインアップ画面（S-02）を`ielts-creater-frontend`に実装する。

- ログイン／エラー／サインアップ／確認コード入力の4状態をUIとして実装する（ネイビー×ドット柄のブランドパネル＋フォームの2カラムレイアウト）
- 認証基盤はAmazon Cognito（NextAuth.jsのCognitoプロバイダ、システム要件定義書8章）を前提とし、サインイン開始・コールバック・セッションまわりの型定義とコンポーネント構造を用意する。実際のCognito User Pool疎通（infra側の環境払い出し・環境変数設定）や実APIとの接続は対象外とし、別チケットに委ねる
- クライアントサイドバリデーション（メール形式・パスワード要件・確認コード桁数等）を実装する

## 受け入れ条件

- [x] ログイン／エラー／サインアップ／確認コード入力の4状態がHTML叩き台の仕様通りに実装されている
- [x] NextAuth.js Cognitoプロバイダ連携を想定した型定義（セッション、サインイン関数インターフェース等）が定義されている
- [x] クライアントサイドバリデーション（メール形式・パスワード要件・確認コード桁数）が実装されている
- [x] `npm run build`が通る
- [x] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [x] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- 2026-08-10 着手
- 2026-08-10 完了。`ielts-creater-frontend` feat/ticket-00018 で実装（PR #14）。react-hook-form + zod、next-auth(v5 beta) Cognitoプロバイダの型骨格を新規導入。認証方式（Hosted UI or カスタムAPIによる直接認証）はCognito実接続を行う別チケットに判断を先送りし、今回は受け入れ条件を優先して4状態すべてをアプリ内蔵フォームとして実装。`middleware.ts`は`(protected)`ルートが存在しないため見送り（#00019以降で対応）。TOP画面のログイン導線を`/login`へ接続
