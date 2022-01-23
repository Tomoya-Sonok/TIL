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

--> .. depcheck や ncu のことを追記予定。
