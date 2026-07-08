# オブジェクト接触選好(ERC/PEC/FOV/ABA_A/ABA_B) — object_explore.py

## 1. これは何か（目的）

5種類のインタラクション対象への接触・選択・報酬を記録するオブジェクト探索課題です。`ERC`、`PEC`、`FOV`、`ABA_A`、`ABA_B` の session type を切り替えて、自由接触、選択、プローブ、ABA デザインを実行します。

## 2. いつ使うか（位置づけ）

2択タッチが成立した後、刺激画像ではなくインタラクション対象への選好や接触行動を見る段階で使います。全体の流れは [PROTOCOL.md](../PROTOCOL.md) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。共有モジュールが同じ場所にある前提。
- `--serial-port`、`--session-type`、`--subject-id` は必須。
- `--sim` と `--dry-run-ttl` はありません。タッチパネルと Arduino TTL が使える実機環境で起動します。
- 刺激画像フォルダは使いません。
- `--session-type` は `ERC`、`PEC`、`FOV`、`ABA_A`、`ABA_B` のいずれかです。

## 4. 起動コマンド例

ERC:

```bash
cd train/code
python object_explore.py --session-type ERC --subject-id 280 --serial-port /dev/ttyACM0
```

PEC:

```bash
cd train/code
python object_explore.py --session-type PEC --subject-id 280 --serial-port /dev/ttyACM0
```

FOV:

```bash
cd train/code
python object_explore.py --session-type FOV --subject-id 280 --serial-port /dev/ttyACM0
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--session-type` | required; choices: `ERC`, `PEC`, `FOV`, `ABA_A`, `ABA_B` | セッション種類。`ABA_A` は FOV baseline、`ABA_B` は ERC reward phase。 |
| `--subject-id` | required | 被験体 ID。 |
| `--block-id` | `A`; choices: `A`, `B` | ERC/PEC の BIBD block。 |
| `--target-trials` | `50` | ERC/PEC の目標試行数。 |
| `--max-duration-s` | `1200` | セッションの最大秒数。 |
| `--zone-size-px` | `280` | ERC/PEC の interaction zone サイズ。 |
| `--fov-zone-size-px` | `200` | FOV の五角形レイアウト zone サイズ。 |
| `--fov-rotation` | `0` | FOV 五角形の回転。72度単位で `0`〜`4`。 |
| `--fov-max-duration-s` | `300` | FOV 最大秒数。 |
| `--fov-inactivity-timeout-s` | `120` | FOV で無タッチが続いたときの自動終了秒数。 |
| `--present-duration-ms` | `500` | PRESENT phase の長さ。 |
| `--choose-timeout-s` | `60` | 選択待ちの最大秒数。 |
| `--interact-max-s` | `10` | INTERACT phase の最大秒数。 |
| `--interact-disengage-s` | `3` | 無接触で INTERACT を終える秒数。 |
| `--reward-cooldown-ms` | `2000` | INTERACT 中の報酬間隔。 |
| `--max-rewards-per-trial` | `5` | 1試行あたり最大報酬数。 |
| `--max-rewards-per-session` | `80` | 1セッションあたり最大報酬数。 |
| `--probe-rate` | `0.15` | PEC の unrewarded probe 割合。 |
| `--bias-threshold` | `0.8` | 側方バイアス判定閾値。 |
| `--bias-window` | `10` | バイアス判定に使う直近試行数。 |
| `--bias-correction-trials` | `3` | バイアス検出時の correction 試行数。 |
| `--max-consecutive-omissions` | `3` | 連続 omission による auto-pause 閾値。 |

### ERC

`ERC` は equal-reward choice です。2つの interaction を左右に出し、選択後の INTERACT phase で報酬を出します。出力 prefix は `erc_log_{subject_id}_` です。

### PEC

`PEC` は probe-embedded choice です。ERC と同じ trial-based 経路を使い、`--probe-rate` により unrewarded probe trial を混ぜます。出力 prefix は `pec_log_{subject_id}_` です。

