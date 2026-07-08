# 確率的反転学習 — prl.py

これは試行数ベースの確率的反転学習です。決定論的な反転学習 `rl.py` とは別物で、`prl.py` には `--sim` によるヘッドレス動作確認があります。

## 1. これは何か（目的）

2つの刺激のうち一方を高報酬確率、もう一方を低報酬確率として提示し、ブロック内の指定試行で高確率側を反転します。正答/不正答だけでなく、確率的な報酬結果と反転への適応を記録します。

## 2. いつ使うか（位置づけ）

2刺激判別と `rl.py` の後に使う、確率スケジュールを含む発展課題です。科学的背景メモは [確率的反転学習課題（Banno）](../../Banno/2026-06-22%20確率的反転学習課題.md) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。`task_common.py`、`touch_task_runner.py`、`schedules.py` が同じ場所にある前提。
- `--seed` は必須。
- `--sim` ではタッチパネル、Arduino、刺激画像なしで CSV を出力できます。
- 実機実行では `--stim-dir` と `--serial-port` を指定します。ただし `--dry-run-ttl` を使う場合は TTL 実送信を行いません。
- `--stim-dir` には `stim_NN_r.png` と `stim_NN_nr.png` のペアを置きます。

## 4. 起動コマンド例

ハードなし動作確認（ヘッドレス sim）:

```bash
cd train/code
python prl.py --seed 1 --sim --max-trials 20 --out-dir logs
```

この例の出力ファイル名は `prl_sim_log_YYYYMMDD_HHMMSS.csv` です。

画面は出すが TTL を送らない確認:

```bash
cd train/code
python prl.py --seed 1 --stim-dir ../visual_stimuli --dry-run-ttl --max-trials 5
```

実機本番の最小形:

```bash
cd train/code
python prl.py --seed 1 --stim-dir ../visual_stimuli --serial-port /dev/ttyACM0
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--seed` | required | スケジュール生成とシミュレーションに使う seed。 |
| `--schedule-json` | `None` | 既存スケジュール JSON を読み込む。 |
| `--save-schedule-json` | `None` | 生成したスケジュール JSON を保存する。 |
| `--n-blocks` | `6` | ブロック数。 |
| `--block-len-trials` | `80` | 1ブロックの試行数。 |
| `--reversal-min-trial` | `30` | 反転試行の最小値。 |
| `--reversal-max-trial` | `50` | 反転試行の最大値。 |
| `--schedule-set` | `80-20`; choices: `80-20`, `70-30`, `60-40`, `mixed` | 高/低報酬確率セット。 |
| `--initial-high-label` | `r`; choices: `r`, `nr`, `random` | 最初に高確率側になるラベル。 |
| `--stim-dir` | `stim` | 刺激画像フォルダ。既定は `stim` だが同梱画像は `train/visual_stimuli/` にあるため、`train/code/` から実機起動するときは `--stim-dir ../visual_stimuli` を指定する。 |
| `--stim-per-block` | `cycle`; choices: `cycle`, `fixed` | ブロックごとの刺激セット使用方法。 |
| `--correction-mode` | off | エラー後に隠れたラベルを露出するコレクションを行う。 |
| `--correction-counts-toward-schedule` | off | コレクショントライアルをスケジュール進行に数える。 |
| `--sim` | off | ヘッドレス sim を実行する。 |
| `--sim-choices` | `random`; choices: `high`, `low`, `random`, `alternate` | sim の選択方略。 |
| `--max-trials` | `None` | 最大試行数。 |
| `--max-rewards` | `None` | 最大報酬数。 |
| `--max-session-min` | `None` | 最大セッション分数。 |

## 6. 出力CSV

- live 保存先: `--out-dir`。既定値は `logs/`。
- live prefix: `prl_log_`
- sim prefix: `prl_sim_log_`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

重要列:

| 列 | 説明 |
|---|---|
| `seed` | 起動時 seed。 |
| `schedule_hash` | 使用スケジュールのハッシュ。 |
| `block_index`, `trial_in_block`, `scheduled_reversal_trial` | ブロック内の位置と予定反転試行。 |
| `is_post_reversal` | 反転後かどうか。 |
| `high_label`, `p_high`, `p_low` | 高確率ラベルと報酬確率。 |
| `chosen_label`, `is_correct`, `p_chosen` | 選択ラベルと選択側の確率情報。 |
| `reward_draw`, `reward_won`, `reward_delivered` | 報酬抽選、当選、TTL 配達結果。 |
| `correction_mode`, `is_correction_trial` | コレクション情報。 |

## 7. 典型パラメータ

導入用（sim で短く確認、既定スケジュール）:

```bash
cd train/code
python prl.py --seed 1 --sim --max-trials 20 --schedule-set 80-20 --initial-high-label r
```

本番用（既定ブロック設定、タッチ専用）:

```bash
cd train/code
python prl.py --seed 1 --stim-dir ../visual_stimuli --serial-port /dev/ttyACM0 --n-blocks 6 --block-len-trials 80 --reversal-min-trial 30 --reversal-max-trial 50 --schedule-set 80-20 --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。
- `--max-trials`、`--max-rewards`、`--max-session-min` に達すると終了条件になります。

## 9. よくある失敗

- `rl.py` と混同しないでください。`rl.py` には `--sim` がなく、出力 prefix も `touch_2stim_log_` です。
- `--seed` を指定しないと起動できません。
- live 実行で `--dry-run-ttl` も `--serial-port` も指定しない場合はエラーになります。
- 実機実行で `--stim-dir` に `stim_NN_r.png` / `stim_NN_nr.png` のペアがないと起動できません。
- sim 出力を探すときは `prl_log_` ではなく `prl_sim_log_` を見ます。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
