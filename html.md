# HTML はどうやって生まれたの？

HTML は 1989 年、スイスのジュネーブにある CERN（欧州原子核研究機構）のイギリス人研究者であるティム・バーナーズ・リー博士によって開発された。研究所内では多くの当時の研究所内では膨大な論文の中から関連する資料を探すのに時間・労力がかかっていて、より簡単に研究者同士が進捗状況や研究成果を共有できるようにする必要があった。そういった経緯から、博士はハイパーテキストシステムを考案し、ある論文から別の参考文献へリンクを貼ってすぐに参照できるようにした。

これがインターネット上でドキュメントやメディアを互いに参照しあうハイパーテキストシステムである`World Wide Web`が生まれた経緯であり、このシステム上のドキュメントを記述するための言語として `HTML（Hyper Text Markup Language）`が開発された。

# VS Code で HTML を書くときの Tips

## Emmet の活用

VS Code では、Emmet という HTML や CSS を省略記法で書けるプラグインが標準搭載されている。例えば、`margin-bottom: 80px;`といちいち書かなくても、`mb80`と入力するだけでよい。

例 1）クラスや ID の付与

```html
<!-- クラス -->
div.hoge + TabキーまたはReturnキー
<!-- ID -->
div#fuga + TabキーまたはReturnキー
```

↓

```html
<!-- クラス -->
<div class="hoge"></div>
<!-- ID -->
<div id="fuga"></div>
```

例 2）要素のタグと閉じタグを自動入力

```
li + TabキーまたはReturnキー
```

↓

```html
<li></li>
```

例 3）階層構造も自動補完

```
ul>li + TabキーまたはReturnキー
```

↓

```html
<ul>
  <li></li>
</ul>
```

例 4）ダミーテキストの挿入

```
lorem + TabキーまたはReturnキー
```

↓

```html
Lorem ipsum dolor sit amet, consectetur adipisicing elit. Temporibus modi nobis
debitis! Et cupiditate ex totam voluptatem sed, iure voluptatum numquam, vero
ipsa porro enim dolor consequuntur, molestias a ullam?
```

他にも数えきれないほどの Emmet 記法が存在する。[チートシート](https://docs.emmet.io/cheat-sheet/)を参照・活用すべし。

## コマンドパレットの活用

なにかをタグで囲みたいとき、いちいちタグを書いてその中に対象を入れる操作は不要。下記の手順で操作したほうが楽だし速く書ける。

```
手順
1. 対象をダブルクリックで選択し、コマンドパレットで`Wrap with abbreviation`を実行。
2. 囲みたいタグを入力して Enter を押す。
```
