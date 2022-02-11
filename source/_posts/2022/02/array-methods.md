---
title: 陣列的處理方法｜The Complete JavaScript Course｜bacnotes備份筆記
date: 2022-02-11 12:23:33
updated: 2022-02-11 12:23:33
excerpt: 陣列是許多fetch資料後需要處理的資料型態，這份陣列方法懶人包整理了slice()、concat()、join()、at()、map()、filter()、reduce()、find()、splice()、reverse()、forEach()

等用法。
categories:
  - JavaScript
---

## 不會改變原始資料

### slice()

- array.slice([start[, end]])
- 跟展開運算子 [...array]一樣常被用作淺拷貝
- 回傳一個新陣列物件，為原陣列選擇之 start 至 end（end index 元素不包含在回傳結果）的淺拷貝
- 可以不傳參數(start 預設 0，end 預設 array.length)
- 可單傳 start 參數或 start end 都傳
- 接受負數，slice(2,-1) 代表拷貝陣列中第三個元素至倒數第二個元素

```js
const animals = ["ant", "bison", "camel", "duck", "elephant"];

animals.slice();
// ['ant', 'bison', 'camel', 'duck', 'elephant']

animals.slice(2);
// ["camel", "duck", "elephant"]

animals.slice(2, 4);
// ["camel", "duck"]

animals.slice(-2);
// ["duck", "elephant"]
```

### concat()

- var newArray = oldArray.concat(value1[, value2[, ...[, valueN]]])
- 合併兩個或多個陣列。回傳一個包含呼叫者陣列本身的值，作為代替的是回傳一個新陣列
- 跟展開運算子[...arr, ...arr2] 一樣

```js
const array1 = ["a", "b", "c"];
const array2 = ["d", "e", "f"];
array1.concat(array2);

// ["a", "b", "c", "d", "e", "f"]
```

### join()

- array.join([separator])
- 回傳合併所有陣列元素的字串
- separator 為用來隔開陣列中每個元素的字串。如果必要的話，separator 會自動被轉成字串型態。如果未傳入此參數，陣列中的元素將預設用英文逗號（「,」）隔開。如果 separator 是空字串，合併後，元素間不會有任何字元。
- 任何 undefined 或 null 的元素都會被視為空字串處理。
- 假如 arr.length 為 0，將回傳空字串。

```js
var a = ["Wind", "Rain", "Fire"];
a.join(); // 'Wind,Rain,Fire'
a.join(""); // 'WindRainFire'
a.join(", "); // 'Wind, Rain, Fire'
a.join(" + "); // 'Wind + Rain + Fire'
```

### at()

- array.at(index)等於 array[index]
- 回傳索引的元素，通常用來取最尾端的元素
- 允許正整數和負整數。負整數從數組中的最後一項開始倒數
- 找不到元素回傳 undefined

```js
const array = [5, 12, 8, 130, 44];

let index = 2;

console.log(`index ${index + 1} is ${array.at(index)}`);
// "index 2 is 8"

index = -2;

console.log(`index ${index + 1} is ${array.at(index)}`);
// "index -2 is 130"
```

### map()

- var new_array = arr.map(function callback(currentValue[, index[, array]]) {
  // Return element for new_array
  }[, thisArg])
- 把元素丟到函式裡執行，並回傳一個新陣列
- callback 可以收三個參數（當前處理函數、當前處理函數 index、正在操作的陣列）
- 可以用作淺拷貝

```js
const array = [1, 4, 9, 16];

// pass a function to map
const map = array.map((x) => x * 2);

console.log(map);
// [2, 8, 18, 32]
```

### filter()

- var newArray = array.filter(callback(element[, index[, array]])[, thisArg])
- 回傳通過該函式檢驗之元素所構成的新陣列
- callback 可以收三個參數（當前處理函數、當前處理函數 index、正在操作的陣列）

```js
const words = [
  "spray",
  "limit",
  "elite",
  "exuberant",
  "destruction",
  "present",
];

const result = words.filter((word) => word.length > 6);

console.log(result);
// ["exuberant", "destruction", "present"]
```