### FOV

`FOV` は free-operant validation です。5種類の interaction を五角形レイアウトに出し、接触数と累積接触を記録します。`--fov-max-duration-s` と `--fov-inactivity-timeout-s` により自動終了します。出力 prefix は `fov_log_{subject_id}_` です。

### ABA_A・ABA_B

`ABA_A` は FOV baseline として扱われます。出力 prefix は `aba_a_log_{subject_id}_` です。`ABA_B` は ERC reward phase として扱われます。出力 prefix は `aba_b_log_{subject_id}_` です。

## 6. 出力CSV

- 保存先: `--out-dir`。既定値は `logs/`。
- prefix: `ERC -> erc`, `PEC -> pec`, `FOV -> fov`, `ABA_A -> aba_a`, `ABA_B -> aba_b`
- ファイル名: `{prefix}_log_{subject_id}_YYYYMMDD_HHMMSS.csv`
- CSV スキーマは2系統です。
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

FOV / ABA_A 系 19列の重要列:

| 列 | 説明 |
|---|---|
| `session_type`, `subject_id` | セッション種別と被験体 ID。 |
| `interaction_hit` | 接触した interaction。 |
| `touch_id` | タッチ ID。 |
| `fov_elapsed_s` | FOV 経過秒。 |
| `fov_cumul_bubble_pond` | `bubble_pond` 累積接触。 |
| `fov_cumul_sound_ball` | `sound_ball` 累積接触。 |
| `fov_cumul_squash_blob` | `squash_blob` 累積接触。 |
| `fov_cumul_peekaboo` | `peekaboo` 累積接触。 |
| `fov_cumul_particle_attractor` | `particle_attractor` 累積接触。 |
| `total_touches`, `session_duration_s` | 総タッチ数とセッション時間。 |

ERC / PEC / ABA_B 系 41列の重要列:

| 列 | 説明 |
|---|---|
| `trial_num`, `pair` | 試行番号と interaction ペア。 |
| `left_interaction`, `right_interaction` | 左右の interaction。 |
| `chosen_interaction`, `chosen_side` | 選択された interaction と左右。 |
| `is_probe` | PEC の probe trial かどうか。 |
| `reward_given` | 報酬が出たか。 |
| `choice_latency_ms` | 選択までの時間。 |
| `interact_duration_ms` | INTERACT phase の長さ。 |
| `interact_touch_count` | INTERACT 中のタッチ数。 |
| `trial_reward_count` | 試行内報酬数。 |
| `left_choices_recent`, `right_choices_recent` | 直近の左右選択数。 |
| `is_correction_trial` | バイアス correction trial かどうか。 |
| `total_trials`, `total_touches`, `total_rewards` | セッション累積値。 |
| `omission_count` | omission 数。 |

## 7. 典型パラメータ

導入用（FOV、既定の5分上限）:

```bash
cd train/code
python object_explore.py --session-type FOV --subject-id 280 --serial-port /dev/ttyACM0 --fov-max-duration-s 300 --fov-inactivity-timeout-s 120 --show-box
```

本番用（ERC、既定50試行、タッチ専用）:

```bash
cd train/code
python object_explore.py --session-type ERC --subject-id 280 --serial-port /dev/ttyACM0 --target-trials 50 --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。
- FOV / ABA_A は `--fov-max-duration-s` または `--fov-inactivity-timeout-s` により自動終了します。
- trial-based session は `--target-trials`、`--max-duration-s`、報酬上限などにより終了します。

## 9. よくある失敗

- `--session-type`、`--subject-id`、`--serial-port` のどれかを忘れると起動できません。
- `--session-type` は大文字の choices どおりに指定します。例: `ERC`, `PEC`, `FOV`, `ABA_A`, `ABA_B`。
- FOV / ABA_A と ERC / PEC / ABA_B では CSV 列数が違います。同じ解析スクリプトで混ぜる前に header を確認してください。
- `ABA_A` は FOV 経路、`ABA_B` は ERC 経路です。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
