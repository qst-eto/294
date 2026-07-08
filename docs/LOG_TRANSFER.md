# ログ回収

`train/code/pi2win.py` は SCP でログを転送するための補助スクリプトです。CLI 引数はありません。スクリプト内の変数を直接編集して使います。

## 使われている変数

| 変数名 | 用途 |
|---|---|
| `host` | SSH 接続先ホスト。 |
| `port` | SSH ポート。 |
| `username` | SSH ユーザー名。 |
| `password` | SSH パスワード。ソースに平文で入るため注意。 |
| `ras_path` | `scp.put()` の転送元パス。 |
| `win_path` | `scp.put()` の転送先パス。 |

実装は `ssh.connect(host, port=port, username=username, password=password)` で接続し、`scp.put(ras_path, win_path, recursive=True)` を実行します。つまり、現行コードは「このスクリプトを実行しているマシン上の `ras_path` を、SSH 接続先の `win_path` へアップロード」します。

Pi の `logs/` を Windows へ送る場合は、通常は Pi 上でこのスクリプトを実行し、`host` を Windows 側の SSH サーバーにします。Windows 上で実行して Pi に接続する構成では、現行の `scp.put()` はダウンロードではないため転送方向が逆になります。

## 手順

1. `train/code/pi2win.py` を開く。
2. `host`、`port`、`username`、`password`、`ras_path`、`win_path` を自分の環境に合わせて書き換える。
3. 転送方向を確認する。現行コードは `ras_path` から `win_path` への `put`。
4. 実行する。

```bash
cd train/code
python pi2win.py
```

`ras_path` には回収したい `logs/` ディレクトリ、`win_path` には保存先ディレクトリを指定します。パスの書き方は、スクリプトを実行するOSとSSH接続先OSに合わせてください。

## 依存関係

`pi2win.py` は `paramiko` と `scp` を使います。ソース上は import に失敗した場合、実行時に `python -m pip install paramiko` と `python -m pip install scp` を試みます。配布環境では、実機でのインストール状態を確認し、必要なら運用方針に合わせて固定してください。

## セキュリティ注意

`password` はソースに平文で直書きされています。実機の実際の資格情報に書き換える場合、その値を共有リポジトリに commit しないでください。

可能なら SSH 鍵認証へ移行し、パスワードをソースに残さない運用にしてください。少なくとも、配布用コードには実際の `host`、`username`、`password`、実験施設固有のパスを入れたままにしないでください。
