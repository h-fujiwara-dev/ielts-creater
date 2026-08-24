# #00065 Cognito Hosted UIログイン画面のデザイン改善案作成

- 対象リポジトリ: root
- ステータス: 完了
- type: docs
- 関連ドキュメント: [#00052 ログイン/登録画面のCognito Hosted UIブランディング調整](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md) / [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md) / [#00051 frontendとbackendの本番環境への初回デプロイ](./00051_frontendとbackendの本番環境への初回デプロイ.md) / [#00062 TOP画面ボタンでCognitoサインイン/サインアップ画面を出し分け](./00062_TOP画面ボタンでCognitoサインイン-サインアップ画面を出し分け.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md) / [#00061 不要ファイル削除とscreen-draftsのアーカイブ化](./00061_不要ファイル削除とscreen-draftsのアーカイブ化.md)

## 実現すること

[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)でCognito Hosted UI（Classic）に配色（ネイビーバナー・オレンジ送信ボタン等、`ielts-creater-infra/terraform/modules/cognito/hosted-ui.css`）を適用済みだが、実URL（`https://auth.band-eight.com/login`）を確認した結果、以下が未対応のまま残っていることが分かった。

- ロゴ画像が未設定でバナーが空白のまま
- 見出し・ラベル（Email / Password 等）が英語のまま（アプリ本体は日本語UI）
- 入力欄（`.inputField-customizable`）・エラー表示（`.errorMessage-customizable`）・「Forgot your password?」「Sign up」リンク（`.redirect-customizable`）はAWSデフォルトの見た目のまま（現状の8ルールでは未カバー）
- カード自体（`.background-customizable`）の角丸・影がアプリ本体の`.card`コンポーネントと不統一

本チケットでは、[#00034](./00034_Cognito認証の本実装.md)の設計判断（Hosted UIへのリダイレクト方式）およびHosted UIの技術方式（Classic Hosted UI CSSカスタマイズ。Managed Login Brandingへの移行はしない）は変更せず、その制約内で実現可能なデザイン改善案を設計し、以下を成果物として作成する。**実際の`ielts-creater-infra`側Terraform変更・ロゴ画像ファイル（PNG）の本番作成・`terraform apply`は本チケットのスコープ外**とし、本チケットの内容を踏まえた別の実装チケット（infra / frontend）に委ねる。frontendの`/login`中間ページ（`auth-brand-panel.tsx`等）は対象外。

1. **hosted-ui.css拡張案**: 現状8ルールに対し、未使用の`.inputField-customizable`・`.errorMessage-customizable`・`.redirect-customizable`・`.logo-customizable`を追加し、フォントスタック（システムフォントfallbackのみ。外部Webフォントは読み込み不可のため）・角丸（アプリ本体の`--radius-md`/`--radius-xl`相当）を適用した改善案を`docs/design-drafts/assets/cognito-hosted-ui.proposal.css`として作成する。`.background-customizable`（カード全体）には`overflow: hidden`を付け、内側の`.banner-customizable`（紺色バナー）がカードの角丸からはみ出さないようにする（実機DOM調査で`.background-customizable`が`.banner-customizable`を包む外側コンテナだと判明）
2. **ロゴ画像の準備方針**: 既存のテキストロゴ（`BookOpenCheck`アイコン + "IELTS Creator"、`site-header.tsx`/`auth-brand-panel.tsx`と同一構成）をSVGマスターとして書き出す方針・書き出し手順を整理する。マスターSVGは`docs/design-drafts/assets/logo-mark.svg`として作成する（本番用PNG化・Terraformの`image_file`への設定は後続の実装チケットで行う）
3. **静的HTMLモックアップ**: Before（#00052時点の現状相当）とAfter（本チケットの改善案）を並べて比較できる単体HTML一式を`docs/design-drafts/`配下に新規作成する（`docs/archive/screen-drafts/`は役目を終えたアーカイブのため再利用しない。frontend実装向けの共通トークン資産とも性質が異なる）。DOM構造は実機と同じ入れ子（`.background-customizable`の内側に`.banner-customizable`）にする
4. **日本語化の扱い**: Classic Hosted UIの表示言語はブラウザのAccept-Languageヘッダー依存であり、CSS/Terraformから固定する明確な方法がないことを調査済み（`ui_locales=ja`パラメータも実機検証で無効と確認）。**モックアップ（After側）の見出し・ラベル・ボタン・リンク・エラー文言は日本語表記に変更**し、理想形の提案として示す。ただし実際のCognito Hosted UI本体側でこれを実現する技術的手段は本チケットの調査範囲では確立できておらず、実現可否の検証を後続の実装チケットの課題として明記する
5. **アクセシビリティ改善（コントラスト比）**: 実機のcomputed styleを確認した結果、現行の送信ボタン（白文字onオレンジ背景、約2.9:1）とリンク（オレンジ文字on白系背景、約2.8:1）がWCAG AA基準（4.5:1）を満たしていないことが判明した。送信ボタンの文字色をアプリ本体の`.btn-accent`パターンに合わせてネイビーに変更（約6.2:1に改善）、リンク色を濃いオレンジ`#c2410c`に変更（約5.1:1に改善）する

## 受け入れ条件

- [x] `docs/design-drafts/cognito-hosted-ui.html`（Before/After比較ページ）と`docs/design-drafts/assets/`（`fragment-before.html`・`fragment-after.html`・`current-hosted-ui.css`・`cognito-hosted-ui.proposal.css`・`logo-mark.svg`）が作成されている
- [x] `cognito-hosted-ui.proposal.css`に`.inputField-customizable`・`.errorMessage-customizable`・`.redirect-customizable`・`.logo-customizable`を含む改善案CSS全文が、既存の8ルール（`.background-customizable`等）を壊さない形で記載されている。`.background-customizable`に`overflow: hidden`が指定され、カードの角丸内に紺色バナーが収まっている
- [x] モックアップをブラウザで実際に表示し、Before/Afterの差分（ロゴ有無・入力欄/リンク/エラー表示のスタイル・カードの角丸統一・日本語表記）が視覚的に確認できる。1440px相当でレイアウト崩れ・コンソールエラーがないことを確認した（375px相当はブラウザ自動化ツールの制約で実機スクリーンショットは取得できなかったが、比較ページはTailwindの`md:grid-cols-2`でモバイル時1カラムに切り替わり、各カード自体も`max-width: 340px`のため崩れない設計になっている）
- [x] ロゴ画像の準備方針（元データ・書き出し形式/サイズ・書き出し手順・本番PNG化の担当）が本チケットまたは`docs/design-drafts/README.md`に記載されている
- [x] After側モックアップの文言が日本語表記になっている。実際のCognito本体での日本語化の実現可否が未確立である旨がチケット・READMEに明記されている
- [x] 送信ボタン・リンクの配色がWCAG AA基準（4.5:1）を満たすよう見直されている
- [x] `docs/design-drafts/README.md`に位置づけ（静的モックアップであり実装コードではない旨）・各ファイルの説明・後続実装チケットへの導線が記載されている
- [x] 本チケットの作業では実際のCognito Hosted UI（`https://auth.band-eight.com/login`等）・`ielts-creater-infra`側Terraformを一切変更しない（`terraform plan`/`apply`は対象外）

## 作業ログ

- 2026-08-24 起票
- 2026-08-24 `https://auth.band-eight.com/login`の実機確認（ロゴ空白・英語ラベル・入力欄/リンク/エラー表示未カスタマイズ・カード角丸不統一を確認）を踏まえ、`docs/design-drafts/`にBefore/After比較の静的HTMLモックアップと`hosted-ui.css`拡張案・ロゴマスターSVGを作成し完了
- 2026-08-25 ユーザーレビューで「文字を日本語表記に」「紺色背景と白背景の角が合っていない」の指摘を受け再調査。`javascript_tool`で実機DOM構造を確認した結果、`.background-customizable`が`.banner-customizable`を内包する外側コンテナ（`overflow: hidden`）であることが判明し、モックアップのDOM構造の誤りが角ズレの原因と特定して修正。`ui_locales=ja`パラメータも実機検証したが無効だったため、After側モックアップの文言を日本語表記に変更しつつ「実現方法は未確立」と明記する方針に更新。あわせて送信ボタン・リンクのコントラスト比不足（WCAG AA基準未達）も発見し配色を修正
