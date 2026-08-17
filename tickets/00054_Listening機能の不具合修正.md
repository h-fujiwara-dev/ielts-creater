# #00054 Listening機能の不具合修正（音声再生・回答・問題数不一致）

- 対象リポジトリ: frontend / backend
- ステータス: 完了
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

- [x] 音声再生ボタンで音声が正しく再生できる
- [x] Listening問題に回答でき、回答内容が保存される
- [x] 出題された問題数と結果確認画面の問題数が一致する
- [x] 関連するUnit Testを追加する
- [x] frontend/backendそれぞれでlint/test/buildが通る

## 作業ログ

- 2026-08-13 起票
- 2026-08-13 調査の結果、報告された3件はそれぞれ別原因と判明し、frontend/backend計4件のPRで対応・developへマージ済み
  - 音声再生不可: frontend [PR #39](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/39)（`<audio src>`への直接URL指定で401になっていた問題をBlob取得+Object URLに変更）/ [PR #40](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/40)（audio要素マウント後にtimeupdate/endedリスナーが付与されない不具合を修正）
  - 回答不可: backend [PR #19](https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/19)（frontend未対応のFORM_COMPLETION/NOTE_COMPLETION形式で生成していたためListening問題に回答できなかった不具合を、対応済みのFILL_BLANK形式に統一）
  - 問題数不一致: backend [PR #20](https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/20)（設問番号`displayOrder`がQuestionGroupごとに1からリセットされていた不具合を通し連番に修正）
  - チケットのステータス更新が漏れていたため今回反映
