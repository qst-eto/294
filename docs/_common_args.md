# 共通引数リファレンス

この資料は `train/code/` 内の実験スクリプトを直接実行する前提です。`prl.py` と `restless_bandit.py` は `task_common.py`、`touch_task_runner.py`、`schedules.py` など同じディレクトリの共有モジュールを import します。配布時は `train/code/` をまとめて置き、実行もその中で行ってください。

```bash
cd train/code
python <script>.py --help
```

## 画面・入力

| 引数 | 対象 | 説明 |
|---|---|---|
| `--fullscreen` | 全実験 | フルスクリーン表示。未指定時はウィンドウ表示。 |
| `--window-w` / `--window-h` | 全実験 | ウィンドウ表示時の幅・高さ。多くのスクリプトで既定値は `1280` / `720`。 |
| `--kiosk` | 全実験 | フルスクリーン、カーソル非表示、入力 grab。実験運用向け。 |
| `--touch-only` | 全実験 | マウス入力をブロックし、タッチ入力だけを受ける。 |
| `--bg-rgb R G B` | 全実験 | 背景色。多くのスクリプトで既定値は `0 0 0`。 |
| `--show-box` | 全実験 | 当たり判定やゾーン境界を画面に表示する確認用。 |
| `--info` | 全実験 | デバッグ情報のオーバーレイを表示する。 |

## 出力先

| 引数 | 対象 | 説明 |
|---|---|---|
| `--out-dir DIR` | 全実験 | CSV 保存先。既定値は `logs`。ファイル名 prefix は各実験ページを参照。 |

## シリアル・TTL

| 引数 | 対象 | 説明 |
|---|---|---|
| `--serial-port PORT` | 実機実行で使用 | Arduino TTL のシリアルポート。`touch_rect_center.py`、`touch_rect_random.py`、`spsm.py`、`spsp.py`、`rl.py`、`object_explore.py` では required。 |
| `--serial-baud BAUD` | シリアル使用タスク | 既定値は `115200`。 |
| `--dry-run-ttl` | `prl.py`、`restless_bandit.py` のみ | TTL パルスを実送信せずに実行する。`--sim` とは別で、画面あり実行の TTL だけを止めるための確認用。 |
| `--sim` | `prl.py`、`restless_bandit.py` のみ | ヘッドレスのシミュレーション。タッチスクリーン、Arduino、刺激画像なしで CSV を出力する。 |

`rl.py` には `--sim` はありません。`prl.py` の `--sim` と混同しないでください。

## ITI

| 引数 | 対象 | 説明 |
|---|---|---|
| `--iti-min-ms` / `--iti-max-ms` | 全実験 | 基本 ITI 範囲。矩形・2刺激・PRL・bandit では既定値 `1000` / `1000`、object explore では `2000` / `4000`。 |
| `--iti-correct-min-ms` / `--iti-correct-max-ms` | 矩形、`spsm.py`、`spsp.py`、`rl.py` | 正答後 ITI。未指定なら `--iti-min-ms` / `--iti-max-ms` を使う。 |
| `--iti-error-min-ms` / `--iti-error-max-ms` | 矩形、`spsm.py`、`spsp.py`、`rl.py` | エラー後 ITI。未指定なら `--iti-min-ms` / `--iti-max-ms` を使う。 |
| `--iti-rewarded-min-ms` / `--iti-rewarded-max-ms` | `prl.py`、`restless_bandit.py` | 報酬あり試行後 ITI。未指定なら基本 ITI を使う。 |
| `--iti-unrewarded-min-ms` / `--iti-unrewarded-max-ms` | `prl.py`、`restless_bandit.py` | 報酬なし試行後 ITI。未指定なら基本 ITI を使う。 |
| `--iti-outside-min-ms` / `--iti-outside-max-ms` | `prl.py`、`restless_bandit.py` | 外側タッチ失敗後 ITI。未指定なら基本 ITI を使う。 |

## 音

| 引数 | 対象 | 説明 |
|---|---|---|
| `--beep-freq` | 全実験 | ビープ周波数。既定値は `1000`。 |
| `--beep-ms` | 全実験 | ビープ長。既定値は `100`。 |
| `--beep-volume` | 全実験 | ビープ音量。既定値は `0.6`。 |

## 解放待ち・外側タッチ

| 引数 | 対象 | 説明 |
|---|---|---|
| `--wait-release-timeout-ms` | 全実験 | WAIT_RELEASE が長引いたときに強制的に次へ進むフェイルセーフ。多くのスクリプトで既定値は `2000`、object explore では `5000`。 |
| `--min-release-ms-after-iti-touch` | 全実験 | ITI 中タッチ後、次試行前に必要な連続解放時間。多くのスクリプトで既定値は `2000`。 |
| `--max-outside-before-fail` | 矩形、2刺激、PRL、bandit | SHOW 状態で外側タッチを何回まで許すか。既定値は `5`。 |
| `--hit-margin-px` | `touch_rect_random.py`、2刺激、PRL、bandit、object explore | ターゲット外側に追加する当たり判定マージン。既定値は多くで `0`、object explore では `20`。 |

## 刺激・プレート表示

| 引数 | 対象 | 説明 |
|---|---|---|
| `--stim-px` | 2刺激、`prl.py` | 刺激を正方形で表示する一辺 px。`spsm.py`、`spsp.py`、`rl.py` の既定値は `240`。 |
| `--stim-w` / `--stim-h` | 2刺激、`prl.py` | `--stim-px` を使わないときの幅・高さ。片方だけ指定しない。 |
| `--plate-px` | 2刺激、PRL、bandit | プレートを正方形で表示する一辺 px。 |
| `--plate-w` / `--plate-h` | 2刺激、PRL、bandit | `--plate-px` を使わないときの幅・高さ。PRL/bandit では片方だけ指定するとエラー。 |
| `--plate-rgb R G B` | 2刺激、PRL、bandit | プレート色。 |
| `--center-offset-px` | 2刺激、PRL、bandit、object explore | 画面中心から左右ターゲット中心までの水平距離。 |
| `--edge-margin-px` | 2刺激、PRL、bandit、object explore | 画面端との最小マージン。 |

## 終了方法

`touch_rect_random.py`、`spsm.py`、`spsp.py`、`rl.py`、`prl.py`、`restless_bandit.py`、`object_explore.py` は次で終了できます。

- 作業ディレクトリ、つまり `train/code/` に `STOP` ファイルを置く
- ウィンドウを閉じる
- `ESC` または `q` を押す

`touch_rect_center.py` はソース確認上、`ESC` とウィンドウを閉じる操作だけが終了条件です。
