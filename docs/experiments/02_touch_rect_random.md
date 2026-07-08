# ランダム配置矩形タッチ訓練 — touch_rect_random.py

## 1. これは何か（目的）

固定サイズの矩形を画面内のランダム位置に出し、タッチできたら TTL とビープを出す訓練です。中央だけでなく、画面内の別位置へ手を伸ばす練習に使います。

## 2. いつ使うか（位置づけ）

[中央矩形タッチ訓練](01_touch_rect_center.md) の次に使います。ランダム位置を安定して触れるようになってから、2刺激課題へ進みます。全体の流れは [PROTOCOL.md](../PROTOCOL.md) を参照。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。共有モジュールが同じ場所にある前提。
- `--serial-port` は必須。
- `--sim` と `--dry-run-ttl` はありません。タッチパネルと Arduino TTL が使える実機環境で起動します。
- 刺激画像は使いません。

## 4. 起動コマンド例

本番最小形:

```bash
cd train/code
python touch_rect_random.py --serial-port /dev/ttyACM0
```

当たり判定を見ながら確認する例:

```bash
cd train/code
python touch_rect_random.py --serial-port /dev/ttyACM0 --show-box --info
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `--rect-mode` | `square_custom`; choices: `auto`, `square`, `square_custom` | 固定サイズの決定方法。 |
| `--square-px` | `240` | `square_custom` の一辺 px。 |
| `--initial-size-frac` | `0.3` | `auto` / `square` 用。画面短辺に対する割合。 |
| `--min-side-px` | `None` | 矩形一辺の最小 px。 |
| `--auto-fullscreen-rect` | off | `fixed_side >= short` かつ fullscreen のとき矩形を全面化する。 |
| `--rect-rgb` | `0 160 255` | 矩形色。 |

## 6. 出力CSV

- 保存先: `--out-dir`。既定値は `logs/`。
- ファイル名 prefix: `touch_rect_log_`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。

重要列:

| 列 | 説明 |
|---|---|
| `rect_x`, `rect_y`, `rect_w`, `rect_h` | 表示された矩形の位置とサイズ。 |
| `hit_margin_px` | 当たり判定マージン。 |
| `hit_area` | タッチが当たり判定内かどうかの領域情報。 |
| `outside_in_trial` | その試行中の矩形外タッチ回数。 |
| `trial_outcome` | 試行結果。 |
| `release_dwell_required_ms`, `release_dwell_elapsed_ms` | ITI 中タッチ後の解放待ち情報。 |

## 7. 典型パラメータ

導入用（既定サイズを見える化）:

```bash
cd train/code
python touch_rect_random.py --serial-port /dev/ttyACM0 --rect-mode square_custom --square-px 240 --show-box
```

本番用（タッチ専用・キオスク）:

```bash
cd train/code
python touch_rect_random.py --serial-port /dev/ttyACM0 --fullscreen --touch-only --kiosk
```

## 8. 終了方法

- `train/code/` に `STOP` ファイルを置く。
- ウィンドウを閉じる。
- `ESC` または `q` を押す。

## 9. よくある失敗

- `--serial-port` を指定しないと起動できません。
- `--rect-mode square_custom` で `--square-px` を極端に小さくすると訓練初期には難しくなります。
- `--touch-only` を付けた状態ではマウスクリック確認ができません。開発確認では外してください。
- 横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
