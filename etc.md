# nodebrew

Node.js のインストールやバージョン切り替えができる。

## インストール方法

```
brew install nodebrew
echo 'export PATH=$HOME/.nodebrew/current/bin:$PATH' >> ~/.zshrc
```

## 使い方

下記コマンドで、インストールできるバージョンを確認して、安定版を指定して実行。

```
nodebrew ls-remote
nodebrew install <バージョン>
```

その後、下記コマンドで使用するバージョンがインストールできていることを確認し、use で決定。念のため、`node -v`で確認

```
nodebrew ls
nodebrew use <バージョン>
```

## nodebrew 自体をアップデートする場合

```
nodebrew selfupdate
```

# depcheck

使用されていないパッケージを教えてくれるツール。

## 使い方、使用例

`npx depcheck` とコマンド実行するだけ。

```
# 未使用パッケージ削除前
$ npx depcheck
npx: 108個のパッケージを6.778秒でインストールしました。
Unused dependencies
* bootstrap
* jquery
* path
Unused devDependencies
* babel-eslint
* casperjs

# 未使用パッケージ削除後
$ npx depcheck
npx: 108個のパッケージを4.581秒でインストールしました。
No depcheck issue
```
