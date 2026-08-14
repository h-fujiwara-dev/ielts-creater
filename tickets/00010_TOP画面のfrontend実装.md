# #00010 TOP画面（S-01）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [#00005 TOP画面のFigmaデザイン作成](./00005_TOP画面のFigmaデザイン作成.md) / [画面遷移図](../docs/画面遷移図.md) / [frontendリポジトリ docs/画面設計書/S-01_Top画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-01_Top画面.md)

## 実現すること

本チケットでは、[#00005](./00005_TOP画面のFigmaデザイン作成.md)で確定したTOP画面（S-01）のデザインを、`ielts-creater-frontend`リポジトリの正式なアプリ実装として反映する。

- `ielts-creater-frontend`リポジトリには現状Next.jsアプリの実体（`src/`等）が存在しないため、本チケットで初めてアプリを新規構築する（Next.js App Router + TypeScript + Tailwind CSS + shadcn/ui、`create-next-app`のオプションは#00005の検証用プロトタイプに合わせる）
- 検証用プロトタイプ（scratchpad `landing-clone`、本リポジトリには未取り込み）のコンポーネント・スタイル・コピーを、正式な実装として移植する
  - フォント: Plus Jakarta Sans（Latin）+ Noto Sans JP、`next/font/google`で自己ホスト
  - カラー: ネイビー`#0F172A`／オレンジ`#F97316`／ブルー`#4640DE`／ラベンダー`#EEF1FF`（[S-01_Top画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-01_Top画面.md)参照）
  - 9セクション構成（ヘッダー／ヒーロー／ハイライト＋対応出題形式／ストーリー／機能グリッド／2カラムCTA／CTAバンド／使い方3ステップ／フッター）
  - ヒーロー・ストーリーの写真はUnsplashから取得済みの実写真（クレジット表記込み）をそのまま静的アセットとして使用する
- ルーティングは`app/page.tsx`（ルート直下）

## 受け入れ条件

- [x] `ielts-creater-frontend`リポジトリにNext.jsアプリが新規構築されている（TypeScript / Tailwind CSS / App Router / shadcn/ui）
- [x] S-01 Top画面が[S-01_Top画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-01_Top画面.md)の仕様通り（9セクション構成・配色・フォント）に実装されている
- [x] `npm run build`が通る
- [x] 既存のlint（markdownlint等）が引き続き通る
- [x] ローカルでdev serverを起動し、レイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- 2026-08-09 着手
- 2026-08-09 `ielts-creater-frontend`にNext.js(App Router) + TypeScript + Tailwind CSS + shadcn/uiのアプリを新規構築（`create-next-app`は一時scaffoldで実行し、既存のCLAUDE.md/README.md/docs等を壊さないよう設定ファイルとpackage.jsonのみを実リポジトリにマージ）。scratchpadの検証用プロトタイプ（`landing-clone`）からコンポーネント・画像・スタイルを移植し、S-01 Top画面を実装
- 2026-08-09 `npm run build` / `npm run lint` / markdownlintが通ることを確認。Next.js 16の`agentRules`機能がCLAUDE.mdに追記する見出しがmarkdownlintのMD025（単一H1）と衝突したため、`next.config.ts`で`agentRules: false`に設定して無効化。ローカルdev serverでレイアウト崩れ・コンソールエラーがないことを確認し、`ielts-creater-frontend` PR [#8](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/8)を作成（CIパス済み）。チケットを完了とする