### reduce()

- array.reduce(callback[accumulator, currentValue, currentIndex, array], initialValue)
- 迭代到的陣列元素，回傳 accumulator 處理結果，accumulator 是代表邏輯複用的函式
- callback 可以收四個參數（accumulator 重複累加函式邏輯、當前處理函數 index、正在操作的陣列）
- 若有傳入 initialValue，則由索引 0 之元素開始，若無則自索引 1 之元素開始
- 空陣列呼叫 reduce() 方法且沒有提供累加器初始值，將會發生錯誤

```js
// a is accumulator, b is currentValue

// 累加
const array = [1, 2, 3, 4];
const reducer = (a, b) => a + b;
const added = array.reduce(reducer));
console.log(added)
// 6

// 找max最大值
const movements = [200, 450, -400, 3000, -650, -130, 70, 1300]
const reducer = (a, b) => {
  if (a > b) return a
  else return b
}
const maxValue = movements.reduce(reducer)
console.log(maxVlue)
// 3000

// 攤平巢狀陣列
const array = [[0, 1], [2, 3], [4, 5]]
const reducer = (a, b) =>  a.concat(b)
const flattened = array.reduce((reducer),[]);
console.log(flattened)
// [0, 1, 2, 3, 4, 5]

// 計數器
const names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice']
const reducer = (allNames, name) => {
  if (name in allNames) {
    allNames[name]++;
  }
  else {
    allNames[name] = 1;
  }
  return allNames;
},
const countedNames = names.reduce(reducer,{});
console.log(countedNames)
// { 'Alice': 2, 'Bob': 1, 'Tiff': 1, 'Bruce': 1 }
```

### find()

- array.find(callback[, thisArg])
- 回傳第一個滿足所提供之測試函式的元素值
- 查找不到會回傳 undefined
- callback 可以收三個參數（當前處理函數、當前處理函數 index、正在操作的陣列）

```js
// 查找含有特定屬性的物件
const accounts = [{
  owner: 'Jonas Schmedtmann',
  movements: [200, 450, -400, 3000, -650, -130, 70, 1300],
  interestRate: 1.2, // %
  pin: 1111,
},  {
  owner: 'Jessica Davis',
  movements: [5000, 3400, -150, -790, -3210, -1000, 8500, -30],
  interestRate: 1.5,
  pin: 2222,
};, {
  owner: 'Steven Thomas Williams',
  movements: [200, -200, 340, -300, -20, 50, 400, -460],
  interestRate: 0.7,
  pin: 3333,
}, {
  owner: 'Sarah Smith',
  movements: [430, 1000, 700, 50, 90],
  interestRate: 1,
  pin: 4444,
}];

const account = accounts.find(account => account.owner === 'Jessica Davis')
console.log(account)


// 查找數值大於10
const array = [5, 12, 8, 130, 44];

const found = array.find(element => element > 10);

console.log(found);
// 12
```

### findIndex()

- array.findIndex(callback[, thisArg])
- 回傳第一個滿足所提供之測試函式的元素索引，若找不到會回傳-1
- callback 可以收三個參數（當前處理函數、當前處理函數 index、正在操作的陣列）
- indexOf(element, fromIndex) 也可以搜尋元素索引位置，但參數通常是給一個primitives值
- findIndex()期望參數為一個callback，可以處理複雜邏輯

```js
// 刪除特定筆資料
btnDelete.addEventListener("click", (e) => {
  e.preventDefault();
  const index = accounts.findIndex(
    (account) => account.username === currentAccount.username
  );
  accounts.splice(index, 1);
});

const array = [5, 12, 8, 130, 44];

const isLargeNumber = (element) => element > 13;

console.log(array.findIndex(isLargeNumber));
// 3
```

## 會改變原始資料

### splice()

