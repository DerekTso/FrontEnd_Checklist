# Javascript基础 - 数组操作

## 目录
- [forEach()](#foreach)
- [map()](#map)
- [reduce()](#reduce)
- [reduceRight()](#reduceright)
- [filter()](#filter)
- [every()](#every)
- [some()](#some)
- [concat()](#concat)
- [join()](#join)
- [push()](#push)
- [pop()](#pop)
- [unshift()](#unshift)
- [shift()](#shift)
- [slice()](#slice)
- [splice()](#splice)
- [sort()](#sort)
- [reverse()](#reverse)
- [indexOf()](#indexof)
- [lastIndexOf()](#lastindexof)
- [find()](#find)
- [includes()](#includes)
- [数组去重](#数组去重)

### forEach()

```
1. 不修改原数组
2. 没有办法中止或者跳出 forEach 循环，除了抛出一个异常

// 语法：
array.forEach(callback(currentValue, index, array){
    //do something
}, thisArg)

// callback 函数会被依次传入三个参数：
currentValue: 数组当前项的值
index(可选): 数组当前项的索引
array(可选): 数组对象本身

thisArg(可选): 当执行回调函数时用作this的值

var colors = ['red', 'green', 'blue'];
colors.forEach(function(color) {
  console.log(color);
}); // 返回值 undefined
```

### map()

```
1. 不修改原数组
2. callback 函数会被自动传入三个参数：ele(数组元素)，index(元素索引)，array(原数组)

var numbers = [1, 4, 9];
var roots = numbers.map(Math.sqrt);
// roots的值为[1, 2, 3], numbers的值仍为[1, 4, 9]

// 获取字符串中每个字符所对应的 ASCII 码组成的数组
var map = Array.prototype.map
var a = map.call("Hello World", function(x) { 
  return x.charCodeAt(0); 
})
// a的值为[72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100]

// 面试题 ["1", "2", "3"].map(parseInt)

1. 首先, map接受两个参数, 一个回调函数 callback, 一个回调函数的this值
2. 其中回调函数接受三个参数 currentValue, index, arrary
3. 题目中, map只传入了回调函数--parseInt
4. 其次, parseInt 只接受两个两个参数 string, radix(基数, 2~36之间)
5. 如果省略 radix 参数或其值为 0，则数字将以 10 为基础来解析
6. 如果它以 “0x” 或 “0X” 开头，将以 16 为基数
7. 所以本题即问

parseInt('1', 0);
parseInt('2', 1);
parseInt('3', 2);
// return [1, NaN, NaN]
```

### reduce()

```
1. 不修改原数组
2. 接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce 的数组
3. 如果数组为空并且没有提供 initialValue，会抛出 TypeError
4. 如果数组仅有一个元素，并且没有提供 initialValue，或者有提供 initialValue 但是数组为空，它将被返回并且 callback 不会被执行

[0,1,2,3,4].reduce(function(previousValue, currentValue, index, array){
  return previousValue + currentValue;
}); // return 10

[0,1,2,3,4].reduce(function(previousValue, currentValue, index, array){
  return previousValue + currentValue;
}, 10); // return 20

var flattened = [[0, 1], [2, 3], [4, 5]].reduce(function(a, b) {
    return a.concat(b);
}); // flattened is [0, 1, 2, 3, 4, 5]

// 面试题 [[3,2,1].reduce(Math.pow), [].reduce(Math.pow)]

1. reduce接受两个参数, 一个回调, 一个初始值
2. 回调函数接受四个参数 previousValue, currentValue, currentIndex, array
3. 如果数组为空并且没有提供 initialValue，会抛出 TypeError
4. 所以第二个表达式会报异常
5. 第一个表达式等价于 Math.pow(3, 2) => 9; Math.pow(9, 1) =>9
```

### reduceRight()

```
1. 不修改原数组

var flattened = [[0, 1], [2, 3], [4, 5]].reduceRight(function(a, b) {
    return a.concat(b);
}, []);
// flattened is [4, 5, 2, 3, 0, 1]
```

### filter()

```
1. 不修改原数组
2. callback返回 true 表示保留该元素，false则不保留

function isBigEnough(element) {
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]

// 面试题

var ary = [0,1,2];
ary[10] = 10;
ary.filter(function(x) { return x === undefined;});
// 返回空数组 []

首先检查一下这个索引值是不是数组的一个属性：
0 in ary; => true
3 in ary; => false
10 in ary; => true

也就是说 从 3 - 9 都是没有初始化的bug, 这些索引并不存在与数组中. 在 array 的函数调用的时候是会跳过这些坑的
```

### every()

```
1. 不修改原数组
2. 找到一个使 callback 返回 false 的元素，将会立即返回 false。否则，返回 true

function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [12, 5, 8, 130, 44].every(isBigEnough);
// passed is false
passed = [12, 54, 18, 130, 44].every(isBigEnough);
// passed is true
```

### some()

```
1. 不修改原数组
2. 找到一个使得 callback 返回一个true 的元素，将会立即返回 true。否则，返回 false

function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [2, 5, 8, 1, 4].some(isBigEnough);
// passed is false
passed = [12, 5, 8, 1, 4].some(isBigEnough);
// passed is true
```

### concat() 

```
1. 不修改原数组
2. 返回合并后的新数组

var myArray = new Array("1", "2", "3");
var newArray = myArray.concat("a", "b", "c");
// newArray is now ["1", "2", "3", "a", "b", "c"]
```

### join() 

```
1. 不修改原数组
2. 用一个分隔符将这些字符串连接起来(默认逗号分隔符)
3. 如果元素是 undefined 或者 null， 则会转化成空字符串

var myArray = new Array("Wind", "Rain", "Fire");
var list = myArray.join(" - ");
// list is "Wind - Rain - Fire"
```

### push() 

```
1. 会修改原数组
2. 添加一个或多个元素到数组的末尾，并返回数组的新长度

var sports = ["soccer", "baseball"];
var total = sports.push("football", "swimming");
console.log(sports);
// ["soccer", "baseball", "football", "swimming"]
console.log(total);
// 4

var vegetables = ['parsnip', 'potato'];
var moreVegs = ['celery', 'beetroot'];
Array.prototype.push.apply(vegetables, moreVegs);
console.log(vegetables);
// ['parsnip', 'potato', 'celery', 'beetroot']
```

### pop() 

```
1. 会修改原数组
2. 删除数组中的最后的一个元素，且返回这个元素

var myArray = new Array("1", "2", "3");
var last = myArray.pop();
// myArray is now ["1", "2"], last = "3"
```

### unshift() 

```
1. 会修改原数组
2. 在数组的开头添加一个或者多个元素，并返回数组的新长度

var arr = [1, 2];
arr.unshift(0); // return 3, arr is [0, 1, 2]
arr.unshift(-2, -1); // return 5, arr is [-2, -1, 0, 1, 2]
arr.unshift( [-3] ); // return 6, arr is [[-3], -2, -1, 0, 1, 2]
```

### shift() 

```
1. 会修改原数组
2. 删除数组的第一个元素，并返回这个元素
3. 如果 length 属性的值为 0 ，则返回 undefined

var myFish = ['angel', 'clown', 'mandarin', 'surgeon'];
var shifted = myFish.shift(); // myFish: "clown, mandarin, surgeon" 
console.log('被删除的元素: ' + shifted); // "被删除的元素: angel"
```

### slice()

```
1. 不修改原数组
2. 提取原数组中索引从 begin 到 end 的所有元素（包含begin，但不包含end）
3. 如果 end 被省略，则slice 会一直提取到原数组末尾
4. 如果 end 参数为负数，则表示在原数组中的倒数第几个元素结束抽取

function list() {
  return Array.prototype.slice.call(arguments);
}
var list1 = list(1, 2, 3); // [1, 2, 3]
```

### splice()

```
1. 会修改原数组
2. splice(index, count_to_remove, addelement1, addelement2, ...)

var myArray = new Array ("1", "2", "3", "4", "5");
myArray.splice(1, 3, "a", "b", "c", "d");
// myArray is now ["1", "a", "b", "c", "d", "5"]
```

### sort()

```
1. 会修改原数组
2. 对数组的元素进行排序，并返回排序后的数组
3. 当排序非 ASCII 字符的字符串（如包含类似 e, é, è, a, ä 等字符的字符串），需要使用 String.localeCompare

var a = [1,2,3,5,4];
a.sort();
// a: [1, 2, 3, 4, 5]

var items = ['réservé', 'premier', 'cliché', 'communiqué', 'café', 'adieu'];
items.sort(function (a, b) {
  return a.localeCompare(b);
});
// items is ['adieu', 'café', 'cliché', 'communiqué', 'premier', 'réservé']
```

### reverse()

```
1. 会修改原数组
2. 颠倒数组中元素的位置，并返回颠倒后的数组

var myArray = ['one', 'two', 'three'];
myArray.reverse(); 
console.log(myArray) // ['three', 'two', 'one']
```

### indexOf()

```
1. 返回首个被找到的元素在数组中的索引位置
2. 若没有找到则返回 -1
2. 使用严格相等（===）比较进行查找

var array = [2, 5, 9, "7"];
array.indexOf(2); // 0
array.indexOf(7); // -1
```

### lastIndexOf()

```
1. 返回指定元素在数组中的最后一个的索引
2. 若没有找到则返回 -1
2. 使用严格相等（===）比较进行

var array = [2, 5, 9, 2];
var index = array.lastIndexOf(2);
// index is 3
index = array.lastIndexOf(7);
// index is -1
```

### find()

```
1. 不修改原数组
2. 如果数组中某个元素满足测试条件，返回那个元素的值
3. 如果没有满足条件的元素，则返回 undefined

var inventory = [
    {name: 'apples', quantity: 2},
    {name: 'bananas', quantity: 0},
    {name: 'cherries', quantity: 5}
];
function findCherries(fruit) { 
    return fruit.name === 'cherries';
}
console.log(inventory.find(findCherries));
// { name: 'cherries', quantity: 5 }
```

### includes()

```
1. 不修改原数组
2. 判断当前数组是否包含某指定的值，如果是，则返回 true，否则返回 false

[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false

// includes 与 indexof 的区别
1. includes 与 indexof 既可以进行字符串的判断，也可以进行数组值的判断
2. 但是 indexof 在对 NaN 进行判断时会出现不准确
3. indexof 的返回结果为 -1||0，includes 为 true||false

var text = [1,NaN]
console.log(text.indexOf(NaN)); //-1
console.log(text.includes(NaN)); //true
```

### 数组去重

```
思路1：定义一个新数组，并存放原数组的第一个元素，然后将元素组一一和新数组的元素对比，若不同则存放在新数组中
思路2：先将原数组排序，在与相邻的进行比较，如果不同则存入新数组。
思路3：利用对象属性存在的特性，如果没有该属性则存入新数组。
思路4（最常用）：使用es6 set

let arr= [1, 2, 3, 3, 5, 7, 2, 6, 8];
console.log([...new Set(arr)]);
```
