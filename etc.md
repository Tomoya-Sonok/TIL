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

使用されていないパッケージを教えてくれる。

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

# npm-check-updates

npm モジュールを一括で更新できる。

## 使い方、使用例

1. ncu コマンドの実行 （アップデート情報の確認）
2. ncu -u コマンドの実行（package.json の更新）
3. npm install コマンドの実行（更新された package.json をもとにパッケージをインストール）

```
# ncu でアップデート可能なパッケージの確認（ほとんどの記事ではグローバルインストールしていますが使用頻度は多くないと思うので npx での実行でもいいと思います）
$ npx -p npm-check-updates  -c "ncu"
npx: 285個のパッケージを6.971秒でインストールしました。
Checking package.json
[====================] 2/2 100%

 cowsay  ^1.2.0  →  ^1.4.0

Run ncu -u to upgrade package.json


# ncu -u を実行すると package.json が更新される
$ npx -p npm-check-updates  -c "ncu -u"
npx: 285個のパッケージを6.971秒でインストールしました。
Checking package.json
[====================] 2/2 100%

 cowsay  ^1.2.0  →  ^1.4.0

Run npm install to install new versions.

# 更新された package.json をもとに npm install の実行
$ npm i
```

また、特定のパッケージだけを指定できたり、正規表現で複数のパッケージも指定できたり、マイナーバージョン以上、またはパッチバージョン以上のバージョンアップを指定できたり、いろいろとできるらしい。
