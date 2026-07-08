# 2刺激判別（昇格あり） — spsm.py

## 1. これは何か（目的）

左右に報酬刺激 `r` と無報酬刺激 `nr` を提示し、報酬刺激へのタッチを学習させる2刺激判別課題です。正答率の窓と閾値により、次の刺激セットへ進む判定を行います。

## 2. いつ使うか（位置づけ）

矩形タッチ訓練の後、画像を使った2択判別を始める段階で使います。両側報酬や画像なしから始める場合は、先に [spsp.py](04_spsp.md) を使います。全体の流れは [PROTOCOL.md](../PROTOCOL.md) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。共有モジュールが同じ場所にある前提。
- `--serial-port` と `--stim-dir` は必須。
- `--sim` と `--dry-run-ttl` はありません。タッチパネルと Arduino TTL が使える実機環境で起動します。
- `--stim-dir` には `stim_NN_r.png` と `stim_NN_nr.png` のペアを置きます。例: `stim_01_r.png`, `stim_01_nr.png`。

## 4. 起動コマンド例

本番最小形:

```bash
cd train/code
python spsm.py --serial-port /dev/ttyACM0 --stim-dir ../visual_stimuli
```

当たり判定を見ながら確認する例:

```bash
cd train/code
python spsm.py --serial-port /dev/ttyACM0 --stim-dir ../visual_stimuli --show-box --info
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--stim-dir` | required | `stim_NN_r.png` / `stim_NN_nr.png` のペアが入ったフォルダ。 |
| `--sliding-n` | `20` | 正答率判定の試行窓。 |
| `--acc-threshold` | `0.8` | 昇格閾値。help では「超えたら」昇格。 |
| `--correction-mode` | off | 失敗後、成功まで前試行と同じ左右配置を繰り返す。 |
| `--exclude-correction-from-acc` | off | 正答率判定からコレクショントライアルを除外する。 |

## 6. 出力CSV

- 保存先: `--out-dir`。既定値は `logs/`。
- ファイル名 prefix: `touch_2stim_log_`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

重要列:

| 列 | 説明 |
|---|---|
| `stim_set` | 刺激セット番号ラベル。 |
| `left_label`, `right_label` | 左右に出た `r` / `nr` ラベル。 |
| `left_image`, `right_image` | 左右画像ファイル。 |
| `target_image`, `non_target_image` | 報酬刺激・無報酬刺激画像。 |
| `trial_index_global`, `trial_index_in_set` | 全体およびセット内の試行番号。 |
| `sliding_n`, `sliding_correct`, `sliding_acc` | 昇格判定に使う窓と成績。 |
| `correction_mode`, `is_correction_trial` | コレクショントライアル情報。 |

## 7. 典型パラメータ

導入用（既定値を明示し、境界を表示）:

```bash
cd train/code
python spsm.py --serial-port /dev/ttyACM0 --stim-dir ../visual_stimuli --sliding-n 20 --acc-threshold 0.8 --show-box
```

本番用（タッチ専用・キオスク）:

```bash
cd train/code
python spsm.py --serial-port /dev/ttyACM0 --stim-dir ../visual_stimuli --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。

## 9. よくある失敗

- `--stim-dir` を指定しない、またはフォルダが存在しないと起動できません。
- `stim_NN_r.png` と `stim_NN_nr.png` の番号が揃っていないと、その番号はペアとして使われません。
- `--serial-port` を指定しないと起動できません。
- `--touch-only` 中はマウスクリックで確認できません。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
