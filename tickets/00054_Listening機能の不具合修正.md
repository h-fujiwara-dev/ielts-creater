# #00054 Listening機能の不具合修正（音声再生・回答・問題数不一致）

- 対象リポジトリ: frontend / backend
- ステータス: 未対応
- type: fix
- 関連ドキュメント: [システム要件定義書 F-06 音声再生 / F-07 回答入力・自動保存 / F-09 結果・解説表示](../docs/システム要件定義書.md)

## 実現すること

dev環境での手動確認の結果、Listening機能で以下3件の不具合が見つかった。いずれも原因未特定のため、実装着手時に調査した上で修正する。

1. 音声の再生ボタンを押しても再生できない
2. 問題に回答できない
3. 出題された問題数と結果確認画面に表示される問題数が異なる

調査の起点として想定される実装箇所:

- frontend: `src/components/answer/audio-player.tsx` / `answer-screen.tsx` / `listening-layout.tsx`
- backend: `questionset/listening/`配下の`PollyListeningAudioSynthesizer.java` / `S3StorageService.java` / `LocalDiskStorageService.java`（音声ファイルの配信元がローカルディスクかS3かで再生失敗の原因が変わりうる）

## 受け入れ条件

- [ ] 音声再生ボタンで音声が正しく再生できる
- [ ] Listening問題に回答でき、回答内容が保存される
- [ ] 出題された問題数と結果確認画面の問題数が一致する
- [ ] 関連するUnit Testを追加する
- [ ] frontend/backendそれぞれでlint/test/buildが通る

## 作業ログ

- 2026-08-13 起票
