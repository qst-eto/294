# CSV ログ形式

この資料では、ソースコードで確認できた列名、`state`、`event` だけを記載します。ここにない列を解析で仮定しないでください。全列順は各スクリプト（`train/code/<script>.py`）の `fieldnames` / `CSV_FIELDNAMES` 定義を正本として参照してください。

## 保存先とファイル名

既定の保存先は `logs/` です。全ファイル名の末尾は `_YYYYMMDD_HHMMSS.csv` です。

| スクリプト | 出力ファイル |
|---|---|
| `touch_rect_center.py` | `touch_rect_log_YYYYMMDD_HHMMSS.csv` |
| `touch_rect_random.py` | `touch_rect_log_YYYYMMDD_HHMMSS.csv` |
| `spsm.py` | `touch_2stim_log_YYYYMMDD_HHMMSS.csv` |
| `spsp.py` | `touch_2stim_log_YYYYMMDD_HHMMSS.csv` |
| `rl.py` | `touch_2stim_log_YYYYMMDD_HHMMSS.csv` |
| `prl.py` | live: `prl_log_YYYYMMDD_HHMMSS.csv` / sim: `prl_sim_log_YYYYMMDD_HHMMSS.csv` |
| `restless_bandit.py` | live: `restless_bandit_log_YYYYMMDD_HHMMSS.csv` / sim: `restless_bandit_sim_log_YYYYMMDD_HHMMSS.csv` |
| `object_explore.py` | `{erc|pec|fov|aba_a|aba_b}_log_{subject_id}_YYYYMMDD_HHMMSS.csv` |

`touch_rect_center.py` と `touch_rect_random.py` は同じ prefix です。`spsm.py`、`spsp.py`、`rl.py` も同じ prefix です。ファイル名だけで課題を判定せず、実行記録、列数、列名も確認してください。

## 共通列

| 列 | 意味 |
|---|---|
| `start_iso` | セッション開始時刻。ISO 形式、ミリ秒精度。 |
| `iso` | 行を書いた時刻。ISO 形式、ミリ秒精度。 |
| `rel_s` | セッション開始からの相対秒。 |
| `state` | その行を書いた時点の状態名。値は下表参照。 |
| `x`, `y` | タッチ座標。タッチに対応しない行では `-1` が入ることがあります。 |
| `event` | 発生したイベント名。値は下表参照。 |
| `iti_ms` | ITI 長。ms。列がある課題で、該当しない行は空または `0`。 |
| `iti_kind` | ITI 種別。`correct`、`error`、`rewarded`、`unrewarded`、`outside` など、課題により異なります。 |
| `hit_area` | 当たり判定領域。矩形課題では `core` / `margin` / `outside`、2択課題では `left_core` / `left_margin` / `right_core` / `right_margin` / `outside` など。 |
| `outside_in_trial` | その試行内の外側タッチ回数。 |
| `max_outside_before_fail` | 外側タッチで失敗扱いにする上限回数。 |
| `trial_outcome` | 試行結果。`success`、`error`、`correct`、`incorrect`、`choice`、`outside` など。 |
| `fail_reason` | 失敗理由。例: `outside_limit`、`touched_nr`、`touched_non_target`。 |
| `release_dwell_required_ms`, `release_dwell_elapsed_ms` | `touch_rect_center.py` / `touch_rect_random.py` の離し待ち計測値。ms。 |

## 課題固有列の要点

### touch_rect 系

`touch_rect_center.py` は `rect_w`、`rect_h` を記録します。`touch_rect_random.py` は加えて `rect_x`、`rect_y`、`hit_margin_px`、`hit_area` を記録します。

### spsm / spsp / rl

2刺激課題は、左右の画像矩形 `left_x` など、判定用プレート矩形 `left_plate_x` など、刺激情報 `stim_set`、`left_label`、`right_label`、`left_image`、`right_image`、`target_image`、`non_target_image`、試行番号 `trial_index_global` / `trial_index_in_set`、成績窓 `sliding_n` / `sliding_correct` / `sliding_acc`、コレクション試行 `correction_mode` / `is_correction_trial` を持ちます。

`rl.py` には反転学習用に `target_label`、`reversal_count_in_set`、`reversals_per_set`、`reversal_event`、`reversal_idx`、`reversal_trigger_acc` が追加されます。

### prl

`prl.py` は確率的反転学習のスケジュール情報として `seed`、`schedule_hash`、`block_index`、`trial_in_block`、`scheduled_reversal_trial`、`is_post_reversal` を記録します。選択と報酬は `high_label`、`p_high`、`p_low`、`chosen_label`、`is_correct`、`p_chosen`、`reward_draw`、`reward_won`、`reward_delivered` を見ます。

