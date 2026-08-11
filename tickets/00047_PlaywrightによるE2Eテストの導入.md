# #00047 PlaywrightによるE2Eテストの導入

- 対象リポジトリ: frontend
- ステータス: 未対応
- type: test
- 関連ドキュメント: [#00031 画面（S-01〜S-09）単体テストの導入](./00031_画面（S-01〜S-09）単体テストの導入.md) / [#00043 frontendからdevバックエンドへの接続](./00043_frontendからdevバックエンドへの接続.md) / [#00044 backendのAWSインフラ構築とSupabase接続](./00044_backendのAWSインフラ構築とSupabase接続.md) / [#00046 Cognito新規サインアップ時のdisplayName未設定バグ修正](./00046_Cognito新規サインアップ時のdisplayName未設定バグ修正.md) / [docs/画面遷移図.md](../docs/画面遷移図.md)

## 実現すること

[#00044](./00044_backendのAWSインフラ構築とSupabase接続.md)でdev環境のAWSインフラ（ECS Fargate Spot + API Gateway + Supabase、エンドポイント `https://g8r1slo0d0.execute-api.ap-northeast-1.amazonaws.com`）が、[#00043](./00043_frontendからdevバックエンドへの接続.md)でフロントエンドからの接続（ローカルfrontend + devバックエンド構成）が整い、実AWS環境に対する動作確認が安定して行える状態になった。これまでE2E相当の検証は#00043の作業中にPlaywright MCPで1回通しの手動確認をした程度にとどまっており、リポジトリにコミットされた再実行可能なE2Eテストスイートは存在しない（単体テスト基盤導入の[#00031](./00031_画面（S-01〜S-09）単体テストの導入.md)でもE2Eは明示的に対象外とされていた）。`ielts-creater-frontend`にPlaywright E2Eテストを新規導入し、認証を含む主要な画面遷移・出題〜採点フローを継続的に検証できるようにする。

- 基盤導入: `@playwright/test`をdevDependenciesに追加し、`playwright.config.ts`を新規作成する（`baseURL: http://localhost:3000`）。テストファイルは`e2e/`配下に新規配置する（Vitestのco-locateテスト`*.test.tsx`とは分離）。`package.json`に実行スクリプト（例: `test:e2e`）を追加する
- 実行環境: ローカルfrontend（`npm run dev`）+ AWS dev backend + devのCognito User Poolに接続して実行する。既存の`.env.local`（#00043で設定済みのBACKEND_API_ORIGIN・Cognito設定）をそのまま利用する
- E2E専用のCognitoテストユーザーをdev User Poolに用意する。[#00046](./00046_Cognito新規サインアップ時のdisplayName未設定バグ修正.md)が未修正の間はHosted UI標準サインアップ経由でdisplayNameがnullになるため、AWS管理者権限で`name`属性を設定済みの状態で作成する。認証情報はテストコードにハードコードせず環境変数経由で渡す
- カバーするフロー:
  - コアフロー: ログイン（既存テストユーザー）→S-07ダッシュボード→S-03問題生成（Reading）→S-04回答→S-05結果/採点。#00043で手動確認済みの経路を自動テスト化する
  - 新規サインアップ（S-02、Cognito Hosted UI標準サインアップ）: [#00046](./00046_Cognito新規サインアップ時のdisplayName未設定バグ修正.md)の既知バグにより現状は初回ログインが完了しない。本チケットでは**その失敗を検証するテストとして先に書く**（バグ修正を前提にはしない）。#00046修正後は本テストを成功系に更新する
  - S-06履歴一覧からの再受験（→S-04）・結果再確認（→S-05）の2経路
  - 静的画面（S-01 Top / S-08 プライバシーポリシー / S-09 利用規約）の表示・フッター相互遷移確認
- 対象外: GitHub Actions CI組み込み（dev Cognitoテストユーザー等のsecrets管理を含む運用整備は別チケット）、[#00046](./00046_Cognito新規サインアップ時のdisplayName未設定バグ修正.md)の根本修正（別チケットで対応する）、Listening（音声再生を伴う）・Writing/Speaking（未実装）のE2Eカバレッジ、visual regressionテスト、モバイル解像度でのE2E

## 受け入れ条件

- [ ] `@playwright/test`が導入され、`playwright.config.ts`（`baseURL: http://localhost:3000`）が追加されている
- [ ] `e2e/`配下にテストファイルが配置され、`package.json`に実行スクリプト（`test:e2e`等）が追加されている
- [ ] コアフロー（ログイン→ダッシュボード→問題生成(Reading)→回答→結果）のE2Eテストが実装され、ローカルでdev backend/Cognitoに接続してパスする
- [ ] 新規サインアップのE2Eテストが実装され、#00046の既知バグによる現状の失敗（初回ログイン未完了）を検証する
- [ ] 履歴一覧（S-06）からの再受験・結果再確認のE2Eテストが実装されている
- [ ] 静的画面（S-01/S-08/S-09）の表示・遷移確認のE2Eテストが実装されている
- [ ] README.md（frontend）にE2Eテストの実行手順（前提となる`.env.local`設定・テストユーザー準備・実行コマンド）を追記する
- [ ] markdownlintが通る

## 作業ログ

- 2026-08-12 チケット起票
