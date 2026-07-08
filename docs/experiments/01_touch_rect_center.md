# 中央矩形タッチ訓練 — touch_rect_center.py

## 1. これは何か（目的）

画面中央の矩形をタッチすると TTL とビープを出す、最初期のタッチ訓練です。成功回数に応じて矩形を小さくでき、中央の大きなターゲットから小さいターゲットへ段階的に慣らします。

## 2. いつ使うか（位置づけ）

タッチスクリーン訓練の最初に使います。中央の大きなターゲットを触れるようになってから、[ランダム配置矩形タッチ訓練](02_touch_rect_random.md) へ進みます。全体の流れは [PROTOCOL.md](../PROTOCOL.md) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。共有モジュールが同じ場所にある前提。
- `--serial-port` は必須。
- `--sim` と `--dry-run-ttl` はありません。タッチパネルと Arduino TTL が使える実機環境で起動します。
- 刺激画像は使いません。

## 4. 起動コマンド例

本番最小形:

```bash
cd train/code
python touch_rect_center.py --serial-port /dev/ttyACM0
```

画面の当たり判定を見ながら確認する例:

```bash
cd train/code
python touch_rect_center.py --serial-port /dev/ttyACM0 --show-box --info
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--rect-mode` | `auto`; choices: `auto`, `square`, `square_custom` | 中央矩形のサイズ決定方法。 |
| `--square-px` | `None` | `--rect-mode square_custom` の一辺 px。 |
| `--initial-size-frac` | `1.0` | 初期サイズ。画面短辺に対する割合。 |
| `--min-size-frac` | `0.10` | 自動縮小時の最小割合。 |
| `--shrink-every` | `10` | 何回成功ごとに縮小するか。 |
| `--shrink-factor` | `0.8` | 縮小倍率。 |
| `--min-side-px` | `None` | 矩形一辺の最小 px。 |
| `--auto-fullscreen-rect` | off | 条件成立時に auto モードの矩形を全面化する。 |
| `--rect-rgb` | `0 160 255` | 矩形色。 |

## 6. 出力CSV

- 保存先: `--out-dir`。既定値は `logs/`。
- ファイル名 prefix: `touch_rect_log_`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

重要列:

| 列 | 説明 |
|---|---|
| `rect_w`, `rect_h` | その試行の矩形サイズ。 |
| `outside_in_trial` | その試行中の矩形外タッチ回数。 |
| `max_outside_before_fail` | 外側タッチ失敗までの上限。 |
| `trial_outcome` | 試行結果。 |
| `iti_kind` | ITI の種類。 |
| `release_dwell_required_ms`, `release_dwell_elapsed_ms` | ITI 中タッチ後の解放待ち情報。 |

## 7. 典型パラメータ

導入用（大きい中央ターゲット、既定値ベース）:

```bash
cd train/code
python touch_rect_center.py --serial-port /dev/ttyACM0 --rect-mode auto --initial-size-frac 1.0 --min-size-frac 0.10 --shrink-every 10 --shrink-factor 0.8 --show-box
```

本番用（タッチ専用・キオスク）:

```bash
cd train/code
python touch_rect_center.py --serial-port /dev/ttyACM0 --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `ESC` を押す。
- ウィンドウを閉じる。

`touch_rect_center.py` では、ソース確認上 `STOP` ファイルと `q` キー終了は確認できません。

## 9. よくある失敗

- `--serial-port` を指定しないと起動時に argparse エラーになります。
- Arduino のポート名が違うと TTL 初期化で失敗します。
- `--rect-mode square_custom` で `--square-px` を指定しないとエラーになります。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
