# #00024 プライバシーポリシー・利用規約画面（S-08/S-09）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 未着手
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

- [ ] `/privacy`・`/terms`両ページに各legal本文が画面設計書の構成要素通りに表示されている
- [ ] 制定日・最終改定日メタ情報、S-08⇔S-09相互リンク、Topへ戻るリンクが実装されている
- [ ] TOPページフッターの2導線が実リンクに置き換わっている
- [ ] `npm run build`が通る
- [ ] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [ ] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- YYYY-MM-DD 着手
