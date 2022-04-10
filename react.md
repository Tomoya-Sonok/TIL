# Context API

Hooks の１つである`useContext()`を使用して、Context オブジェクトを扱える。

`Context オブジェクト`とは、コンポーネントツリー上直接の親子関係にない（＝ツリー上離れたところにいる）コンポーネント間で同じ値を共有するための道具。 「範囲が限定されたグローバル変数」みたいなものを利用するために使用するもの。

Context オブジェクトの中に`Context Provider`があり、これは対象の値の利用可能な範囲を指定するために使うもの。 具体的には、コンポーネントツリー上で Context Provider の内側にあるすべてのコンポーネントからその Provider に対応したコンテキストの値を利用できる（以下の例を参照）

## App.js

```react
import "./App.css"
import Board from "./components/Board"
import Keyboard from "./components/Keyboard"
import { boardDefault } from "./Words"
import { createContext, useState } from "react"

// ※ Context API を使用
// 以下のAppContext.Providerに入れたコンポーネント内で
// valueとして渡した変数や関数が使えるようになる
export const AppContext = createContext()

function App() {
  const [board, setBoard] = useState(boardDefault)
  const [currAttempt, setCurrAttempt] = useState({ attempt: 0, letterPos: 0 })
  return (
    <div className="App">
      <nav>
        <h1>Worlde</h1>
      </nav>
      <AppContext.Provider
        value={{ board, setBoard, currAttempt, setCurrAttempt }}
      >
        <div className="game">
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

```react
import React from "react"
import Key from "./Key"

function Keyboard() {
  const keys1 = ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"]
  const keys2 = ["A", "S", "D", "F", "G", "H", "J", "K", "L"]
  const keys3 = ["Z", "X", "C", "V", "B", "N", "M"]

  return (
    <div className="keyboard">
      <div className="line1">
        {keys1.map((key) => {
          return <Key keyVal={key} />
        })}
      </div>
      <div className="line2">
        {keys2.map((key) => {
          return <Key keyVal={key} />
        })}
      </div>
      <div className="line3">
        <Key keyVal={"ENTER"} bigKey />
        {keys3.map((key) => {
          return <Key keyVal={key} />
        })}
        <Key keyVal={"DELETE"} bigKey />
      </div>
    </div>
  )
}

export default Keyboard
```

## Key.js

```react
import React, { useContext } from "react"
import { AppContext } from "../App"

function Key({ keyVal, bigKey }) {
  const { board, setBoard, currAttempt, setCurrAttepmt } =
    useContext(AppContext)

  const selectLetter = () => {
    const newBoard = [...board]
    newBoard[currAttempt.attempt][currAttempt.letterPos] = keyVal
    setBoard(newBoard)
    setCurrAttepmt({ ...currAttempt, letterPos: currAttempt.letterPos + 1 })
  }
  return (
    <div className="key" id={bigKey && "big"} onClick={selectLetter}>
      {keyVal}
    </div>
  )
}

export default Key
```

App.js 内の Context Provider で今回使えるようにしたいコンテクスト（グローバル変数のようなもの）が利用可能な範囲を指定しており、その中に Keyboard.js がある。その Keyboard.js の子コンポーネントの Key.js にて、`Context Provider`を通して渡した currAttempt, setCurrAttempt を`useContext()`で使用している。

# 参考

[Build a Wordle Clone in ReactJS - Best React Project](https://www.youtube.com/watch?v=WDTNwmXUz2c&t=1893s)  
[Context – React](https://reactjs.org/docs/context.html)  
[useContext | Hooks API Reference – React](https://reactjs.org/docs/hooks-reference.html#usecontext)  
[React Context API と useContext() の使い方](https://gotohayato.com/content/523/)
