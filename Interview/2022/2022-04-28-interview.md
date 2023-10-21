# 鑫城商務面試

## 面試流程

 - 紙本考題，簡答 2 題，程式題 5 題，切版 1 題- **30 min**
 - 前端主管 & 前端團隊面談（遠距視訊） - **20 min**
    - 口頭簡答
      - Vue life circle （萬年老題）
      - computed & watch 的用途及效能差異
      - 是否有使用過 JSX 及 TypeScript 的經驗
      - 有無寫過 App 的經驗
 - 大主管面談 + HR 詢問待遇、介紹福利（遠距視訊） - **20 min**
    - 簡單了解公司產品（包網）及目前使用技術及團隊規模


### 紙本題

#### 請說明 let, var, const 差別 (10 分)

var 的宣告是處於全域變數，在 JS 中會被自動 hoisting，但也有著容易被汙染的問題。

let 及 const 是 ES6 後出現的宣告，此兩種宣告方式不能被重複宣告，所以不會像 var 一樣容易不小心出現變數污染的情形。

其中又以 const 不能重複賦值，所以通常用於宣告不會改動的變數。


#### 請說明Arrow Function與General Function的區別 (10 分)

箭頭函式為 ES6 新增的寫法，與普通函式不同的地方在於箭頭函式本身並沒有綁定的 this 變數。

同時 箭頭函式不適合作為物件的方法( Method )中使用  (因為在撰寫上十分彆扭，不方便)

在事件處理器上，綁定物件代表觸發的對象；但若使用箭頭函式，就無此對應關係 (會回饋到全域變數 window 或當前函式的上層環境)

舉例來說
```js
  const user = {
    email: 'my@mail.com',
    update: email => {
      this.email = email
    }
  }
  user.update('new@mail.com')
  console.log(user.email) // 'my@mail.com'
```
- 因此處的 `update()` 為箭頭函式，故 `this.email` 指向到外層 user 物件的值，即為 'my@mail.com'


#### 寫出此程式片段在 Chrome 瀏覽器控制台會印出甚麼樣的結果? (10 分)
```js
(function(){
  var a;
  console.log(typeof a);
  function a(){}
  a = 'hello';
  console.log(typeof a);
  var a;
  console.log(typeof a);
})();
```
> 答案為 function, string, string
- 本題在考 hoisting(提升)。function 優先於 var 宣告，故首先印出 'function'；之後宣告了 a 變數，並指定其變量為 'hello' 字串，故接著印出 'string'，此時變數 a 已經有了變量('hello')，接著重複宣告 a 並不會改變其值(多餘的變數宣告)，故依然印出 'string'
- 重點：**在 hoisting 時，function 會優先於 var 宣告**


#### 寫出此程式片段在 Chrome 瀏覽器控制台會印出甚麼樣的結果? (10 分)
```js
n = 10
function func1() {
  var n = 99;
  nAdd = function () {
    this.n += 1;
    console.log(this.n)
  }
  function fun2() {
    console.log(n);
  }
  return fun2;
}
var result = func1();
result()
nAdd()
result()
```
> 答案為 99, 11, 99
- n = 10：這裡宣告一個全域變數 n，其值為 10。
- function func1()：這是一個函式定義，它包含了兩個內部函式：nAdd 和 fun2。在這個函式內部宣告了區域變數 n，其值為 99。
- nAdd = function () { ... }：在 func1 函式內，宣告了一個全域變數 nAdd，它是一個匿名函式，這個匿名函式將全域變數 n 增加 1，並將其值印出。當 nAdd 函式執行時，它會改變全域變數 n 的值。
- function fun2()：在 func1 函式內部宣告了一個區域函式 fun2，它印出區域變數 n 的值。由於 fun2 在 func1 內部定義，它可以訪問 func1 內部的區域變數 n。
- var result = func1();：呼叫 func1 函式並將其返回的 fun2 函式存儲在 result 變數中。
- result()：呼叫 result，這實際上是呼叫了 fun2，它印出了 func1 內部的區域變數 n 的值，即 99。
- nAdd()：呼叫 nAdd 函式，它增加了全域變數 n 的值，使其從 10 變為 11，然後印出 11。
- result()：再次呼叫 result（實際上是 fun2），它印出了 func1 內部的區域變數 n 的值，但由於 fun2 在第一次呼叫時已經捕獲了 n 的值，因此印出的仍然是 99。
- 總結，函式 fun2 在第一次呼叫時捕獲了 func1 內部的區域變數 n 的值，並保持不變。全域變數 n 則在 nAdd 函式中被改變。


