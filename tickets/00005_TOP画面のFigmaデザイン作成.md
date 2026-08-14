# #00005 TOP画面（S-01）のFigmaデザイン作成

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: docs
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [システム要件定義書 4章 画面要件](../docs/システム要件定義書.md#4-画面要件) / [ロードマップ Phase 1](../docs/ロードマップ.md) / [frontendリポジトリ docs/画面設計書/](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/) / [#00004 Top画面追加とログイン画面遷移の反映](./00004_Top画面追加とログイン画面遷移の反映.md)

## 実現すること

TOP画面（S-01: サービス概要を紹介し、ログイン／サインアップ画面へ誘導する画面）のビジュアルデザインを確定し、frontendリポジトリの画面設計書に反映する。

- 参照元は、Figma Community「[Top 16 Websites of 2024 - Awwwards (Community)](https://www.figma.com/design/svb83g0lE6UzihtDoyFcqk/Top-16-Websites-of-2024---Awwwards--Community-)」内の **certosoftware.comフレーム**（node-id=30-8264。ヒーロー＋アプリ画像／機能紹介／ベネフィット訴求／ブログ風セクション／フッターという構成、ネイビー×オレンジのトーン対比が特徴）とした
  - このファイルは汎用UIキットではなく、実在企業サイトをそのまま再現したギャラリーであるため、**参考にしたのはレイアウト構成（セクション構成・情報設計）と配色のトーン感まで**とし、**ロゴ・コピー・写真等のブランド固有素材および実在企業名（Certo等）は一切使用せず、IELTS Creator独自の素材に置き換えた**
- **方針転換**: 当初Claudeがclaude-in-chromeでFigma上に矩形・テキストを直接組み立てる案で進めたが、質感（余白・タイポグラフィ・配色の深み）の作り込みに限界があり「Claude Codeの上手い使い方ではない」というフィードバックを受けて中止。代わりにNext.js + TypeScript + Tailwind CSS + shadcn/uiでcertosoftware.comフレームの構成を忠実にコード実装し、その上でIELTS Creator向けの内容に差し替える二段階アプローチに変更した（プロトタイプはリポジトリ外のscratchpadで作成、成果物はスクリーンショットとして本リポジトリ・frontendリポジトリに反映）
- `/ui-ux-pro-max`スキルでモダンなSaaS/教育系プロダクトに適した配色・タイポグラフィ（Plus Jakarta Sans + Noto Sans JP、ネイビー`#0F172A`×オレンジ`#F97316`のトークン更新）を検討し反映した
- 画像素材はUnsplash（[unsplash.com/developers](https://unsplash.com/developers)、Unsplash API）から取得した。APIキーはユーザーが取得・共有し、ダウンロードイベントのトリガー・撮影者/Unsplashへのクレジット表記を実施済み
- IELTS Creatorのブランド要素（配色）は、certosoftware.comフレームの「ネイビー×オレンジ」のトーンをそのまま採用することで合意した（別途のブランドキット共有は行わなかった）
- 完成したデザインのスクリーンショット・主要要素・デザイン意図を、frontendリポジトリの`docs/画面設計書/S-01_Top画面.md`に反映し、本リポジトリの`docs/画面遷移図.md`にもプレビュー画像を追加した
- Figmaへの最終デザインの反映（画像貼り付け等）は、手作業での再現コストに対して効果が低いと判断し見送った。Figmaファイルには検討経緯を示す参照メモのみを残し、最終デザインの一次情報はコード実装とスクリーンショットとする

なお、frontendリポジトリの`docs/画面設計書/`の既存ファイルのリナンバリング（S-01〜S-06 → S-02〜S-07）は[#00007 フロントエンド画面設計書の作成](./00007_フロントエンド画面設計書の作成.md)で別途完了済み。本チケットではその後の状態（`S-01_Top画面.md`が既存ファイルとして用意された状態）を前提に、内容を確定・追記した。

## 受け入れ条件

- [x] certosoftware.comフレームを参考に、レイアウト構成（セクション構成・情報設計）を検討している（実在企業のロゴ・コピー・写真は使用しない）
- [x] 画像素材をUnsplash APIから取得し、クレジット表記を記録している
- [x] IELTS Creatorのブランド要素（配色）を確定している（certosoftware.comフレームのネイビー×オレンジのトーンを採用）
- [x] frontendリポジトリの`docs/画面設計書/S-01_Top画面.md`にTOP画面のデザイン仕様（スクリーンショット・主要要素・デザイン意図）を反映している
- [x] 既存5ファイルのリナンバリングは#00007で完了済みであることを確認している

## 作業ログ

- 2026-08-09 着手。チケット起票のみ実施し、Figma上の実作業は着手前の状態
- 2026-08-09 参照デザインを確定。Figma Community「Top 16 Websites of 2024 - Awwwards」内のcertosoftware.comフレームをレイアウト＋配色トーンの参考とし、ブランド固有要素は独自素材に置き換える方針で合意。画像素材はUnsplash API経由で取得予定（APIキー未取得）。IELTS Creator既存のブランド要素（配色・トーン・ロゴ）はユーザーが後日共有予定のため、共有されるまでFigma上の実作業には着手しない
- 2026-08-09 Figma上での手作業（矩形・テキストの組み立て）でヘッダー＋ヒーローを試作したが、質感が粗く「Claude Codeの上手い使い方ではない」とフィードバックを受け中止。方針転換し、certosoftware.comフレームをNext.js + TypeScript + Tailwind + shadcn/uiでコード実装する二段階アプローチ（忠実クローン→IELTS Creator向け差し替え）に切り替えることで合意
- 2026-08-09 Unsplash APIキーを取得・共有いただき、ブランド要素（配色）はcertosoftware.comフレームのトーンをそのまま採用する方針で合意。scratchpadにNext.jsプロトタイプ（`landing-clone`）を作成し、忠実クローン→IELTS Creator向け差し替え（コピー・実写真2枚・ロゴ）まで完了
- 2026-08-09 `/ui-ux-pro-max`スキルで設計データベースを検索し、Plus Jakarta Sans + Noto Sans JP、ネイビー`#0F172A`×オレンジ`#F97316`等モダンなSaaS/教育系トークンに更新。sticky header・浮遊ステータスカード・ホバーインタラクション・アクセシビリティ（cursor-pointer、aria-label、focus ring）を追加。ユーザーレビューを受けフッターのニュースレター登録・サイト内リンク集、ヘッダーのナビリンクなど実体のないUIを削除しシンプル化
- 2026-08-09 最終デザインをfrontendリポジトリ`docs/画面設計書/S-01_Top画面.md`（構成要素・ビジュアルデザイン・スクリーンショット5枚）とrootリポジトリ`docs/画面遷移図.md`（プレビュー画像）に反映。Figmaへの最終デザイン反映は工数対効果が低いと判断し見送り、参照メモのみ残した。既存5ファイルのリナンバリングは#00007で完了済みであることを確認し、チケットを完了とする
