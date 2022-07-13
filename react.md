# useId()

`useId()`は、クライアント側とサーバー側の両方で安定した一意の ID を生成する hooks である。

例えば、以下のように label タグと input タグを`htmlFor`と`id`で紐づけることで、ラベルをクリックしても input タグがフォーカスされるようにしたいとき、例１のようなコードでは問題ないが、例２のような場合は id 重複が起きると最初の該当 id の要素（1 つ目の PersonForm コンポーネント）しかフォーカスされないという事象が発生してしまう。

## 例１

```jsx
function PersonForm() {
  return (
    <div>
      <label htmlFor={'firstName'}>First Name</label>
      <div>
        <input id={'firstName'} type='text' />
      </div>
      <label htmlFor={'lastName'}>Last Name</label>
      <div>
        <input id={'lastName'} type='text' />
      </div>
    </div>
  )
}
```

## 例２

```jsx
import { PersonForm } from './PersonForm'

function App() {
  return (
    <>
      <h1>Host</h1>
      <PersonForm>
      <h1>Guest</h1>
      <PersonForm>
    </>
  )
}

export default App
```

```jsx
function PersonForm() {
  return (
    <div>
      <label htmlFor={'firstName'}>First Name</label>
      <div>
        <input id={'firstName'} type='text' />
      </div>
      <label htmlFor={'lastName'}>Last Name</label>
      <div>
        <input id={'lastName'} type='text' />
      </div>
    </div>
  )
}
```

これは id 重複が起きてしまうことによって生じる問題であるが、以下のように`useId()`を使うことで解決できる。

## useId()を使用した場合

```jsx
import { PersonForm } from './PersonForm'

function App() {
  return (
    <>
      <h1>Host</h1>
      <PersonForm>
      <h1>Guest</h1>
      <PersonForm>
    </>
  )
}

export default App
```

```jsx
import { useId } from 'react'

function PersonForm() {
  // useId()で、:r0:や:ra:のように:で囲まれた一意の文字列が生成される
  const id = useId()
  return (
    <div>
      <label htmlFor={id + 'firstName'}>First Name</label>
      <div>
        <input id={id + 'firstName'} type='text' />
      </div>
      <label htmlFor={id + 'lastName'}>Last Name</label>
      <div>
        <input id={id + 'lastName'} type='text' />
      </div>
    </div>
  )
}
```

上記の例では、`:r1:-firstName`や`:r1:-lastName`でそれぞれ label 要素と input 要素が紐づけられることになる。同じ PersonForm コンポーネントが複数回レンダリングされるとしても、useId()で生成されるのは`:r2:`や`:r3:`のように一意の文字列なので、例２のような問題は防ぐことができる。

# useRef()

`useRef()`という hooks を使うと、紐づけた要素への参照が簡単にできるようになる。  
Ref オブジェクト（ { current: _initialValue_( <- `useRef()`で設定する初期値)} ）を生成し、 `Refオブジェクト.current`に設定した値を変更したり自由に取り出して使うことができる。 絶対に押さえておきたいポイントは、**この Ref オブジェクトの current プロパティの値が変更されても、再レンダリングが行われないということ。**  
（簡単なイメージでいうと、コンポーネントの再レンダリングを行わずに内部の値を保持して使いやすくできる箱を作る感じ）

## 使い方

```jsx
import { useRef } from 'react'

function App() {
  // useRef()でRefオブジェクトを生成
  const inputElmRef = useRef()

  // Refオブジェクトを使うには、HTML要素にrefという属性でuseRef()で生成した値を渡す
  return (
    <>
      <input type='text' ref={inputElmRef} />
    </>
  )
  // たったこれだけで、
  // inputElmRef.current.focus()で要素にフォーカスさせたり、
  // inputElmRef.current.offsetHeightで要素の高さを取得できる
}
```

また、再レンダリングが発生しない点を利用し、  
そのページ内で一度だけ実行したい処理があるときは以下のような使い方もできる。

```jsx
const once = useRef(true)

useEffect(() => {
  if (!once.current) return
  // 実行したい処理
  once.current = false
}, [])
```

↓↓↓ 以下、簡単な Todo アプリでの使用例 ↓↓↓

```jsx
import { useState, useRef } from 'React'
import TodoList from './TodoList'
// Todoのキー重複を避けるためのuuid
import { v4 as uuidv4 } from 'uuid'

function App() {
  const [todos, setTodos] = useState([
    // １つ目のTodoを仮設定しているだけ
    { id: 1, name: 'Todo1', completed: false },
  ])

  // ここでRefオブジェクトを生成
  const todoNameRef = useRef()

  const handleAddTodo = () => {
    // 下のinput要素に入力された値を参照して使う
    const name = todoNameRef.current.value
    setTodos((prevTodos) => {
      // スプレッド構文でその時点のtodosを配列内で展開して、
      // その中に新しいTodoのオブジェクトを追加する
      return [...prevTodos, { id: uuidv4(), name: name, completed: false }]
    })
    // Todoを追加したら、Refオブジェクトをいったん空に戻す
    todoNameRef.current.value = null
  }

  // 下記のinput要素のref属性でRefオブジェクトを紐づけているので、
  // 「Add Task」ボタンが押下されて上のhandleAddTodo()が実行されるときに
  // input要素に入力された値が todoNameRef.current.valueとして使うことができる
  return (
    <>
      <TodoList todos={todos} />
      <input type='text' ref={todoNameRef} />
      <button onClick={handleAddTodo}>Add Task</button>
    </>
  )
}
```

# useContext()

Hooks の１つである`useContext()`を使用して、Context オブジェクトを扱える。

`Context オブジェクト`とは、コンポーネントツリー上直接の親子関係にない（＝ツリー上離れたところにいる）コンポーネント間で同じ値を共有するための道具。 「範囲が限定されたグローバル変数」みたいなものを利用するために使用するもの。

