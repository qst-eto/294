# restless 2-armed bandit — restless_bandit.py

## 1. これは何か（目的）

左右2つの選択肢の報酬確率が試行ごとにランダムウォークする restless bandit 課題です。どちらの選択肢が高確率かが固定されない状況で、選択と報酬結果を記録します。

## 2. いつ使うか（位置づけ）

2択課題に慣れた後の発展課題です。確率的反転学習とは違い、ブロック内の一回反転ではなく、左右の報酬確率が連続的に変化します。関連文献は [Chen et al. 2021 (eLife)](../../Banno/Chen+2021_eLife.pdf) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。`task_common.py`、`touch_task_runner.py`、`schedules.py` が同じ場所にある前提。
- `--seed` は必須。
- `--sim` ではタッチパネル、Arduino、刺激画像なしで CSV を出力できます。
- 実機で画像を使わない場合は、白い正方形を表示します。
- 画像を使う場合は `--images --stim-dir DIR` を指定します。検出対象は `stim_NN_r.png` です。
- 実機 live 実行で TTL を送る場合は `--serial-port` を指定します。`--dry-run-ttl` では TTL 実送信を止めます。

## 4. 起動コマンド例

ハードなし動作確認（ヘッドレス sim）:

```bash
cd train/code
python restless_bandit.py --seed 1 --sim --max-trials 20 --out-dir logs
```

この例の出力ファイル名は `restless_bandit_sim_log_YYYYMMDD_HHMMSS.csv` です。

画面は出すが TTL を送らない確認:

```bash
cd train/code
python restless_bandit.py --seed 1 --dry-run-ttl --max-trials 5
```

実機本番の最小形:

```bash
cd train/code
python restless_bandit.py --seed 1 --serial-port /dev/ttyACM0
```

画像あり実行:

```bash
cd train/code
python restless_bandit.py --seed 1 --serial-port /dev/ttyACM0 --images --stim-dir ../visual_stimuli
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--seed` | required | walk 生成と sim に使う seed。 |
| `--walk-json` | `None` | 既存 walk JSON を読み込む。 |
| `--save-walk-json` | `None` | 生成した walk JSON を保存する。 |
| `--n-trials` | `300` | walk の試行数。 |
| `--step-prob-frac` | `0.10` | 確率が変化するステップ確率。 |
| `--step-size-frac` | `0.10` | 変化幅。 |
| `--p-init-left-frac` / `--p-init-right-frac` | `0.50` / `0.50` | 左右の初期報酬確率。 |
| `--p-floor-frac` / `--p-ceil-frac` | `0.10` / `0.90` | 報酬確率の下限・上限。 |
| `--balance-tol-frac` | `0.02` | 左右報酬確率の balance 許容値。 |
| `--double-low-thresh-frac` | `0.20` | 両側低確率判定の閾値。 |
| `--double-low-max-run` | `29` | 両側低確率が続く最大試行数。 |
| `--boundary-mode` | `reject-step`; choices: `reject-step`, `reflect`, `reject-walk` | 境界到達時の処理。 |
| `--max-walk-generation-attempts` | `10000` | walk 生成の最大試行回数。 |
| `--images` | off | 画像を使う。 |
| `--stim-dir` | `None` | `--images` 使用時の画像フォルダ。 |
| `--square-px` | `240` | 画像なし表示の正方形一辺 px。 |
| `--square-rgb` | `255 255 255` | 画像なし表示の正方形色。 |
| `--sim` | off | ヘッドレス sim を実行する。 |
| `--sim-choices` | `random`; choices: `higher`, `lower`, `left`, `right`, `random` | sim の選択方略。 |
| `--max-trials` | `None` | 最大試行数。 |
| `--max-rewards` | `None` | 最大報酬数。 |
| `--max-session-min` | `None` | 最大セッション分数。 |

## 6. 出力CSV

- live 保存先: `--out-dir`。既定値は `logs/`。
- live prefix: `restless_bandit_log_`
- sim prefix: `restless_bandit_sim_log_`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

重要列:

| 列 | 説明 |
|---|---|
| `seed` | 起動時 seed。 |
| `walk_hash` | 使用 walk のハッシュ。 |
| `n_trials`, `trial_index` | walk 試行数と現在試行番号。 |
| `p_left`, `p_right` | その試行の左右報酬確率。 |
| `chosen_side`, `p_chosen` | 選択側と選択側確率。 |
| `chose_higher_p` | 高確率側を選んだか。 |
| `reward_draw`, `reward_won`, `reward_delivered` | 報酬抽選、当選、TTL 配達結果。 |
| `step_prob`, `step_size`, `p_floor`, `p_ceil` | walk 生成パラメータ。 |
| `balance_metric` | walk の balance 指標。 |

## 7. 典型パラメータ

導入用（sim で短く確認、既定 walk）:

```bash
cd train/code
python restless_bandit.py --seed 1 --sim --max-trials 20 --n-trials 300 --sim-choices random
```

本番用（画像なし、タッチ専用）:

```bash
cd train/code
python restless_bandit.py --seed 1 --serial-port /dev/ttyACM0 --n-trials 300 --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。
- `--max-trials`、`--max-rewards`、`--max-session-min` に達すると終了条件になります。

## 9. よくある失敗

- `--seed` を指定しないと起動できません。
- sim 出力を探すときは `restless_bandit_log_` ではなく `restless_bandit_sim_log_` を見ます。
- `--images` を付けた場合、`--stim-dir` が必要です。
- `--images` 用フォルダに `stim_NN_r.png` がないと起動できません。
- live 実行で `--dry-run-ttl` も `--serial-port` も指定しない場合はエラーになります。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
