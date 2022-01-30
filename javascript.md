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
  console.log("Hello")
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
  throw "err"
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
    console.log("started")
    const response = await axios.get("https://httpbin.org/status/200")
    console.log("success", response.status)
  } catch (error) {
    console.log("error", error.response.status)
  } finally {
    console.log("finished") // 共通処理
  }
}
```