#### 寫出此程式片段在 Chrome 瀏覽器控制台會印出甚麼樣的結果? (10 分)
```js
let data = { a: 1, b: 2, c: { d: 3, e: { f: 4 } } }
let data2 = data

let d = data2.c.d

data = null
data2 = null

console.log(JSON.stringify(data2));
console.log(`d = ${d}`)
```
> "null", "d = 3"
- `data2` 跟 `data` 實際上是兩個變數引用了同一個物件 (call by reference)
- 變數 `d` 宣告賦值為 `data2.c.d`，即為 3，此時它是一個獨立的變數，不再受原先的 `data2` 影響 
- 所以在 ```data2 = null``` 時，變數 `d` 的值依然是 3

> - 題外話，若將 ```data2 = null``` 註解掉，此時 ```console.log(JSON.stringify(data2))``` 的結果將為 `{ a: 1, b: 2, c: { d: 3, e: { f: 4 } } }`
> - 但若將前一行改寫為 ```data.a = null```， ```console.log(JSON.stringify(data2))``` 則會印出 `{ a: null, b: 2, c: { d: 3, e: { f: 4 } } }`
> - 這就是上面說到的 call by reference（傳參考呼叫）


#### 以下程式碼輸出的結果是甚麼? 為甚麼會有這樣的結果? 若必須使⽤ setTimeout 來 log 結果，如何改寫能得到原本預期的結果（1，2，3，4，5，6）? (10 分)
```js
const arr = [1,2,3,4,5,6];
for(var i = 0; i < arr.length; i++) {
  setTimeout(()=> {
  console.log(arr[i])
  }, 0)
}
```
> 會回傳六次 undefined，因為 `for(var i = 0; i < arr.length; i++)` 此段迴圈執行完後，setTimeout 中最終得 i 為 6，而 arr[6] 得出 undefined

> 若要修正結果，必須每次都將迴圈中 i 的值保存並傳入
```js
const arr = [1, 2, 3, 4, 5, 6];
for(var i = 0; i < arr.length; i++) {
  timeOut(i)
}
function timeOut(i){
  setTimeout( () => {
    console.log(arr[i])
  }, 0)
}
```


#### ⼆维转换，請將下列之 data 陣列轉換為指定的 result 結果 (30 分)
```js
// Input:
const data = [
  [
    { id: 1, value: 100 },
    { id: 2, value: 200 },
    { id: 3, value: 300 },
  ], [
    { id: 1, value: 101 },
    { id: 2, value: 201 },
    { id: 3, value: 301 },
  ], [
    { id: 1, value: 102 },
    { id: 2, value: 202 },
    { id: 3, value: 302 },
  ], [
    { id: 1, value: 103 },
    { id: 2, value: 203 },
    { id: 3, value: 303 },
  ]
  ]
/*
// Output:
result = [
  [100, 101, 102, 103],
  [200, 201, 202, 203],
  [300, 301, 302, 303]
]
*/
```
- 以下為當時的作答，現在想想太過冗長且複雜，~~雖然答案對了但應該算不及格才是~~
```js
function test(array){
  let flatten = []
  let result = []

  // 將陣列 data 攤平
  for(let i in array){
    for(let k in array[i]){
      flatten.push(array[i][k])
    }
  }
  // console.log(flatten)

  // 抓取 id 值
  let ID = []
  for(let i in flatten){
    ID.push(flatten[i].id)
  }
  // 刪去重複
  const IDkey = ID.filter(function(ele , pos){
    return ID.indexOf(ele) == pos;
  })   
  // console.log(IDkey) // [1, 2, 3]
  
  // 重新編輯陣列
  let newArray = []
  for(let i in IDkey){
    const filter = flatten.filter( item => {
      return item.id === IDkey[i]
    })
    newArray.push(filter)
  }
  
  for(let i in newArray){
    // console.log(newArray[i])
    let item = []
    for(let k in newArray[i]){
      item.push(newArray[i][k].value)
    }
    // console.log(item)
    result.push(item)
  }

  console.log(result)
}

test(data) // [[100, 101, 102, 103], [200, 201, 202, 203], [300, 301, 302, 303]]
```
- 其實只要針對物件內陣列的長度去做遍歷就好，所以跑兩個迴圈（一個迴圈遍歷每一列「從左到右」，另一個迴圈遍歷每一行「從上到下」）即可得出結果，不必這麼複雜
```js
const result = [];

for (let i = 0; i < data[0].length; i++) {
  result[i] = [];
  for (let j = 0; j < data.length; j++) {
    result[i].push(data[j][i].value);
  }
}

console.log(result);
```
- 或是使用 `Aarray.map`
```js
const result = data[0].map((_, i) => data.map(row => row[i].value));

console.log(result);
```

