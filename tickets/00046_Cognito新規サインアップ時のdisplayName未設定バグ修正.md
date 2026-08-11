# #00046 Cognito新規サインアップ時のdisplayName未設定バグ修正

- 対象リポジトリ: backend
- ステータス: 未対応
- type: fix
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md) / [#00043 frontendからdevバックエンドへの接続](./00043_frontendからdevバックエンドへの接続.md) / [backendリポジトリ docs/API設計書/GET_me.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_me.md)

## 実現すること

[#00043](./00043_frontendからdevバックエンドへの接続.md)のdev環境動作確認中に発見した不具合を修正する。Cognito Hosted UIの標準サインアップ画面はemail・passwordのみを収集し`name`属性を設定しないため、`UserProvisioningService.createUser()`（backend）が`AppUser.displayName`を`null`のまま永続化してしまう。`GET /api/v1/me`のレスポンス契約（`docs/API設計書/GET_me.md`）では`displayName`は必須文字列だが、実際には`null`が返り、frontend（`src/auth.ts`の`meResponseSchema`、`displayName: z.string()`）でのレスポンス検証に失敗してログインフローがブロックされる。つまりHosted UIの標準フローで初めてサインアップする実ユーザーは全員ログインが完了しない。

さらに`UserProvisioningService.provisionFromToken()`は`cognito_sub`で既存行が見つかった場合はCognitoへ再問い合わせしない設計（初回アクセス時のみ`GetUser`を呼ぶ）のため、一度`displayName=null`で作成された`app_user`行は、以後何度ログインし直しても同じ壊れたレスポンスを返し続ける。

- 修正方針: Cognitoの`name`属性が存在しない場合、email のローカル部（`@`より前）等の妥当なフォールバック値を使い、`AppUser.displayName`を必ず非null文字列にする（`CognitoUserAttributesClient`または`UserProvisioningService`のどちらに実装するかは着手時に判断）
- 既存にすでに`displayName=null`で作成済みの`app_user`行（dev環境）への対応要否・方法（DB直接更新 or 自然解消は不可のため何らかの手当てが必要）は着手時に確認する

## 受け入れ条件

- [ ] Cognitoの`name`属性が存在しない新規ユーザーでも、`GET /api/v1/me`の`displayName`が非nullの文字列で返る
- [ ] `displayName`のフォールバック挙動をUnit Testで検証する（`UserProvisioningServiceTest`等）
- [ ] dev環境で、Hosted UIの標準サインアップフロー（email+passwordのみ）から新規ユーザーがログイン〜`GET /api/v1/me`成功〜ダッシュボード表示まで到達することを実際に確認する
- [ ] 既存にdisplayName=nullで作成済みのdev環境`app_user`行があれば、あわせて解消する
- [ ] `./gradlew spotlessCheck test integrationTest`が通る
- [ ] markdownlintが通る（変更したdocsがあれば）

## 作業ログ

- 2026-08-12 チケット起票（#00043のdev環境動作確認中に発見）