- array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
- 回傳一個包含被刪除的元素陣列
- 如果只有一個元素被刪除，依舊是回傳包含一個元素的陣列。若沒有元素被刪除，則會回傳空陣列。
- 多數時候不在意回傳值而是修改後的陣列資料，可以用來取代陣列某個索引的元素，可以新增元素也可以刪除元素
- start 是改動點，若大於陣列長度，起始點為陣列長度。若為負，起始點為-1。複數絕對值大於陣列長度，則起始點為 0
- deleteCount 為刪除元素數量
- array.splice(-1) 等於 array.pop()

```js
const months = ["Jan", "March", "April", "June"];
months.splice(1, 0, "Feb");
// 起始點1，刪除0個元素，並插入元素'Feb'
// []

console.log(months);
// ["Jan", "Feb", "March", "April", "June"]

months.splice(4, 1, "May");
// 作為取代方法，起始點4 刪除1元素 並插入元素'May'
// [ 'June' ]

console.log(months);
// ["Jan", "Feb", "March", "April", "May"]
```

### reverse()

- array.reverse()
- 回傳反轉後的陣列

```js
const array = ["one", "two", "three"];

array.reverse();
// ["three", "two", "one"]
```

## 原始資料可改可不改，看函式邏輯

- 可以只迭代 render 在畫面上，也可修改資料 e.g.新增物件屬性等

### forEach()

- array.forEach(callback(currentValue [, index [, array]])[, thisArg])
- 對每個陣列內的元素執行一次函式的內容，不會回傳執行結果，但可以修改原來的陣列
- callback 可以收三個參數（當前處理函數、當前處理函數 index、正在操作的陣列）
- forEach 無法從迴圈跳出，會執行完畢

```js
// 交易紀錄
const array = [100, 300, -250, 200, 300, -20, 50, -100];

array.forEach((el, index, array) => {
  if (el > 0) {
    console.log(`${index + 1}: you deposited ${el} dollars`);
  } else {
    console.log(`${index + 1}: you withdrew ${Math.abs(el)} dollars`);
  }
});

// 等於之前的for of 搭配array.entries()同時取出index(key) value
for (const [index, el] of array.entries()) {
  if (el > 0) {
    console.log(`${index + 1}: you deposited ${el} dollars`);
  } else {
    console.log(`${index + 1}: you withdrew ${Math.abs(el)} dollars`);
  }
}

// 0: you deposited 100 dollars
// 1: you deposited 300 dollars
// 2: you withdrew 250 dollars
// 3: you deposited 200 dollars
// 4: you deposited 300 dollars
// 5: you withdrew 20 dollars
// 6: you deposited 50 dollars
// 7: you withdrew 100 dollars
```

- forEach 用在 set 跟 map

```js
// set
const currenciesUnique = new Set(["USD", "EUR", "GBP"]);
currenciesUnique.forEach((el, _, map) => {
  console.log(`${el}: ${el}`);
});

// USD: USD
// EUR: EUR
// GBP: GBP

// map
const currencies = new Map([
  ["USD", "United States dollar"],
  ["EUR", "Euro"],
  ["GBP", "Pound sterling"],
]);

currencies.forEach((el, index, map) => {
  console.log(`${index + 1}: ${el}`);
});
// USD: United States dollar
// EUR: Euro
// GBP: Pound sterling
```

- 增加物件屬性

```js
const accounts = [{
  owner: 'Jonas Schmedtmann',
  movements: [200, 450, -400, 3000, -650, -130, 70, 1300],
  interestRate: 1.2, // %
  pin: 1111,
},  {
  owner: 'Jessica Davis',
  movements: [5000, 3400, -150, -790, -3210, -1000, 8500, -30],
  interestRate: 1.5,
  pin: 2222,
};, {
  owner: 'Steven Thomas Williams',
  movements: [200, -200, 340, -300, -20, 50, 400, -460],
  interestRate: 0.7,
  pin: 3333,
}, {
  owner: 'Sarah Smith',
  movements: [430, 1000, 700, 50, 90],
  interestRate: 1,
  pin: 4444,
}];

const createUsernames = (array) => {
  array.forEach((account)=>{
    accounts.username = acc.owner
      .toLowerCase()
      .split(' ')
      .map(name => name[0])
      .join('')
  })
}
```
