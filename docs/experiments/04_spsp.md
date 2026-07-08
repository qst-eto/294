# 2刺激課題（両報酬/画像なし可） — spsp.py

## 1. これは何か（目的）

左右どちらを触っても正解として扱う2刺激課題です。画像ありでも、画像なしでプレートだけでも実行でき、2択画面に慣らす段階で使います。

## 2. いつ使うか（位置づけ）

矩形タッチ訓練の後、2つの選択肢が出る画面に慣らすときに使います。その後、報酬刺激と無報酬刺激を分ける [spsm.py](03_spsm.md) に進みます。全体の流れは [PROTOCOL.md](../PROTOCOL.md) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。共有モジュールが同じ場所にある前提。
- `--serial-port` は必須。
- `--sim` と `--dry-run-ttl` はありません。タッチパネルと Arduino TTL が使える実機環境で起動します。
- 画像を使う場合は `--stim-dir` を指定します。`stim_NN_r.png` を使い、`stim_NN_nr.png` は任意です。
- 画像を使わない場合は `--no-images` を指定します。

## 4. 起動コマンド例

画像なしの最小形:

```bash
cd train/code
python spsp.py --serial-port /dev/ttyACM0 --no-images
```

画像ありの例:

```bash
cd train/code
python spsp.py --serial-port /dev/ttyACM0 --stim-dir ../visual_stimuli
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--no-images` | off | 画像なしモード。プレートのみ表示する。 |
| `--dummy-sets` | `1` | 画像なしモードで作るダミーセット数。 |
| `--stim-dir` | optional | 画像を使う場合のフォルダ。 |
| `--sliding-n` | `20` | 正答率判定の試行窓。 |
| `--acc-threshold` | `0.8` | 昇格閾値。help では「超えたら」昇格。 |
| `--correction-mode` | off | 失敗後、同じ左右配置を繰り返す。 |
| `--exclude-correction-from-acc` | off | 正答率判定からコレクショントライアルを除外する。 |

## 6. 出力CSV

- 保存先: `--out-dir`。既定値は `logs/`。
- ファイル名 prefix: `touch_2stim_log_`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

重要列:

| 列 | 説明 |
|---|---|
| `stim_set` | 刺激セット番号ラベル。画像なしではダミーセット。 |
| `left_label`, `right_label` | 左右ラベル。両側報酬課題のため両側が報酬側として扱われる。 |
| `left_image`, `right_image` | 画像ありの場合の左右画像。 |
| `trial_outcome`, `fail_reason` | 試行結果と失敗理由。 |
| `sliding_n`, `sliding_correct`, `sliding_acc` | 昇格判定に使う窓と成績。 |
| `correction_mode`, `is_correction_trial` | コレクショントライアル情報。 |

## 7. 典型パラメータ

導入用（画像なし、既定のダミーセット数）:

```bash
cd train/code
python spsp.py --serial-port /dev/ttyACM0 --no-images --dummy-sets 1 --show-box
```

本番用（画像あり・タッチ専用）:

```bash
cd train/code
python spsp.py --serial-port /dev/ttyACM0 --stim-dir ../visual_stimuli --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。

## 9. よくある失敗

- 画像なしで実行したいのに `--no-images` を付け忘れると、`--stim-dir` が必要になります。
- `--stim-dir` のフォルダ自体が存在しないと `RuntimeError`（刺激ディレクトリが見つかりません）で停止します。フォルダはあるが中に画像が無い場合のみ、警告を出して画像なしモードへ自動切替えされます。
- `--serial-port` を指定しないと起動できません。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
