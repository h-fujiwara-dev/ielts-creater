# #00062 TOP画面ボタンでCognitoサインイン/サインアップ画面を出し分け

- 対象リポジトリ: frontend / infra
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md) / [#00036 TOP画面のボタン未配線修正](./00036_TOP画面のボタン未配線修正.md) / [#00042 認証まわりのコード品質整理](./00042_認証まわりのコード品質整理.md)

## 実現すること

TOP画面（S-01）の「ログイン」ボタンと「無料ではじめる」ボタンは、現状どちらを押しても遷移後のCognito認証画面が完全に同一になっている。

経緯: [#00036](./00036_TOP画面のボタン未配線修正.md)で「無料ではじめる」ボタンに`/login?step=signup`というクエリ付きリンクが実装され、当時はアプリ内蔵のログイン/サインアップフォームの初期表示を`step`で出し分けていた。その後[#00034](./00034_Cognito認証の本実装.md)で認証方式がCognito Hosted UIへの外部リダイレクト（NextAuth.js、Authorization Code + PKCE）に全面移行してアプリ内蔵フォームが撤去され、[#00042](./00042_認証まわりのコード品質整理.md)でその残骸（`flow-step.ts`のsignup/confirmCodeステップ）がデッドコードとして削除された際に、`step`クエリパラメータを読む処理自体がどこにも残らなくなった。結果、`/login`ページは`step`を無視し、常に同一の`CognitoSignInButton`（「Cognitoでログイン／新規登録」）を表示するため、両ボタンの遷移先Cognito画面が区別できない状態になっている。

対応方針: AWS Cognito Classic Hosted UIが公式に提供する`/signup`エンドポイント（`/oauth2/authorize`と同一パラメータを受け付ける直リンク。通常のサインイン画面自体にも同経路への`Sign up`リンクが既に存在する）を使い、「無料ではじめる」経由時はサインアップ画面、「ログイン」経由時はサインイン画面へ確実に振り分ける。

具体的には、NextAuth（`frontend/src/auth.ts`）に`authorization.url`を`https://${COGNITO_HOSTED_UI_DOMAIN}/signup`へ上書きした2つ目のCognitoプロバイダ（id: `cognito-signup`）を追加し、`CognitoSignInButton`に`mode`propを持たせて`/login`ページの`step`クエリで呼び分ける。NextAuthのコールバックパスはprovider idから自動決定される（`/api/auth/callback/cognito-signup`）ため、Cognito App Clientの`callback_urls`（`infra/terraform/envs/dev`・`prod`の`variables.tf`）に新パスの追加登録が必要。

## 受け入れ条件

- [ ] `frontend/src/auth.ts`に`id: "cognito-signup"`のプロバイダが追加され、`authorization.url`が`https://${COGNITO_HOSTED_UI_DOMAIN}/signup`を指す
- [ ] `/login`（`step`未指定）のボタン押下でprovider `cognito`が呼ばれ、Cognito Hosted UIのサインイン画面（`/oauth2/authorize`）へ遷移する（従来どおり）
- [ ] `/login?step=signup`のボタン押下でprovider `cognito-signup`が呼ばれ、Cognito Hosted UIのサインアップ画面（`/signup`）へ直接遷移する
- [ ] `infra`のdev/prod Cognito App Clientの`callback_urls`に`/api/auth/callback/cognito-signup`相当のURLが追加登録されている
- [ ] `frontend/src/auth.test.ts`の`next-auth/providers/cognito`モックが渡された設定を反映するよう修正され、2プロバイダの区別を検証するテストが追加されている
- [ ] `CognitoSignInButton`・`/login`ページの`mode`/`step`出し分けを検証するテストが追加されている
- [ ] `npx tsc --noEmit` / `npm run lint` / `npm run build` / `npm run test`（frontend）がすべて通る
- [ ] `terraform plan`（infra、dev/prod）で意図した差分のみが出ることを確認する（`terraform apply`はユーザーが手動実行）
- [ ] dev serverで実際に「ログイン」「無料ではじめる」それぞれのボタンを押下し、遷移先のCognito Hosted UI画面（サインイン/サインアップ）が異なることを確認する

## 作業ログ

- 2026-08-23 着手
