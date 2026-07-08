# <日本語名> — <script>.py

（`rl.py` / `prl.py` など紛らわしい名前がある場合は、ここで別スクリプトとの違いを1文で書く。）

## 1. これは何か（目的）

この実験が測る行動を、非プログラマにも分かる日本語で1〜3文で説明する。

## 2. いつ使うか（位置づけ）

訓練・実験の流れのどこで使うかを書く。全体の流れは [PROTOCOL.md](../PROTOCOL.md) を参照。科学的背景がある場合は `../../Banno/` 配下の資料へリンクする。

## 3. 前提条件

- 実行は `train/code/` ディレクトリ内で行う。共有モジュールが同じ場所にある前提。
- 実機が必要か、`--sim` が使えるかを書く。
- required 引数を書く。
- 刺激画像が必要な場合だけ、`stim_NN_r.png` / `stim_NN_nr.png` の命名規則を書く。
- `--seed`、`--subject-id` など必須識別子がある場合は書く。

## 4. 起動コマンド例

```bash
cd train/code
python <script>.py <required-args>
```

共通引数は [共通引数リファレンス](../_common_args.md) を参照。

## 5. この実験に固有の主要引数

共通引数は [共通引数リファレンス](../_common_args.md) に集約する。ここには、この実験に固有または特に重要な実在引数だけを載せる。

| 引数 | 既定値 / choices | 説明 |
|---|---|---|
| `<arg>` | `<default>` | `<description>` |

## 6. 出力CSV

- 保存先: `--out-dir`。既定値は `logs/`。
- ファイル名 prefix: `<prefix>`
- 共通列は [CSV_FORMAT.md](../CSV_FORMAT.md) を参照。
- この実験固有の重要列だけを書く。

| 列 | 説明 |
|---|---|
| `<column>` | `<description>` |

## 7. 典型パラメータ

値を創作せず、help / ソースで確認できる既定値と choices の範囲で書く。

導入用（易しい設定）:

```bash
python <script>.py ...
```

本番用:

```bash
python <script>.py ...
```

## 8. 終了方法

ソースで確認できる終了方法だけを書く。例: `STOP` ファイル、ウィンドウを閉じる、`ESC`、`q`。

## 9. よくある失敗

この実験に特有の失敗を書く。横断的な問題は [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) を参照。
