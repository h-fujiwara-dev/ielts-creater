# #00041 ログアウト時のCognito Hosted UIグローバルログアウト対応

- 対象リポジトリ: frontend
- ステータス: 未対応
- type: fix
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md)

## 実現すること

`src/components/app-shell/app-nav-user-menu.tsx`のログアウト処理は`signOut({ callbackUrl: "/login" })`のみで、NextAuthのローカルセッションCookieのみを消去する。Cognito Hosted UI側のセッションCookieは消えないため、共有/公共PCでログアウトした後に再度ログインボタンを押すと、資格情報入力なしにCognitoのSSOで無言再認証されてしまい、ログアウトの意図が達成されない（チケット#00034でも既知の先送り事項としてS-02設計書に記載済み）。

修正方針:

1. ログアウト時にNextAuthのローカルセッションクリアに加えて、Cognito Hosted UIのログアウトエンドポイント（`https://<domain>/logout?client_id=...&logout_uri=...`）へのリダイレクトを行う
2. S-02設計書の「今後の改善点」記載を「対応済み」に更新する

## 受け入れ条件

- [ ] ログアウト操作でNextAuthセッションとCognito Hosted UIセッションの両方がクリアされる
- [ ] ログアウト後に再度ログインボタンを押すと、Cognitoの認証画面（またはHosted UI）が改めて表示され、無言でSSO再認証されないことを手動確認する
- [ ] S-02設計書の記載を実装状況に合わせて更新する
- [ ] `npx tsc --noEmit` / `npm run lint` / `npm run build` / `npm run test`が通る

## 作業ログ

- 2026-08-11 起票