sim CSV では `start_iso`、`iso`、`rel_s` は空、`state` は `SIM`、`event` は `SIM_CHOICE` です。

### restless_bandit

`restless_bandit.py` は `seed`、`walk_hash`、`n_trials`、`trial_index`、`p_left`、`p_right`、`chosen_side`、`p_chosen`、`chose_higher_p`、`reward_draw`、`reward_won`、`reward_delivered` を記録します。walk 生成条件として `step_prob`、`step_size`、`p_floor`、`p_ceil`、`balance_tol`、`double_low_thresh`、`double_low_max_run`、`boundary_mode`、`balance_metric` も入ります。

sim CSV では `start_iso`、`iso`、`rel_s` は空、`state` は `SIM`、`event` は `SIM_CHOICE` です。

### object_explore

`FOV` / `ABA_A` 系は19列です。代表列は `session_type`、`subject_id`、`interaction_hit`、`touch_id`、`fov_elapsed_s`、`fov_cumul_bubble_pond`、`fov_cumul_sound_ball`、`fov_cumul_squash_blob`、`fov_cumul_peekaboo`、`fov_cumul_particle_attractor`、`total_touches`、`session_duration_s` です。

`ERC` / `PEC` / `ABA_B` 系は41列です。代表列は `trial_num`、`pair`、`left_interaction`、`right_interaction`、`chosen_interaction`、`chosen_side`、`is_probe`、`reward_given`、`choice_latency_ms`、`interact_duration_ms`、`interact_touch_count`、`trial_reward_count`、`touch_id`、`interaction_hit`、`phase`、左右ゾーン列、`left_choices_recent`、`right_choices_recent`、`is_correction_trial`、`total_trials`、`total_touches`、`total_rewards`、`session_duration_s`、`omission_count` です。

## state 語彙

| 対象 | `state` 値 |
|---|---|
| `touch_rect_center.py`, `touch_rect_random.py`, `spsm.py`, `spsp.py`, `rl.py`, `prl.py` live, `restless_bandit.py` live | `SHOW`, `ITI`, `WAIT_RELEASE` |
| `prl.py` sim, `restless_bandit.py` sim | `SIM` |
| `object_explore.py` FOV / ABA_A | `FREE_EXPLORE` |
| `object_explore.py` ERC / PEC / ABA_B | `PRESENT`, `CHOOSE`, `INTERACT`, `ITI`, `WAIT_RELEASE`, `PAUSED` |

## event 語彙

