# セットアップ

Raspberry Pi 受領後、実験を起動できる状態にする手順です。コマンド例はリポジトリ直下を `Homecage-TR/` として書いています。

## Python と依存関係

対象 Python は `requirements.txt` に従い 3.8-3.11 です。

```bash
cd Homecage-TR
python --version
python -m pip install -r requirements.txt
```

`requirements.txt` は最小バージョンだけを指定しています。

- `pygame>=2.1.0`
- `pyserial>=3.5`

実機で動作確認した後、推測で版を決めず、Pi 上の実インストール状態を固定します。

```bash
python -m pip freeze > requirements.lock.txt
```

なお、ログ回収ユーティリティ `pi2win.py` は `paramiko` / `scp` を追加で使います（`requirements.txt` には含めていません。未導入時は実行時に自動インストールを試みます）。オフライン運用や版固定が必要な場合は事前に導入してください。詳細は [LOG_TRANSFER.md](./LOG_TRANSFER.md) を参照。

## 実験コードの配置

実験スクリプトは `train/code/` で実行します。

```bash
cd train/code
python prl.py --help
```

`train/code/` 配下の共有モジュールも同じディレクトリに必要です。

- `task_common.py`
- `touch_task_runner.py`
- `schedules.py`

`train/architecture_analysis.md` には「各タスクファイルは自己完結」とする旧版の記述がありますが、現在の `prl.py`、`restless_bandit.py` などは共有モジュールを import するため、その記述は古いです。

## 刺激画像

`spsm.py`、`rl.py`、`prl.py` など、画像刺激を使う課題では `--stim-dir` に刺激画像ディレクトリを指定します。ファイル名は次の形式です。

- `stim_NN_r.png`
- `stim_NN_nr.png`

配置例は `train/visual_stimuli/` です。`train/code/` から実行する場合は次のように指定します。

```bash
python rl.py --stim-dir ../visual_stimuli --serial-port /dev/ttyACM0
```

## Arduino TTL の準備

Arduino IDE で [../train/pd_ttl/pd_ttl.ino](../train/pd_ttl/pd_ttl.ino) を Arduino に書き込みます。スケッチのボーレートは `115200` です。Python 側の既定 `--serial-baud` も `115200` です。

## シリアルポート確認

Pi では次を確認します。

```bash
ls /dev/ttyACM*
ls /dev/ttyUSB*
```

Windows ではデバイスマネージャーで COM 番号を確認します。確認した値を `--serial-port` に指定します。

```bash
python touch_rect_random.py --serial-port /dev/ttyACM0
python touch_rect_random.py --serial-port COM3
```

## ハードなしの最短動作確認

`prl.py` の sim は画面、pygame、シリアル接続なしで CSV を出せます。

```bash
cd train/code
python prl.py --sim --seed 1 --sim-choices high
```

既定では `train/code/logs/` に `prl_sim_log_YYYYMMDD_HHMMSS.csv` が出ます。

## 実験運用時の表示モード

本番運用では必要に応じて次を付けます。

```bash
--fullscreen --kiosk --touch-only
```

共通引数の詳細は [./_common_args.md](./_common_args.md) を参照してください。

## テスト

配布前または変更後は、`train/code/` で unittest を実行します。

```bash
cd train/code
python -m unittest test_prl test_restless_bandit test_schedules test_touch_task_runner
```

期待件数は35件です。
