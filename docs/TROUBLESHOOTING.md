# トラブルシューティング

このページは横断的な症状だけを扱います。実験固有のエラーや前提条件は、各ページの「よくある失敗」も確認してください。

## まず確認すること

- 実行ディレクトリは `train/code/` か。
- 目的のスクリプトに必要な必須引数を指定しているか。
- `--serial-port` の値が実機のポート名と一致しているか。
- 刺激画像が必要な課題では `--stim-dir` とファイル名が合っているか。
- 出力先 `--out-dir` に書き込めるか。

共通引数は [./_common_args.md](./_common_args.md)、セットアップ手順は [./SETUP.md](./SETUP.md) を参照してください。

## 症状別

| 症状 | 主な原因 | 対処 |
|---|---|---|
| `ModuleNotFoundError: task_common` などが出る | `train/code/` の共有モジュールが同梱されていない、または実行場所が想定と違う。 | `train/code/` 配下をまとめて配置し、`cd train/code` してから実行する。必要な共有モジュールは [./SETUP.md](./SETUP.md) を参照。 |
| `--serial-port` を指定せず argparse エラーになる | 実機系スクリプトでは `--serial-port` が必須。 | Pi では `ls /dev/ttyACM*`、`ls /dev/ttyUSB*` で確認し、例: `--serial-port /dev/ttyACM0` を指定する。Windows ではデバイスマネージャーで COM 番号を確認する。 |
| シリアルポートが開けない | Arduino 未接続、ポート名違い、別プロセスが使用中、権限不足など。 | Arduino 接続とポート名を確認する。TTL を実送信せず画面だけ確認する場合、`prl.py` と `restless_bandit.py` では `--dry-run-ttl` を使える。 |
| `prl.py` / `restless_bandit.py` の live 実行で即終了する | live 実行で `--dry-run-ttl` も `--serial-port` も指定していない。 | 実機では `--serial-port` を指定する。TTL なし確認では `--dry-run-ttl` を指定する。ヘッドレス確認なら `--sim` を使う。 |
| pygame の初期化に失敗する、画面がない環境で動かない | live 実行には pygame の GUI 表示環境が必要。 | 実機の GUI セッション上で起動する。`prl.py` と `restless_bandit.py` は `--sim` で画面なしの CSV 出力確認ができる。 |
| 画像刺激が出ない、画像フォルダでエラーになる | `--stim-dir` のパス違い、命名規則違い、必要なペア不足。 | `spsm.py`、`rl.py`、`prl.py` では `stim_NN_r.png` / `stim_NN_nr.png` のペアを置く。`spsp.py` は画像ありなら `stim_NN_r.png` を使う。`restless_bandit.py --images` では `stim_NN_r.png` が必要。 |
| 音が出ない | pygame mixer、音声出力デバイス、音量、ビープ設定の問題。 | OS 側の音量と出力先を確認する。`--beep-freq`、`--beep-ms`、`--beep-volume` の値を確認する。 |
| タッチ位置がずれる | タッチパネルのキャリブレーション、解像度、表示設定のずれ。 | 実機のタッチパネル設定を確認する。記入すべきハード情報は [./HARDWARE.md](./HARDWARE.md) を参照。`--show-box` で当たり判定の位置を確認する。 |
| マウスで確認できない | `--touch-only` でマウス入力をブロックしている。 | 開発確認では `--touch-only` を外す。本番運用時だけ必要に応じて付ける。 |
| 終了できない、kiosk で操作しにくい | `--kiosk` はフルスクリーン、カーソル非表示、入力 grab を行う。 | まず `ESC` または `q` を試す。対応スクリプトでは `train/code/` に `STOP` ファイルを置く。ウィンドウを閉じられる場合は閉じる。`touch_rect_center.py` はソース確認上、`STOP` と `q` 終了は確認できず、`ESC` またはウィンドウを閉じる操作が終了条件。 |
| CSV が見つからない | 出力先や prefix を取り違えている。 | 既定保存先は `logs/`。ファイル名 prefix は [./CSV_FORMAT.md](./CSV_FORMAT.md) を確認する。`prl.py --sim` は `prl_sim_log_`、`restless_bandit.py --sim` は `restless_bandit_sim_log_`。 |
| `rl.py` に `--sim` を付けてエラーになる | `rl.py` と `prl.py` の混同。 | `rl.py` に `--sim` はない。ヘッドレス確認が必要なら `prl.py --sim` を使う。違いは [experiments/05_rl.md](experiments/05_rl.md) と [experiments/06_prl.md](experiments/06_prl.md) を参照。 |
| seed を指定せず起動できない | `prl.py` と `restless_bandit.py` は `--seed` 必須。 | 実行コマンドに `--seed <整数>` を入れる。スケジュールや walk を保存する場合は `--save-schedule-json` または `--save-walk-json` を使う。 |

## 刺激画像チェック

画像刺激を使う課題では、`--stim-dir` に指定したフォルダを確認してください。

- `spsm.py`: `stim_NN_r.png` と `stim_NN_nr.png` のペアが必要。
- `rl.py`: `stim_NN_r.png` と `stim_NN_nr.png` のペアが必要。
- `prl.py`: `stim_NN_r.png` と `stim_NN_nr.png` のペアが必要。
- `spsp.py`: 画像ありでは `stim_NN_r.png` を使い、`stim_NN_nr.png` は任意。
- `restless_bandit.py --images`: `stim_NN_r.png` が必要。

配置例と実行例は [./SETUP.md](./SETUP.md) を参照してください。

## 終了方法の注意

多くの実験は次の方法で終了できます。

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。

例外として、`touch_rect_center.py` はソース確認上、`ESC` とウィンドウを閉じる操作だけが終了条件です。kiosk や input grab を使う本番設定は、実験前に終了手順を確認してから使ってください。

## 実験別ページ

- [中央矩形タッチ訓練](experiments/01_touch_rect_center.md)
- [ランダム配置矩形タッチ訓練](experiments/02_touch_rect_random.md)
- [2刺激判別（昇格あり）](experiments/03_spsm.md)
- [2刺激課題（両報酬/画像なし可）](experiments/04_spsp.md)
- [反転学習](experiments/05_rl.md)
- [確率的反転学習](experiments/06_prl.md)
- [restless 2-armed bandit](experiments/07_restless_bandit.md)
- [オブジェクト接触選好](experiments/08_object_explore.md)

## 関連資料

- 入口: [../README.md](../README.md)
- セットアップ: [./SETUP.md](./SETUP.md)
- ハードウェア: [./HARDWARE.md](./HARDWARE.md)
- 共通 CLI 引数: [./_common_args.md](./_common_args.md)
- CSV ログ形式: [./CSV_FORMAT.md](./CSV_FORMAT.md)
- ログ回収: [./LOG_TRANSFER.md](./LOG_TRANSFER.md)
