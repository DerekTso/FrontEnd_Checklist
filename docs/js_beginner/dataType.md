# Javascript基础 - 数据类型

## 目录
- [数据类型(Data type)](#数据类型data-type)
- [数据类型转换(Data type conversion)](#数据类型的转换data-type-conversion)
- [数据类型检测(Data type detection)](#数据类型检测data-type-detection)
- [对象字面量(Object literals)](#对象字面量object-literals)

### 数据类型(Data type)

```
// 六种原型数据类型(primitive type):
undefined、null、boolean、number、string 和 Symbol (在 ES6 中新添加的类型，它的实例是唯一且不可改变的）

// 引用类型(reference type):
对象(Object)、数组(Array)、函数(Function)
```

### 数据类型转换(Data type conversion)

```
// 强制转换: 利用js提供的函数parseInt() ,parseFloat() ,Number(),Boolean()进行数据转换。

> parseInt(string, radix)
string，必需。要被解析的字符串。
radix，可选。表示要解析的数字的基数

parseInt("123"); //123
parseInt("+123"); //123
parseInt("-123"); //-123
parseInt("12.3元"); //12
parseInt("abc"); //NaN
parseInt([1,2]); //1
parseInt([1,2,4]); //1
parseInt("  "); //NaN

parseInt("10",0); //10
parseInt("10",2); //2
parseInt("10",8); //8
parseInt("10",10); //10
parseInt("AA",16); //170
parseInt("0xAA")//17

parseInt("010"); //10 
parseInt("010",2); //2
parseInt("010",8); //8
parseInt("010",10); //10 
parseInt(null); //NaN
```

```
> parseFloat(string)
string，必需。要被解析的字符串。

parseFloat("123"); //123
parseFloat("-123"); //-123
parseFloat("+123"); //123
parseFloat("12.34"); //12.34
parseFloat("12.35元"); //12.35
parseFloat("12.23.122"); //12.23
parseFloat("test"); //NaN
parseFloat("0xAA"); //0
parseFloat("0110"); //110
parseFloat([1]); //1
parseFloat([2,3]); //2
parseFloat([]); //NaN
parseFloat(null); //NaN
```

```
> Number(object)
object，必需。JavaScript 对象。

Number("123"); //123
Number("+123"); //123
Number("12.3"); //12.3
Number(true); //1
Number(" "); //0
Number([]); //0
Number(null); //0
Number(false); //0
Number([1]); //1
Number([1,2]); //NaN
Number("abc"); //NaN
Number("12.3.4"); //NaN
Number(new Object()); //NaN
Number(undefined); //NaN

// 如果参数是 Date 对象，Number() 返回从 1970 年 1 月 1 日至今的毫秒数。
Number(new Date()); //1536805209002
```

```
> Boolean(object)
不会对引号里面的数字进行自动进行转换。

Boolean(1); //true
Boolean(0); //false
Boolean("1"); //true
Boolean("0"); //true
Boolean("abc"); //true
Boolean(''); //false
Boolean('   '); //true
Boolean([]); //true
Boolean([1]); //true
Boolean(null); //false
Boolean(NaN); //false
```

```
> String(object)
object，必需。JavaScript 对象。

String(null); //"null"
String(undefined); //"undefined"
```

```
// 隐式类型转换: 字符串和数字之间进行减乘除取模运算或者进行比较运算时，会自动把字符串转换为数字。
// 转换数字的默认方法是调用Number()，进行加法运算则是将数字看成字符串进行拼接。

var x = "123";
var y = 121;

alert(x+y); //"123121;

alert(x-y); //2
alert(x*y); //14883
alert(x/y); //1.016528256198346
alert(x%y); //2

alert(x>y); //true
alert(x==y); //false
alert("123a">y); //false
// Number("123a") => NaN
// NaN > 123 => false // NaN参与运算所得的结果也是NaN
// Boolean(NaN) => false
```

```
// undefined 不发生类型转换
console.log(undefined == undefined); //true
console.log(undefined == 0); //false
console.log(undefined > 0); //false
console.log(undefined < 0); //false
```

```
// null 不发生类型转换
console.log(null == null); //true
console.log(null == 0); //false
console.log(null > 0); //false
console.log(null < 0); //false
console.log(undefined == null); //true 
```

```
[] == []; //false
[] == {}; //false
当两个值都是对象(引用值) 时, 比较的是两个引用值在内存中是否是同一个对象。
```

```
[] == ![]; //true
![] 的值是 false, 此时表达式变为 [] == false, 
比较变成了 [] == ToNumber(false), 即 [] == 0,
又变成了 ToPrimitive([]) == 0, 即 '' == 0, 
接下来就是比较 ToNumber('') == 0, 也就是 0 == 0, 最终结果为 true。
```

```
若a={};b={}; 则a==b 返回false
若a=b={}则返回true
第一句两个都是空对象 但两个是不同的空对象
第二句都是同一个空对象所以相等
```

```
1. 当"=="号两边其中一个是布尔值的话，先把它转换为数字
2. 当"=="的一边是字符串或数字，另一边是对象的时候，先把对象值转换为原始值再判断相等
3. 当"=="两边一个是字符串一个是数字的时候，先把字符串转为数字
```

```
1.所有对象先调用valueOf()方法，如果此方法返回的是原始值，则对象转为这个原始值
2.如果valueOf方法返回的不是原始值，则调用toString方法，如果toString方法返回的是原始值，则对象转换为这个原始值。
3.如果valueOf和toString方法均没有返回原始值，则抛出TypeError异常.
```

### 数据类型检测(Data type detection)

```
// typeof 返回值:
"number"、"string"、"boolean"、
"undefined"、"function"、"object"

console.log(typeof 12); // number
console.log(typeof "test"); // string
console.log(typeof false); // boolean
console.log(typeof undefined); // undefined
console.log(typeof function(){}); // function

console.log(typeof {}); // object
console.log(typeof new Date()); // object
console.log(typeof new RegExp()); // object

console.log(typeof null); // object

// 使用 typeof 检测数据类型，对于对象数据类型中的所有的值，最后返回的结果都是"object"
// 所以 typeof 不能具体的细分是数组还是正则，还是对象中其他的值
```

```
// instanceof

console.log(null instanceof Object); // false

var ary = [];
console.log(ary instanceof Array); // true
console.log(ary instanceof Object); // true

function fn() {}
console.log(fn instanceof Function); // true
console.log(fn instanceof Object); // true

function Animal(){}
function Dog(){}
Dog.prototype = new Animal();
var dog = new Dog();
console.log(dog instanceof Dog); //true
console.log(dog instanceof Animal); //true
console.log(Animal instanceof Function); //true
console.log(Animal instanceof Animal); //false

console.log(Object instanceof Object); //true
console.log(Function instanceof Function); //true
console.log(Function instanceof Object); //true
console.log(Number instanceof Number); //false
console.log(String instanceof String); //false

console.log(/abc/ instanceof RegExp); //true
console.log(new Date() instanceof Date); //true
```

### 对象字面量(Object literals)

```
// 对象属性名字可以是任意字符串，包括空串。
// 如果对象属性名字不是合法的javascript标识符，它必须用""包裹。
// 属性的名字不合法，那么便不能用.访问属性值，而是通过类数组标记("[]")访问和赋值。

var unusualPropertyNames = {
  "": "An empty string",
  "!": "Bang!"
}
console.log(unusualPropertyNames.""); // Unexpected string
console.log(unusualPropertyNames[""]); // An empty string
console.log(unusualPropertyNames.!); // Unexpected token !
console.log(unusualPropertyNames["!"]); // Bang!

var foo = {a: "alpha", 2: "two"};
console.log(foo.a); // alpha
console.log(foo.2); // Error: missing ) after argument list
console.log(foo[a]); // Error: a is not defined
console.log(foo[2]); // two
console.log(foo["a"]); // alpha
console.log(foo["2"]); // two
```