| 対象 | `event` 値 | 意味 |
|---|---|---|
| touch_rect center | `TOUCH_TTL`, `TOUCH_TTL_FAIL` | 矩形内タッチ。TTL 成功または失敗。 |
| touch_rect random | `RECT_PLACED` | 新しい矩形位置を提示。 |
| touch_rect 系 | `TOUCH_OUTSIDE`, `FAIL_OUTSIDE_LIMIT` | 矩形外タッチ、または外側タッチ上限到達。 |
| touch_rect 系 | `TOUCH_ITI_INSIDE`, `TOUCH_ITI_OUTSIDE` | ITI 中のタッチ。 |
| touch_rect 系 | `RELEASE_DWELL_WILL_REQUIRE`, `RELEASE_DWELL_START`, `RELEASE_DWELL_OK`, `RELEASE_DWELL_RESET`, `RELEASE_DWELL_START_FORCED` | ITI 後の離し待ちと連続解放判定。 |
| `spsm.py` | `TRIAL_PLACED`, `TOUCH_R_CORRECT`, `TOUCH_R_CORRECT_TTL_FAIL`, `TOUCH_NR_ERROR` | 試行配置、報酬刺激タッチ、不報酬刺激タッチ。 |
| `spsp.py` | `TRIAL_PLACED`, `TOUCH_R_CORRECT`, `TOUCH_R_CORRECT_TTL_FAIL` | 両側報酬刺激の試行配置と正反応。 |
| `spsm.py`, `spsp.py`, `rl.py` | `TOUCH_OUTSIDE`, `FAIL_OUTSIDE_LIMIT`, `TOUCH_ITI_LEFT`, `TOUCH_ITI_RIGHT`, `TOUCH_ITI_OUTSIDE` | プレート外、外側上限、ITI 中タッチ。 |
| `spsm.py`, `spsp.py`, `rl.py` | `RELEASE_DWELL_WILL_REQUIRE`, `RELEASE_DWELL_START`, `RELEASE_DWELL_OK`, `RELEASE_DWELL_RESET`, `RELEASE_DWELL_START_FORCED` | ITI 後の離し待ちと連続解放判定。 |
| `rl.py` | `TOUCH_TARGET_CORRECT`, `TOUCH_TARGET_CORRECT_TTL_FAIL`, `TOUCH_NONTARGET_ERROR` | 現在の正解または不正解刺激へのタッチ。 |
| `rl.py` | `REVERSAL_TRIGGERED`, `ADVANCE_TO_NEXT_SET`, `ADVANCE_TO_NEXT_SET_AFTER_REVERSALS` | 反転発生、または次刺激セットへの移行。 |
| `prl.py` live | `TRIAL_PLACED` | 試行配置。 |
| `prl.py` live | `TOUCH_R_REWARDED`, `TOUCH_R_UNREWARDED`, `TOUCH_NR_REWARDED`, `TOUCH_NR_UNREWARDED` | `r` / `nr` 選択と報酬抽選結果。 |
| `prl.py` live | `TOUCH_R_REWARDED_TTL_FAIL`, `TOUCH_NR_REWARDED_TTL_FAIL` | 報酬当選時の TTL 失敗。 |
| `prl.py` live | `TOUCH_OUTSIDE`, `FAIL_OUTSIDE_LIMIT`, `TOUCH_ITI_LEFT`, `TOUCH_ITI_RIGHT`, `TOUCH_ITI_OUTSIDE` | プレート外、外側上限、ITI 中タッチ。 |
| `restless_bandit.py` live | `TRIAL_PLACED` | 試行配置。 |
| `restless_bandit.py` live | `TOUCH_LEFT_REWARDED`, `TOUCH_LEFT_UNREWARDED`, `TOUCH_RIGHT_REWARDED`, `TOUCH_RIGHT_UNREWARDED` | 左右選択と報酬抽選結果。 |
| `restless_bandit.py` live | `TOUCH_LEFT_REWARDED_TTL_FAIL`, `TOUCH_RIGHT_REWARDED_TTL_FAIL` | 報酬当選時の TTL 失敗。 |
| `prl.py`, `restless_bandit.py` live | `RELEASE_DWELL_WILL_REQUIRE`, `RELEASE_DWELL_START`, `RELEASE_DWELL_OK`, `RELEASE_DWELL_RESET`, `RELEASE_DWELL_START_FORCED` | ITI 後の離し待ちと連続解放判定。 |
| `prl.py`, `restless_bandit.py` sim | `SIM_CHOICE` | シミュレーション上の選択行。 |
| `object_explore.py` FOV / ABA_A | `SESSION_START`, `SESSION_TIMEOUT`, `FOV_INACTIVITY_END`, `TOUCH_FOV`, `TOUCH_FOV_OUTSIDE`, `SESSION_END` | 自由探索セッション開始、終了、タッチ。 |
| `object_explore.py` ERC / PEC / ABA_B | `SESSION_START`, `SESSION_END`, `SESSION_TIMEOUT`, `SESSION_PAUSE`, `SESSION_RESUME` | セッション制御。 |
| `object_explore.py` ERC / PEC / ABA_B | `TRIAL_START`, `TRIAL_END`, `CHOOSE_ENABLED`, `OMISSION`, `ITI_START`, `WAIT_RELEASE_START` | 試行制御と状態遷移。 |
| `object_explore.py` ERC / PEC / ABA_B | `TOUCH_CHOOSE`, `TOUCH_CHOOSE_OUTSIDE`, `TOUCH_INTERACT`, `TOUCH_INTERACT_OUTSIDE`, `TOUCH_ITI` | 選択、相互作用、ITI 中のタッチ。 |
| `object_explore.py` ERC / PEC / ABA_B | `REWARD_TTL`, `REWARD_COOLDOWN` | 報酬 TTL、または報酬クールダウン中。 |
| `object_explore.py` ERC / PEC / ABA_B | `INTERACT_TIMEOUT`, `INTERACT_DISENGAGE` | 相互作用時間上限、または無操作終了。 |
| `object_explore.py` ERC / PEC / ABA_B | `BIAS_CORRECTION_START`, `BIAS_CORRECTION_END` | 側方バイアス補正の開始と終了。 |
| `object_explore.py` ERC / PEC / ABA_B | `RELEASE_DWELL_WILL_REQUIRE`, `RELEASE_DWELL_START`, `RELEASE_DWELL_OK`, `RELEASE_DWELL_RESET`, `RELEASE_DWELL_START_FORCED` | ITI 後の離し待ちと連続解放判定。 |