> - 雖說當時的作法很笨，但也就是因為土法煉鋼的很徹底，所以如果題目變種反而能更彈性的應對
> - 例如說題目若改為以下
```js
// Input:
const data = [
  [
    { id: 1, value: 100 },
    { id: 2, value: 200 },
    { id: 3, value: 300 },
    { id: 4, value: 400 },
  ], [
    { id: 1, value: 101 },
    { id: 2, value: 201 },
    { id: 3, value: 301 },
  ], [
    { id: 1, value: 102 },
    { id: 2, value: 202 },
    { id: 3, value: 302 },
  ], [
    { id: 1, value: 103 },
    { id: 2, value: 203 },
    { id: 3, value: 303 },
    { id: 4, value: 404 },
  ]
  ]
/*
// Output:
result = [
  [100, 101, 102, 103],
  [200, 201, 202, 203],
  [300, 301, 302, 303],
  [400, 404]
]
*/
```
> - 原先笨的徹底的做法就不需要任何修改也可以回傳想要的答案
> - 呈上題，若題目變種，使用 `Array.map` 的答案請參考 [這裡](https://jsbin.com/vohajocema/1/edit?js,console)


#### 請使⽤css配合下列html將畫⾯排列成上中下三部分，並實現下列需求 (30 分)
> 1. 總⾼度為滿版的⾴⾯；
> 2. . header/body/footer區塊需佔整個可視區寬度；
> 3. . body內部分成橫向的左中右區塊。其中左區塊的寬度隨著內容寬度增減，但最⼩不⼩於
60px；右區塊的寬度為固定寬度；
> 4. footer需貼合⾴⾯底部；
```html
<Header class="header"/>
<div class="body">
  <div class="left">
    left fit content width
  </div>
  <div class="center">
    center fit content width
  </div>
  <div class="right">
    right fixed width
  </div>
</div>
<footer class="footer"/>
```
- 當時的答案：**沒做完**，因為前一題花太多時間，沒時間寫了
- 現在的答案：~~想到再回來補好了~~


### 檢討

不常碰觸的 method 就幾乎沒有去研究了解的習慣，當時作業環境很少使用到 `Array.map`，或者說其實有機會用到但自己都習慣用遍歷的方式處理，沒什麼長進。

但在此次後有深刻的檢討體悟，在後續的工作上的確有較常使用原先不熟悉的各項 Array method，也算是一種進步？

30 分鐘的考試時間有點趕，但其實這份考題題目出得挺不錯的（雖然 2021.3 後就沒有變過） ~~這都是後話了~~
硬要講最大的問題就是時間不夠長的情況下，簡答題還是直接口述問答吧 XD
太花時間了啦！

這次面試也算是踏出舒適圈的一步，終於比較瞭解所謂的「包網」是在處理哪些業務