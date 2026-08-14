# #00024 プライバシーポリシー・利用規約画面（S-08/S-09）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [frontendリポジトリ docs/画面設計書/S-08_プライバシーポリシー画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-08_プライバシーポリシー画面.md) / [frontendリポジトリ docs/画面設計書/S-09_利用規約画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-09_利用規約画面.md) / [frontendリポジトリ docs/legal/プライバシーポリシー.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/legal/プライバシーポリシー.md) / [frontendリポジトリ docs/legal/利用規約.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/legal/利用規約.md) / [#00008 プライバシーポリシー・利用規約画面の追加](./00008_プライバシーポリシー利用規約画面の追加.md)

## 実現すること

`#00008`で作成した画面設計書（S-08/S-09）とlegal本文（`docs/legal/プライバシーポリシー.md`・`利用規約.md`）に基づき、プライバシーポリシー画面（S-08）・利用規約画面（S-09）を`ielts-creater-frontend`に実装する。両画面ともフォーム入力・API連携を持たない静的コンテンツ画面であり、共通の法的ページレイアウトとして実装する。

- `app/privacy/page.tsx`（`/privacy`）、`app/terms/page.tsx`（`/terms`）を新規作成し、対応するlegal本文（Markdown）を章立てのまま表示する
- 本文冒頭の制定日・最終改定日のメタ情報を表示する
- S-08⇔S-09の相互リンク、Topへ戻るリンクを実装する
- 本画面にはHTML叩き台（`docs/screen-drafts/`）が存在しない（`#00008`でビジュアルデザインは対象外とされたため）。既存のデザインシステム（Tailwind CSS + shadcn/ui、ブランドカラー）に沿って実装する
- TOPページのフッター（`src/components/sections/site-footer.tsx`）にある「プライバシーポリシー」「利用規約」の導線（現状ダミーの`<button type="button">`）を、それぞれ`/privacy`・`/terms`への実リンク（Next.jsの`Link`）に置き換える

## 受け入れ条件

- [x] `/privacy`・`/terms`両ページに各legal本文が画面設計書の構成要素通りに表示されている
- [x] 制定日・最終改定日メタ情報、S-08⇔S-09相互リンク、Topへ戻るリンクが実装されている
- [x] TOPページフッターの2導線が実リンクに置き換わっている
- [x] `npm run build`が通る
- [x] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [x] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- 2026-08-10 着手
- 2026-08-10 完了。`ielts-creater-frontend` feat/ticket-00024 で実装（PR #17）。共通の法的ページレイアウト（`LegalPageLayout`）と、見出し・テーブル・箇条書き表示用の共通コンポーネント（`LegalSection`/`LegalTable`/`LegalList`）を新規作成し、`docs/legal/`の本文をJSXに転記する形で`/privacy`・`/terms`を実装（本文の一次情報源は引き続き`docs/legal/`とし、更新時はまずそちらを更新してからページ側に反映する運用）。ヘッダーは`(auth)`レイアウトで使用中の`AuthHeader`（ロゴのみ、TOP画面のアンカーナビを持たない）を再利用し、フッターは既存の`SiteFooter`をそのまま利用。TOPページフッターの「プライバシーポリシー」「利用規約」導線をダミーの`<button>`から`next/link`の実リンクに置き換え。`npm run build`・`npm run lint`はエラー0件を確認。claude-in-chromeでのdev server動作確認では、デスクトップ幅・約500px幅（ヘッダーがハンバーガーメニューに切り替わる幅）の両方でレイアウト崩れ・コンソールエラーがないこと、フッターからの実リンク遷移・S-08⇔S-09相互リンク・Topへ戻るリンクが正しく機能することを確認。ただしこの環境では自動リサイズツールで正確に390px幅を再現できなかったため、iPhone SE相当の実機/DevToolsでの追加確認を推奨する
