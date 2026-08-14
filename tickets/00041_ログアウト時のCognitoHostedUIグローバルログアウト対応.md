# #00041 ログアウト時のCognito Hosted UIグローバルログアウト対応

- 対象リポジトリ: frontend
- ステータス: 完了
- type: fix
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md)

## 実現すること

`src/components/app-shell/app-nav-user-menu.tsx`のログアウト処理は`signOut({ callbackUrl: "/login" })`のみで、NextAuthのローカルセッションCookieのみを消去する。Cognito Hosted UI側のセッションCookieは消えないため、共有/公共PCでログアウトした後に再度ログインボタンを押すと、資格情報入力なしにCognitoのSSOで無言再認証されてしまい、ログアウトの意図が達成されない（チケット#00034でも既知の先送り事項としてS-02設計書に記載済み）。

修正方針:

1. ログアウト時にNextAuthのローカルセッションクリアに加えて、Cognito Hosted UIのログアウトエンドポイント（`https://<domain>/logout?client_id=...&logout_uri=...`）へのリダイレクトを行う
2. S-02設計書の「今後の改善点」記載を「対応済み」に更新する

## 受け入れ条件

- [ ] ログアウト操作でNextAuthセッションとCognito Hosted UIセッションの両方がクリアされる（NextAuthセッション破棄とCognito logoutエンドポイントへの正しいリダイレクトは確認済み。実Cognitoセッションが実際にクリアされる点は、実AWS環境にCognito User Poolが未払い出しのため未確認）
- [ ] ログアウト後に再度ログインボタンを押すと、Cognitoの認証画面（またはHosted UI）が改めて表示され、無言でSSO再認証されないことを手動確認する（#00038/#00039/#00040と同じ既知の残課題により未確認）
- [x] S-02設計書の記載を実装状況に合わせて更新する
- [x] `npx tsc --noEmit` / `npm run lint` / `npm run build` / `npm run test`が通る

## 作業ログ

- 2026-08-11 起票
- 2026-08-11 新規Route Handler `GET /api/auth/cognito-logout`を実装。サーバー側`signOut({ redirect: false })`でNextAuthのローカルセッションを破棄した上で、Cognito Hosted UIのグローバルログアウトエンドポイント（`/logout?client_id=...&logout_uri=...`）へリダイレクトする。`COGNITO_HOSTED_UI_DOMAIN`未設定時は`/login`へのリダイレクトにフォールバックする。`AppNavUserMenu`のログアウトボタンをこの新エンドポイントへの`window.location.href`遷移に変更（`next-auth/react`の`signOut()`は廃止）。`.env.example`に`COGNITO_HOSTED_UI_DOMAIN`を追加。テスト追加（Route Handler・ボタン挙動）。`npx tsc --noEmit` / `npm run lint` / `npm run build` / `npm run test`（72件）すべてグリーン。dev serverでCognito環境変数を仮設定した状態で`GET /api/auth/cognito-logout`が正しいCognito logout URL（`client_id`・`logout_uri`付き）へ307リダイレクトすること、および環境変数未設定時に`/login`へフォールバックすることの両方を実際に確認。S-02設計書の「今後の改善点」記載を対応済みに更新。[PR #33](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/33)を作成しマージ済み
