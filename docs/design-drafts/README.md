# Cognito Hosted UIデザイン改善案（静的モックアップ）

[#00065](../../tickets/00065_CognitoHostedUIログイン画面のデザイン改善案作成.md) で作成した、Cognito Hosted UI（Classic）ログイン画面のデザイン改善案の静的プロトタイプ。

**これは実装コードではない。** ビルド不要の単体HTML（Tailwind CDN使用）で、現状（Before）と改善案（After）を並べて比較できるようにしている。実際の`ielts-creater-infra`側Terraform（`terraform/modules/cognito/hosted-ui.css`）への反映は、本チケットの内容を踏まえた別の実装チケットで行う。

`docs/archive/screen-drafts/`（S-01〜S-07のUI叩き台）とは別系統。対象がCognito Hosted UI本体（AWS側が配信する認証フォーム画面。frontendの`/login`中間ページとは別物）であるため、DOM構造・CSS適用範囲ともにfrontend実装とは無関係に検討する必要があり、`assets/tokens.css`等の資産は流用していない。

## 見方

```sh
open docs/design-drafts/cognito-hosted-ui.html
```

## 構成

```text
docs/design-drafts/
├── cognito-hosted-ui.html
└── assets/
    ├── fragment-before.html
    ├── fragment-after.html
    ├── current-hosted-ui.css
    ├── cognito-hosted-ui.proposal.css
    └── logo-mark.svg
```

## 主な設計判断

- Cognito Hosted UI（Classic）が公式にサポートする`*-customizable`クラス名をそのままモックアップのDOMにも使用し、`cognito-hosted-ui.proposal.css`を無加工の`<link>`でAfter側に読み込むことで、モックアップと実際にinfra側へ渡すCSSの乖離を防いでいる
- DOM構造も実機と同じ入れ子（`.background-customizable`の内側に`.banner-customizable`）にしている。実機は`.background-customizable`がカード全体を包む`overflow: hidden`の外側コンテナで、`.banner-customizable`はその子要素（初期実装ではこれを兄弟要素にしてしまい、紺色バナーと白いフォーム部分の角がずれる不具合があったため修正）
- Before側は現状の`hosted-ui.css`（8ルール）をそのまま適用した上で、未カスタマイズ要素（入力欄・リンク・エラー表示）はAWSデフォルトスタイルの近似値で補っている（実測に基づく近似であり正確な値ではない旨をコメントで明記）
- `current-hosted-ui.css`は比較用のスナップショット。実際の正本は`ielts-creater-infra`リポジトリ側であり、乖離した場合はinfra側が正
- After側の見出し・ラベル・ボタン・リンク・エラー文言は日本語表記にしている。ただし実際のCognito Hosted UI本体（Classic）でこれを実現する技術的手段は本チケットの調査範囲では確立できていない（`ui_locales`パラメータは実機検証で無効、CSSでの文言差し替えも不可）。あくまで理想形のデザイン提案であり、実現可否の検証は後続の実装チケットの課題とする
- 送信ボタンの文字色（白→ネイビー）・リンク色（オレンジ→濃いオレンジ`#c2410c`）は、WCAG AA基準（4.5:1）のコントラスト比を満たすよう見直している（現行の配色は約2.8〜2.9:1で基準未達だった）
- **既知の制約（#00067の実装・dev環境実機確認で判明）**: `.redirect-customizable:hover`や`.redirect-customizable a`のような疑似クラス・子孫セレクタはCognitoのSetUICustomization APIから拒否される。実機では「Need an account? Sign up」の「Sign up」部分がCognito側マークアップ上ネストされた別要素になっており、親の色を継承できないため、Cognito標準の青のまま残る（本モックアップのAfter側では理想形として単一色で表示している）
- ロゴ画像は実ファイル（PNG）を用意せず、書き出し元のマスターSVGをそのままモックアップ内に表示している

## ロゴ画像の本番化手順（後続の実装チケット向け）

1. `assets/logo-mark.svg`をマスターとする
2. Playwright（`#00047`でfrontend E2E用に導入済み、新規依存追加不要）でGoogle Fontsを読み込んだ状態でスクリーンショットし、透過PNG化する（2倍解像度、440×96px程度）
3. `ielts-creater-infra`側の`aws_cognito_user_pool_ui_customization`リソースに`image_file`（`filebase64(...)`）として設定する
4. サイズはAWS公式ドキュメントで正式な上限を確認した上、可能な限り小さく（目安100KB未満）最適化する
