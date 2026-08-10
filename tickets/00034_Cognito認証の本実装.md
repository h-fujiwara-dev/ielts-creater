# #00034 Cognito認証の本実装

- 対象リポジトリ: backend / frontend / infra
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [backendリポジトリ docs/API一覧.md 3章 認証フロー](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API一覧.md#3-認証フロー) / [backendリポジトリ docs/API設計書/GET_me.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_me.md) / [backendリポジトリ docs/実装規約.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/実装規約.md) / [frontendリポジトリ docs/画面設計書/S-02_ログインサインアップ画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-02_ログインサインアップ画面.md) / [docs/ロードマップ.md](../docs/ロードマップ.md) / [#00032 画面（S-03〜S-07）のモックAPIから実API接続への切り替え](./00032_画面（S-03〜S-07）のモックAPIから実API接続への切り替え.md)

## 実現すること

Phase1のno-auth暫定実装（backend `NoAuthSecurityConfig`/`NoAuthCurrentUserProvider`による固定devユーザー、frontend `dev-user.ts`による擬似ログイン）を、ロードマップPhase2で計画しているCognito認証の本実装に置き換える。`docs/実装規約.md`の未決事項R-2「Phase1（認証なし）→ Phase2（Cognito認証）の切り替え方式の詳細設計」を本チケットで確定・実装する。

あわせて、調査で判明した以下のドキュメント上の実態不一致を解消する:

- `docs/API一覧.md`は全エンドポイントに「Bearer必須」と記載しているが、現状の実装はno-authモードのみで整合していない（本チケットでCognitoモードを実装することで実態と一致させる）
- backendの`application.yml`はデフォルトで`app.auth.mode: cognito`を指定しているが、対応する`@ConditionalOnProperty(havingValue="cognito")`のBeanが存在しないため、`local`以外のプロファイルを起動すると`CurrentUserProvider`/`SecurityFilterChain`のBeanが解決できず起動不能になり得る（本チケットでCognito向けBeanを実装することで解消する）

なお、Cognito User Poolの構築方式（Terraform化はロードマップ上Phase3のinfraスコープのため、本チケットの時点では未整備の可能性がある）は着手時にユーザーと相談し、暫定的な手動構築とTerraform先行構築のどちらで進めるか決定する。

## 受け入れ条件

- [ ] infra: 開発用のAmazon Cognito User Pool・App Clientを用意する（方式は着手時に確認）
- [ ] backend: `CognitoSecurityConfig`（`@ConditionalOnProperty(prefix="app.auth", name="mode", havingValue="cognito")`）でJWKSによるJWT検証を実装する。CognitoアクセストークンはOIDC標準の`aud`クレームを持たないため、`token_use=access`・`client_id`の検証を独自実装で追加する（API一覧.md 3章準拠）
- [ ] backend: `AppUser`エンティティ・Repository（既存`app_user`テーブル準拠）と、JWT検証成功時に`app_user`をUpsertする`UserProvisioningService`を実装する
- [ ] backend: `CognitoCurrentUserProvider`（`CurrentUserProvider`実装）を追加し、`GET /api/v1/me`エンドポイントを実装する
- [ ] backend: `docs/API一覧.md`の認可要否の記載をPhase1（no-auth）/Phase2（Cognito）の実態に即して更新する
- [ ] frontend: NextAuth.js（Cognitoプロバイダ、Authorization Code + PKCE）によるログインフローを実装し、httpOnly暗号化Cookieにセッションを保存する（S-02設計書の方針を反映）
- [ ] frontend: `lib/api-client.ts`でbackend呼び出し時にBearerトークンを付与し、`middleware.ts`で保護ルートを実装する
- [ ] frontend: ログイン/サインアップ画面の`dev-user.ts`による擬似判定をCognito実接続に置き換える
- [ ] backend: JWT検証のUnit Test・結合テスト（不正トークンで401、有効トークンでの`app_user` Upsert確認）を実装する
- [ ] `./gradlew spotlessCheck test integrationTest`（backend）・`npm run lint` / `npm run build` / `npm run test`（frontend）がグリーンであることを確認する

## 作業ログ

- 2026-08-11 起票
