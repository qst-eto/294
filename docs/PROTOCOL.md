# 訓練プロトコル

このページは、Homecage-TR の標準的な進行順序をまとめた入口です。ここに書く順序や基準は「例・目安」です。最終的な昇格基準、試行数、日数、除外基準は研究計画に合わせて事前に決めてください。

## 標準フロー

| 段階 | 目的 | 典型スクリプト | 参照 |
|---|---|---|---|
| 1. 中央タッチ | タッチスクリーンと報酬の対応を学習する。 | `touch_rect_center.py` | [中央矩形タッチ訓練](experiments/01_touch_rect_center.md) |
| 2. ランダム位置タッチ | 中央以外の位置へ手を伸ばす。 | `touch_rect_random.py` | [ランダム配置矩形タッチ訓練](experiments/02_touch_rect_random.md) |
| 3. 2択画面への慣らし | 左右2つの選択肢が出る画面に慣らす。 | `spsp.py` | [2刺激課題（両報酬/画像なし可）](experiments/04_spsp.md) |
| 4. 2刺激弁別 | 報酬刺激 `r` と無報酬刺激 `nr` を弁別する。 | `spsm.py` | [2刺激判別（昇格あり）](experiments/03_spsm.md) |
| 5. 反転学習 | 決定論的な正解ラベルの反転に適応する。 | `rl.py` | [反転学習](experiments/05_rl.md) |
| 6. 確率的反転学習 | 確率的報酬とブロック内反転に適応する。 | `prl.py` | [確率的反転学習](experiments/06_prl.md) |
| 7. restless bandit | 報酬確率が連続的に変化する左右選択を行う。 | `restless_bandit.py` | [restless 2-armed bandit](experiments/07_restless_bandit.md) |

`object_explore.py` は、上の一本道の訓練とは別系統です。2択タッチが成立した後、ERC / PEC / FOV / ABA_A / ABA_B で接触選好や接触行動を測るときに使います。詳細は [オブジェクト接触選好](experiments/08_object_explore.md) を参照してください。

## 各段階の次へ進む目安

### 1. 中央矩形タッチ訓練

`touch_rect_center.py` で中央の矩形を安定して触れるようにします。サイズは `--initial-size-frac`、`--min-size-frac`、`--shrink-every`、`--shrink-factor` で調整できます。次へ進む基準はコード上に固定されていないため、研究計画に合わせて設定してください。

参照: [中央矩形タッチ訓練](experiments/01_touch_rect_center.md)

### 2. ランダム配置矩形タッチ訓練

`touch_rect_random.py` でランダム位置の矩形を触れることを確認します。既定の `--square-px` は `240` です。次へ進む正確な基準はコード上に固定されていないため、成功率、外側タッチ、セッション継続性などを研究計画に合わせて設定してください。

参照: [ランダム配置矩形タッチ訓練](experiments/02_touch_rect_random.md)

### 3. 2択画面への慣らし

`spsp.py` は左右どちらも報酬として扱う課題です。画像なしで始める場合は `--no-images` を使えます。`--sliding-n` の既定値は `20`、`--acc-threshold` の既定値は `0.8` です。help では「超えたら」昇格と説明されています。

参照: [2刺激課題（両報酬/画像なし可）](experiments/04_spsp.md)

### 4. 2刺激弁別

`spsm.py` で報酬刺激 `r` と無報酬刺激 `nr` を弁別します。`--sliding-n` の既定値は `20`、`--acc-threshold` の既定値は `0.8` です。help では「超えたら」昇格と説明されています。コレクショントライアルを使う場合は `--correction-mode`、昇格判定から除く場合は `--exclude-correction-from-acc` を使います。

参照: [2刺激判別（昇格あり）](experiments/03_spsm.md)

### 5. 反転学習

`rl.py` は `spsm.py` の後に使う決定論的な反転学習です。`--sliding-n` の既定値は `20`、`--acc-threshold` の既定値は `0.8` です。help では「以上」で逆転/昇格を判定します。`--reversals-per-set` の既定値は `0` で、これは反転なしです。反転を実施する場合は `1` 以上を指定します。

参照: [反転学習](experiments/05_rl.md)

### 6. 確率的反転学習

`prl.py` は `--seed` が必須です。既定のスケジュールは `--n-blocks 6`、`--block-len-trials 80`、`--reversal-min-trial 30`、`--reversal-max-trial 50`、`--schedule-set 80-20` です。生成したスケジュールを保存する場合は `--save-schedule-json` を使います。

参照: [確率的反転学習](experiments/06_prl.md)

### 7. restless bandit

`restless_bandit.py` は `--seed` が必須です。既定の walk 長は `--n-trials 300` です。生成した walk を保存する場合は `--save-walk-json` を使います。`prl.py` と違い、ブロック内の一回反転ではなく、左右の報酬確率が試行ごとに変化します。

参照: [restless 2-armed bandit](experiments/07_restless_bandit.md)

## seed 管理

`prl.py` と `restless_bandit.py` は `--seed` が必須です。セッションごとに、少なくとも次を実験ノートやメタデータに残してください。

- スクリプト名
- 被験体 ID
- 実行日時
- `--seed`
- `--schedule-json` または `--walk-json` を使った場合はファイル名
- `--save-schedule-json` または `--save-walk-json` で保存したファイル名
- 実行したコマンド全文

同じ seed でも、スクリプトの版や主要パラメータが変わると同一条件とは扱えません。配布後にコードを変更した場合は、変更内容とテスト結果も残してください。

## `rl.py` と `prl.py` を混同しない

`rl.py` は決定論的な reversal learning です。`--serial-port` と `--stim-dir` が必須で、`--sim` はありません。出力 prefix は `touch_2stim_log_` です。

`prl.py` は probabilistic reversal learning です。`--seed` が必須で、`--sim` によるヘッドレス確認があります。live 出力 prefix は `prl_log_`、sim 出力 prefix は `prl_sim_log_` です。

## 関連資料

- 入口: [../README.md](../README.md)
- セットアップ: [./SETUP.md](./SETUP.md)
- ハードウェア: [./HARDWARE.md](./HARDWARE.md)
- 共通 CLI 引数: [./_common_args.md](./_common_args.md)
- CSV ログ形式: [./CSV_FORMAT.md](./CSV_FORMAT.md)
- ログ回収: [./LOG_TRANSFER.md](./LOG_TRANSFER.md)
- トラブル対応: [./TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
