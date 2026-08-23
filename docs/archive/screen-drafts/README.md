# 画面デザイン叩き台（静的プロトタイプ）

TOP画面（S-01）で確定した配色・フォント・コンポーネントパターンをベースに、ログイン後の主要フロー画面のビジュアルデザインを検討するための静的HTMLの叩き台。

**これは実装コードではない。** ビルド不要の単体HTMLファイル（Tailwind CDN + `assets/`配下の共通CSS）で構成しており、デザインの方向性をブラウザ上で素早く確認・レビューするためのプロトタイプ。実装時はNext.js + TypeScript + Tailwind CSS + shadcn/ui（frontendリポジトリ）に移植する前提。

> **2026-08-23移設**: 対応するfrontend実装（#00018〜#00024）が全て完了したため、`docs/screen-drafts/`から本ディレクトリ（`docs/archive/screen-drafts/`）へ移動した。旧パス（`docs/screen-drafts/...`）を参照している完了済みチケット（[#00016](../../../tickets/00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)、#00018〜#00024）は当時の記録として書き換えていない。

## 見方

各HTMLファイルはブラウザで直接開ける（`file://` で問題ない）。

```sh
open docs/archive/screen-drafts/00_style-guide.html
```

## 一覧

| ファイル | 画面 | 内容 |
| --- | --- | --- |
| [00_style-guide.html](./00_style-guide.html) | ベースデザイン | カラー・タイポグラフィ・ボタン・フォーム部品・バッジ・アラート・カード・タブ・ページング・ローディング・音声プレイヤー・アプリナビゲーションシェル等の一覧 |
| [s-02_login-signup.html](./s-02_login-signup.html) | S-02 ログイン/サインアップ画面 | ログイン／エラー／サインアップ／確認コード入力の4状態 |
| [s-07_dashboard.html](./s-07_dashboard.html) | S-07 ダッシュボード画面 | スコア推移・セクション別/出題形式別正答率・受験回数 |
| [s-03_question-generation.html](./s-03_question-generation.html) | S-03 問題生成画面 | 生成条件入力／生成中／生成失敗の3状態 |
| [s-04_answer.html](./s-04_answer.html) | S-04 回答画面 | Reading（本文＋TFNG/穴埋め/見出しマッチング）・Listening（音声プレイヤー＋MCQ）の2パターン |
| [s-05_result.html](./s-05_result.html) | S-05 結果画面 | スコアサマリー・設問ごとの正誤/解説・再挑戦導線 |
| [s-06_history.html](./s-06_history.html) | S-06 履歴一覧画面 | 受験履歴一覧・セクション絞り込み・ページング・空状態 |

対象範囲は主要フロー優先のため、S-08（プライバシーポリシー）・S-09（利用規約）は今回のスコープ外（[チケット#00016](../../../tickets/00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)参照）。

## 構成

```text
docs/archive/screen-drafts/
├── assets/
│   ├── tokens.css       # ブランドカラー・タイポグラフィ・角丸等のCSS変数
│   └── components.css   # ボタン・フォーム・カード・ナビ等の共通コンポーネントクラス
├── 00_style-guide.html
├── s-02_login-signup.html
├── s-07_dashboard.html
├── s-03_question-generation.html
├── s-04_answer.html
├── s-05_result.html
└── s-06_history.html
```

## 主な設計判断

- **ボタン**: primary（ネイビー塗り）を主要アクション、accent（オレンジ塗り）はナビのCTA等アクセント用途に限定
- **セマンティックカラー**（success/warning/error）はTOP画面（マーケティング用途）には存在せず、今回新規に定義した
- **アプリナビゲーションシェル**（ロゴ＋ダッシュボード/問題生成/履歴一覧＋ユーザーメニュー）はTOPのマーケティング用ヘッダーとは別物として新規デザインし、S-03/S-04/S-05/S-06/S-07で共通利用する
- S-02はTOPのヘッダーを使わず、独立した2カラムの認証画面（ネイビー＋ドット柄のブランドパネル＋フォーム）とした
