# Promise

非同期処理の状態を管理するために使われるオブジェクト。  
非同期処理の操作が`完了` or `失敗`したときに結果を返してくれる。

## 基本的な構文と書き方

例 ①

```js
new Promise(function (resolve, reject) {
  非同期的な関数(
    (成果) => resolve(成果), // 成功時コールバック関数
    (問題) => reject(問題) // 失敗時コールバック関数
  )
})
```

例 ②

```js
function delay(t) {
  return new Promise(function (resolve) {
    return setTimeout(resolve, t)
  })
}

function sayHello() {
  console.log('Hello')
}

delay(3000).then(sayHello)
```

基本的には、例 ② のようになにか時間がかかってしまう処理（関数）に対して Promise を使用し、`.then`で繋げることで対象の重たい処理が確実に終わってからその次の処理を実行することが可能。

# async/await

Promise を使った結果は、以下のように`.then`を使わなければ処理できない。

```js
/**
 * @returns {Promise.<number>}
 */
function 非同期処理() {
  return Promise.resolve(1)
}

/**
 * @returns {number}
 */
function 同期処理() {
  return 1
}

function main() {
  console.log(1 + 非同期処理()) // 1[object Promise]
  console.log(1 + 同期処理()) // 2
  // Promiseの結果の処理はthenが必要
  非同期処理().then((value) => console.log(1 + value)) // 2
}

main()
```

一方で、`async/await`を使うと次のように書ける。

```js
/**
 * @returns {Promise.<number>}
 */
function 非同期処理() {
  return Promise.resolve(1)
}

async function main() {
  console.log(1 + 非同期処理()) // 1[object Promise]
  console.log(1 + (await 非同期処理())) // 2
}

main()
```

async の関数内で例外が throw された場合も、戻り値は Promise 型のオブジェクト。`catch()`で処理できる。

```js
/**
 * @returns {Promise.<void>}
 */
async function throwError() {
  throw 'err'
}

throwError().catch(console.log) // errと出力される
```

以下、例外処理の書き方メモ

```js
/**
 * @returns {Promise.<void>}
 */
async function getStatusCode() {
  try {
    console.log('started')
    const response = await axios.get('https://httpbin.org/status/200')
    console.log('success', response.status)
  } catch (error) {
    console.log('error', error.response.status)
  } finally {
    console.log('finished') // 共通処理
  }
}
```

# 再帰関数

実行された関数の処理の中で再びその関数自身を再起的に呼び出す関数のこと。  
同じ処理を繰り返すときなどに少ない記述で実装することができる。

## 例１）フィボナッチ数列

--> [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, ...]のように前の 2 項を足してできあがる数列

```js
function fib(n) {
  if (n < 3) return 1
  return fib(n - 1) + fib(n - 2)
}
```

上記は、フィボナッチ数列の左から n 番目の値を求めることができるシンプルな再起関数の例。
1 番目か 2 番目であれば`1`を返し、3 番目以降は前の 2 項を足した数を返却する。

## 例２）フィボナッチ数列（応用編）

--> 1 から 1000 までのフィボナッチ数列を出力する関数を作る

```js
// fib関数を実行し、結果を出力
const outputFib = () => {
  const array = [1, 1]
  const result = fib(array[0], array[1], array)
  console.log(result)
}

// fib関数 <-- 再帰関数
const fib = (prev, next, result) => {
  // 前の2項を足す
  const sum = prev + next

  // 前の2項の和が1000を超えたら、すぐ結果を返却
  if (sum > 1000) return result

  // フィボナッチ数列のprevとnextを更新する。
  prev = next
  next = sum

  // 返却する結果に足した数を追加
  result.push(sum)

  // return 時に、fib関数を再帰的に呼び出す
  return fib(prev, next, result)
}

outputFib()
```

例１でも例２でも、**if 文などを使って再帰関数が終了する条件（終了条件）を必ず書く必要があり、書かないと無限ループになってしまう**ので要注意。  
また、処理速度やメモリを考慮すると再帰関数よりも for 文等のループ処理が優れている印象。もし再帰関数をどうしても使いたいケースがあれば、`メモ化`や`末尾再帰`を調べて取り入れることでパフォーマンスを最適化しよう。

# Hoisting (巻き上げ)

Hoisting(巻き上げ)とは、コードの実行時に関数や変数の`定義のみ`がスコープの先頭に巻き上げられること。これは、JavaScript がコード評価時に関数や変数を初期化前の状態としてメモリに一時保存される仕様によるもの。（以下の画像を参照）

![hoisting](https://user-images.githubusercontent.com/50537591/173578193-7b1ade6c-56df-4ccd-aa49-33a08d2305aa.png)

例１）関数の定義部分が巻き上げられ、宣言する前にその関数を呼び出す処理を書いても関数が正しく動く

```js
dogName('Komugi')

function dogName(name) {
  console.log("My dog's name is " + name)
}
/*
実行結果: "My dog's name is Komugi"
*/
```

ただし、上記のように関数宣言であれば先頭に巻き上げられて実行できるが、下記のように関数式を変数に代入した場合は`undefined` となってしまう。

```js
dogName('Komugi')

var dogName = (name) => {
  console.log("My dog's name is " + name)
}
/*
実行結果: TypeError: dogName is not a function
（var dogName という定義だけ先頭で実行され、varの仕様によりdogName = undefined となっているため。undefinedは関数ではないので、上記のようなエラーとなる。）
*/
```

例２）変数の定義部分のみが巻き上げられ、初期化は行われていない

```js
console.log(num)

var num // 宣言 ⇦ 巻き上げられる！
num = 6 // 初期化 ⇦ 巻き上げられない！

/*
実行結果: undefined 
（宣言のみが巻き上げられ、console.log()でログ出力する時点ではまだ初期化が行われていないため）
*/
```

以下のコードでは初期化のみが行われていて巻き上げは行われていないので、変数を読み取ろうとすると ReferenceError 例外が発生する。

```js
console.log(num)
num = 6 // 初期化

/*
実行結果: ReferenceError 例外が発生
*/
```

## var と let/const で宣言した場合の挙動の違い

上記で例で使用した`var`で宣言された変数には他の何かが割り当てられるまで undefined が初期値として与えられるが、`let`と`const`は undefined で初期化されないので参照しようとすると ReferenceError（参照エラー）となる。

```js
console.log(x) // undefined
console.log(y) // ReferenceError: can't access lexical declaration `y' before initialization
console.log(z) // ReferenceError: can't access lexical declaration `z' before initialization
var x = 100
let y = 200
const z = 300
```