Context オブジェクトの中に`Context Provider`があり、これは対象の値の利用可能な範囲を指定するために使うもの。 具体的には、コンポーネントツリー上で Context Provider の内側にあるすべてのコンポーネントからその Provider に対応したコンテキストの値を利用できる（以下の例を参照）

## App.js

```jsx
import './App.css'
import Board from './components/Board'
import Keyboard from './components/Keyboard'
import { boardDefault } from './Words'
import { createContext, useState } from 'react'

// ※ Context API を使用
// 以下のAppContext.Providerに入れたコンポーネント内で
// valueとして渡した変数や関数が使えるようになる
export const AppContext = createContext()

function App() {
  const [board, setBoard] = useState(boardDefault)
  const [currAttempt, setCurrAttempt] = useState({ attempt: 0, letterPos: 0 })
  return (
    <div className='App'>
      <nav>
        <h1>Worlde</h1>
      </nav>
      <AppContext.Provider
        value={{ board, setBoard, currAttempt, setCurrAttempt }}
      >
        <div className='game'>
          <Board />
          <Keyboard />
        </div>
      </AppContext.Provider>
    </div>
  )
}

export default App
```

## Keyboard.js

```jsx
import React from 'react'
import Key from './Key'

function Keyboard() {
  const keys1 = ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P']
  const keys2 = ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L']
  const keys3 = ['Z', 'X', 'C', 'V', 'B', 'N', 'M']

  return (
    <div className='keyboard'>
      <div className='line1'>
        {keys1.map((key) => {
          return <Key keyVal={key} />
        })}
      </div>
      <div className='line2'>
        {keys2.map((key) => {
          return <Key keyVal={key} />
        })}
      </div>
      <div className='line3'>
        <Key keyVal={'ENTER'} bigKey />
        {keys3.map((key) => {
          return <Key keyVal={key} />
        })}
        <Key keyVal={'DELETE'} bigKey />
      </div>
    </div>
  )
}

export default Keyboard
```

## Key.js

```jsx
import React, { useContext } from 'react'
import { AppContext } from '../App'

function Key({ keyVal, bigKey }) {
  // ここが重要！
  const { board, setBoard, currAttempt, setCurrAttepmt } =
    useContext(AppContext)

  const selectLetter = () => {
    const newBoard = [...board]
    newBoard[currAttempt.attempt][currAttempt.letterPos] = keyVal
    setBoard(newBoard)
    setCurrAttepmt({ ...currAttempt, letterPos: currAttempt.letterPos + 1 })
  }
  return (
    <div className='key' id={bigKey && 'big'} onClick={selectLetter}>
      {keyVal}
    </div>
  )
}

export default Key
```

App.js 内の Context Provider で今回使えるようにしたいコンテクスト（グローバル変数のようなもの）が利用可能な範囲を指定しており、その中に Keyboard.js がある。その Keyboard.js の子コンポーネントの Key.js にて、`useContext()`を用いたことで`Context Provider`を通して渡した currAttempt, setCurrAttempt を使用できるようになっている。

# useMemo()

`useMemo`は関数の結果を保持するための hooks で、何回やっても結果が同じ場合の値などを保存(メモ化)し、そこから値を再取得する。不要な再計算をスキップすることから、パフォーマンスの向上が期待できる。useCallback は関数自体をメモ化するが、useMemo は関数の結果を保持する。

## メモ化

メモ化とは、再レンダリングが走る時、不要な関数や値を再生成しないための処理。ブラウザのメモリに一時的に値を保存することで、不必要な処理が実行されるのを防ぎ、パフォーマンス最適化に繋がる。

## 使い方

```jsx
// 基本形（depsは、useEffectでもおなじみの依存配列のこと）
useMemo(() => someExpression, [deps])

const [count, setCount] = useState(initialCount)

// useMemoを使わない
const result = () => count * 2)

// useMemoを使う
const result = useMemo(() => count * 2, [count])
```

依存配列に入っている`count`に変化があれば再計算するが、もし変化がなければ前回計算した値が result へ格納される。

# useCallback()

`useCallback`はメモ化されたコールバック関数を返す。

## 使い方

```jsx
// 基本形（depsは、useEffectでもおなじみの依存配列のこと）
useCallback(callbackFunction, [deps])

// useCallbackを使わない
const sampleFunc = () => {
  doSomething(a, b)
}

// useCallbackを使う
const sampleFunc = useCallback(() => {
  doSomething(a, b)
}, [a, b])
```

依存配列の要素 a,b のいずれかが変化した場合のみ、以前作ってメモ化した sampleFunc の値を再計算する。依存配列の要素に変化がなければ、前回の sampleFunc を再利用する。

React.memo と併用することで、React.memo でメモ化したコンポーネントに useCallback でメモ化したコールバック関数を Props として渡し、コンポーネントの不要な再描画をスキップすることができパフォーマンスが上げることが可能。（むしろ React.memo と合わせて使わないと、メモ化していないコンポーネントに useCallback でメモ化した関数を props で渡しても結局再レンダリングされてしまう）

---

## どういった時にメモ化すべきか

前提として、React は非常に最適化されており、そのままでも十分高速である。しかしバックエンド側のリファクタリングやコンポーネント設計でも改善されないほど動作が重たい場合などには、`useMemo`や`useCallback`等のメモ化の hooks を活用して関数の再実行やコンポーネントの再レンダリングを防ぐことを検討してみるべき。

ちなみに、`useCallback(fn, deps)` と `useMemo(() => fn, deps)`は等価であり、おそらく関数をメモ化するときに useCallback の書き方のほうが使いやすいので実装された hooks だろうか。
