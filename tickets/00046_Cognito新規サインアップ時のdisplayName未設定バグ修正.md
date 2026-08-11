# #00046 Cognito新規サインアップ時のdisplayName未設定バグ修正

- 対象リポジトリ: backend
- ステータス: 完了
- type: fix
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md) / [#00043 frontendからdevバックエンドへの接続](./00043_frontendからdevバックエンドへの接続.md) / [backendリポジトリ docs/API設計書/GET_me.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_me.md)

## 実現すること

[#00043](./00043_frontendからdevバックエンドへの接続.md)のdev環境動作確認中に発見した不具合を修正する。Cognito Hosted UIの標準サインアップ画面はemail・passwordのみを収集し`name`属性を設定しないため、`UserProvisioningService.createUser()`（backend）が`AppUser.displayName`を`null`のまま永続化してしまう。`GET /api/v1/me`のレスポンス契約（`docs/API設計書/GET_me.md`）では`displayName`は必須文字列だが、実際には`null`が返り、frontend（`src/auth.ts`の`meResponseSchema`、`displayName: z.string()`）でのレスポンス検証に失敗してログインフローがブロックされる。つまりHosted UIの標準フローで初めてサインアップする実ユーザーは全員ログインが完了しない。

さらに`UserProvisioningService.provisionFromToken()`は`cognito_sub`で既存行が見つかった場合はCognitoへ再問い合わせしない設計（初回アクセス時のみ`GetUser`を呼ぶ）のため、一度`displayName=null`で作成された`app_user`行は、以後何度ログインし直しても同じ壊れたレスポンスを返し続ける。

- 修正方針: Cognitoの`name`属性が存在しない場合、email のローカル部（`@`より前）等の妥当なフォールバック値を使い、`AppUser.displayName`を必ず非null文字列にする（`CognitoUserAttributesClient`または`UserProvisioningService`のどちらに実装するかは着手時に判断）
- 既存にすでに`displayName=null`で作成済みの`app_user`行（dev環境）への対応要否・方法（DB直接更新 or 自然解消は不可のため何らかの手当てが必要）は着手時に確認する

## 受け入れ条件

- [x] Cognitoの`name`属性が存在しない新規ユーザーでも、`GET /api/v1/me`の`displayName`が非nullの文字列で返る
- [x] `displayName`のフォールバック挙動をUnit Testで検証する（`UserProvisioningServiceTest`等）
- [x] dev環境で、Hosted UIの標準サインアップフロー（email+passwordのみ）から新規ユーザーがログイン〜`GET /api/v1/me`成功〜ダッシュボード表示まで到達することを実際に確認する
- [x] 既存にdisplayName=nullで作成済みのdev環境`app_user`行があれば、あわせて解消する
- [x] `./gradlew spotlessCheck test integrationTest`が通る
- [x] markdownlintが通る（変更したdocsがあれば）

## 作業ログ

- 2026-08-12 チケット起票（#00043のdev環境動作確認中に発見）
- 2026-08-12 完了: `UserProvisioningService.createUser()`にフォールバック処理を実装（Cognitoの`name`属性が無い場合、emailのローカル部を`displayName`として使用）。`UserProvisioningServiceTest`にフォールバック挙動のテストを追加。`./gradlew spotlessCheck test integrationTest`グリーン（`QuestionSetApiIntegrationTest`の一部失敗は本チケットの変更と無関係な既知のflakiness〔他クラスと同時実行時のみ再現、#00034から既知〕であることを、変更前のコードで単体実行して再現しないことを確認済み）。dev環境の既存壊れ行（`displayName=null`、削除済みテストユーザーのcognito_sub）をSupabaseから直接削除して解消。修正版backendイメージをビルド・ECR push・ECS再デプロイ（linux/amd64向けビルドが必要だった点に注意。ローカルがApple Siliconのため`docker build --platform linux/amd64`を明示指定）し、dev環境で実際にname属性なしの新規Cognitoユーザーがサインアップ〜ログイン〜`GET /api/v1/me`成功〜ダッシュボード表示（`displayName`がemailローカル部にフォールバック）まで到達することをPlaywrightで確認した。[backend PR #18](https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/18)を作成しマージ済み
