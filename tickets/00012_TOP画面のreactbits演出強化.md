# #00012 TOP画面（S-01）のreactbits演出強化

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [#00010](./00010_TOP画面のfrontend実装.md)、[frontendリポジトリ docs/画面設計書/S-01_Top画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/develop/docs/画面設計書/S-01_Top画面.md)

## 実現すること

[#00010](./00010_TOP画面のfrontend実装.md)で実装したTOP画面（S-01）に、[reactbits.dev](https://reactbits.dev)のアニメーションコンポーネントを取り入れ、モダンでリッチな質感に演出強化する。
既存のネイビー×オレンジのプロフェッショナルなSaaSトーンを崩さない「中程度」の演出強度で実施する。

- Heroセクション見出し: 単語単位でブラー解除＋フェードインするテキストアニメーション（reactbits `BlurText`）
- Heroセクション背景: マウス追従で膨らむ・光るドットフィールド背景演出（reactbits `DotField`）
- カードのホバー演出: マウス追従のスポットライト効果（reactbits `SpotlightCard`）をハイライトカード・機能グリッドに適用
- `prefers-reduced-motion`を尊重し、モーション低減設定時は静的表示にフォールバックする

## 受け入れ条件

- [x] Heroの見出しがBlurTextでマウント時にアニメーション表示される
- [x] Hero背景にDotFieldが表示され、`prefers-reduced-motion`時は非表示になる
- [x] TrustBand・FeatureGridのカードにSpotlightCardのホバー演出が適用される
- [x] `npx tsc --noEmit` / `npm run lint` がエラーなしで通る
- [x] dev serverで1440px/375px相当の表示崩れ・コンソールエラーがないことを確認
- [x] frontendリポジトリへコミット・push

## 作業ログ

- 2026-08-09 着手。reactbits.dev（GitHub: DavidHDev/react-bits）から`BlurText`（motion依存）・`DotGrid`（gsap依存）・`SpotlightCard`（依存なし、CSSのみ）のソースを調査し、TypeScript化して`feat/ticket-00010`ブランチに追加実装する方針とした。
  - develop未マージの#00010ブランチにのみアプリ実体が存在するため、本チケットも同ブランチ上で作業しコミットを積む（PR #8に統合）
- 2026-08-09 実装・検証・完了。
  - `src/components/reactbits/`配下に`blur-text.tsx`・`dot-grid.tsx`（+css）・`spotlight-card.tsx`（+css）を新規追加。`src/lib/use-reduced-motion.ts`は`useSyncExternalStore`ベースの`prefers-reduced-motion`検知フック（`useEffect`内での直接`setState`はeslint-plugin-react-hooksの`set-state-in-effect`ルールに抵触するため回避）
  - Hero見出しを`BlurText`（`animateBy="characters"`）2行構成に、Hero背景に`DotGrid`（`opacity-70`のラッパーでさらに減光、`baseColor`はslate-400・`activeColor`はブランドオレンジ）を追加
  - DotGridは初期ポインタ座標を画面外（`-9999`）に変更（デフォルトの`(0,0)`のままだと初回描画時にキャンバス左上付近の点が誤って「アクティブ色」で描画されるバグがあったため）
  - TrustBand・FeatureGridの各カードを`SpotlightCard`でラップ（オレンジの控えめなスポットライト色）
  - ドットの視認性はスクリーンショット圧縮では判別困難なほど繊細なため、canvasの生ピクセルデータ直接検査と拡大レンダリングで実際に描画されていることを確認
  - `npx tsc --noEmit` / `npm run lint` / `npm run build` すべて成功、claude-in-chromeでのdev server目視確認（見出しアニメーション・カードホバー演出・コンソールエラーなし）も完了
- 2026-08-10 追加改善。「Heroセクションが弱い」というフィードバックを受け、Hero背景を`DotGrid`から[reactbits DotField](https://reactbits.dev/backgrounds/dot-field)に差し替え。
  - `DotField`はマウス追従の膨らみ（bulge）演出とカーソル追従グロー（SVG radialGradient）を持ち、gsap非依存（canvas + SVGのみ）。TypeScript移植し`src/components/reactbits/dot-field.tsx`として追加、`dot-grid.tsx`/`dot-grid.css`は用途がなくなったため削除、`gsap`依存も削除
  - グラデーション色はブランドのネイビー→オレンジ（`rgba(15,23,42,0.65)`→`rgba(249,115,22,0.4)`）、グローはブランドオレンジに調整。ドットの半径・不透明度は前回の反省を踏まえ、実際に視認できる強さまで引き上げ
  - reactbits公式実装がSSR非対応の`Math.random()`ベースID生成をしていたためNext.jsでhydration mismatchエラーが発生。React標準の`useId()`に置き換えて解消
  - `npx tsc --noEmit` / `npm run lint` / `npm run build`すべて成功、dev serverでhydrationエラー・コンソールエラーがないことを確認。canvasの生ピクセル検査と拡大レンダリングで実際の描画強度も検証済み
