# 恒利威科技面試

## 面試流程

 - 紙本題 15 題 + 面試者基本資料撰寫 - **40 min**
 - 上機考 4 題，使用 Mac (提供的電腦鍵盤有問題) - **35 min**
    - 時間到後前端主管會根據答案做提問，即使沒完成的題目也可以口頭解釋自己的 solution
 - 個人經歷說明 + HR 介紹公司環境、福利，提問環節 - **20 min**
    - 因為已經了解過公司的業務內容，所以這次沒有特別提問什麼


### 紙本題

僅列出較有印象、自己可能寫錯或沒自信的題目

#### 執行結果應為何？
```js
const x = (false || {} || null)
const y = (null || false || "")
const z = ([] || 0 || true)
console.log(x, y, z)
```
> 此題為考何種類型的資料會回傳 `true`，故答案為 `{}, "", []` (當下腦袋沒轉過來想清楚這題想問什麼，故答錯)
- `z` 的參數中，`[]` 跟 `true` 皆會回傳 `true`，但因 `||` 運算符會返回第一個為 `true` 的資料，故回傳空陣列 `[]`

#### 執行結果為何？
 ```js
function exec() {
  console.log('a')
  setTimeout(function (){
    console.log('b')
  }, 0) 
  console.log('c')
}
exec()

//  A. 印出 a, b, c
//  B. 印出 a, c, b
//  C. 印出 b, c, a
//  D. 印出 b, a, c
//  E. 以上皆非
```
> 答案為 B。
- 按照執行順序，'a' 及 'c' 為正序同步程式碼，故依序執行；'b' 為 setTimeout，故依照程式碼延遲 0 毫秒後執行，在當前事件執行佇列中，'a' 'c' 已被印出，故 'b' 為最後位
- 重點：`setTimeout` 是一個異步操作而非同步執行，故在正常的執行佇列中會是最後被執行的（即使延遲為 0 毫秒）

#### 執行結果為何？
 ```js
function exec() {
  setTimeout(function (){
    console.log('a')
  }, 0) 
  new Promise(function (resolve, reject) {
    console.log('b')
  })
  console.log('c')
}
exec()

//  A. 印出 a, b, c
//  B. 印出 b, c, a
//  C. 印出 c, b, a
//  D. 印出 c, a, b
//  E. 以上皆非
```
> 答案為 B。
- 因 Promise 建構函式會立即執行，故首先印出 'b'，而 'c' 是在 Promise 之後的同步程式碼，故接著印出；'a' 則是最後被印出，因其在 setTimeout 內的函式被放入佇列，但由於指定了延時為 0 毫秒，所以它不會立即執行。 它會等待當前事件循環中的所有微任務執行完畢，然後才會執行。
- 重點：`Promise` 建構函式會立即執行

#### 先執行 a.js 再執行 b.js，b.js 印出的數值為？
```js
class Counter {
  constructor(){
    this.value = 0
  }
  plus() {
    this.value++
  }
}

let counter = new Counter()
export default counter

// a.js
import counter from './counter.js'
counter.plus()

// b.js
import counter from './counter.js'
counter.plus()
console.log(counter.value)
```
> 答案為 2
- 因在 a.js 中，counter 實例被創建，且執行了 `counter.plus() `，故此時 `counter.value` 為 1。其後於 在 b.js 中再次匯入 counter，並執行 `counter.plus()` 方法，再次將 counter 的值增加了1，此時 `counter.value` 變為 2。
- 重點：即使你在 a.js 和 b.js 中都使用 `let counter = new Counter()` 創建了不同的counter 變量，它們仍然會引用相同的 Counter 類別的實例，因此它們會相互影響，即彼此共享相同的狀態。 這是因為 **Counter 類別的實例在記憶體中只有一個**，它被兩個變數引用。


#### 視覺上 .container 的顏色應為？
```html
<div class="container">
  <div class="inner" id="inner"></div>
</div>
```
```css
div {
  width: 100px;
  height: 100px;
  background: black;
}
div:before {
  width: 100px;
  height: 100px;
  background: red;
  content: '';
}
inner:before {
  background: blue;
}
```
> 答案為 black。
- 因兩個偽元素的 `content` 都沒有設定，且也沒有設定 `display: block`，故在畫面上不會顯示
- 可參考 [JSBin](https://jsbin.com/livevoyice/1/edit?html,css,output) 上的實際情況，更易釐清

#### 執行後控制台會先後印出什麼？
```js
exec()
function exec () {
  console.log('ok')
}
var exec = function () {
  console.log('not ok')
}
```
```js
exec()
function exec () {
  console.log('ok')
}
var exec = function () {
  console.log('not ok')
}
exec()
```
> 兩題答案都是執行錯誤
- 因 `exec` 被重複宣告

#### 下列情境，執行下各會印出什麼？
```js
var x = 200
function exce() {
  var y = 0 // 2. 若將此處改為 100，則輸出結果為？
  if(y) {
    var x = 100  // 3. 若 y 維持 100，並將此行註解，則輸出結果為？
  }
  console.log(x) // 1. 此處的輸出結果為？
}
exce()
```
> 1. undefined  2. 100  3. 200
- 重點： **function 的 hoisting(提升)會優先於 var 宣告**，故 `exce()` 執行時，x 尚未被宣告變量，所以回傳 undefined


### 上機考

#### MarriorNumber
```js
function mirrorNumber (num){
  ...
  return // true or false
}

mirrorNumber(123321)  // true，123 的鏡像為 321
mirrorNumber(1234321)  // false，1234 的鏡像不為 321
mirrorNumber(1111)  // true，11 的鏡像為 11
mirrorNumber(971451154179)  // 971451 的鏡像為 154179
```
- [作答](https://jsbin.com/qajacereye/1/edit?js,console)


#### 邏輯判斷
```js
function logic(num){
  ...
}

logic(1) // 1
logic(2) // 3
logic(3) // 7
logic(4) // 15
logic(5) // ??
```
- [作答](https://jsbin.com/hiwezuvehe/1/edit?js,console)


#### diffString
```js
const a = 'abcd'
const b = 'bdafh'

function diff(a, b) {
  ...
  return // 回傳字串 b 當中跟字串 a 不重複的字符，不論順序
}

diff(a, b) // 'fh'
```
- [作答](https://jsbin.com/xivarihisu/1/edit?js,console)
- [作答2](https://jsbin.com/qaqesupolo/1/edit?js,console) - 使用 `Set`


### 檢討

本次答題部分覺得沒有太大障礙，但對於基礎 JS 的熟悉度依舊還需加強，可能因為習慣使用框架處理，某些不常於實務中操作的語法顯得生疏。

面試中考的題目有些挺有意思，尤其比較兩組字串、陣列的差異值這題應該算是實務上比較常碰到的情境。