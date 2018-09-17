# Javascript基础 - 深拷贝

## 目录
- [深拷贝的三种方法](#深拷贝的三种方法)

#### 深拷贝的三种方法

```
// 利用JSON.stringify和JSON.parse
let obj = {
    name:"tom",
    age:28
}
let copy = JSON.parse(JSON.stringify(obj))
console.log(copy) // { name:"tom",age:28 }

obj.age = 29
console.log(obj) // { name:"tom",age:29 }
console.log(copy) // { name:"tom",age:28 }
```

```
function deepCopy(fromObj,toObj) {
  // 容错
  if(fromObj === null) return null // 当fromObj为null
  if(fromObj instanceof RegExp) return new RegExp(fromObj) // 当fromObj为正则
  if(fromObj instanceof Date) return new Date(fromObj) // 当fromObj为Date

  toObj = toObj || {}
  
  for(let key in fromObj){ // 遍历
    if(typeof fromObj[key] !== 'object'){ // 是否为对象
      toObj[key] = fromObj[key] // 如果为普通值，则直接赋值
    } else {
      toObj[key] = new fromObj[key].constructor // 如果为object，则new这个object指向的构造函数
      deepCopy(fromObj[key],toObj[key]) // 递归
    }
  }
  return toObj
}

let dog = {
  name:"小白",
  sex:"公",
  firends:[
    {
      name:"小黄",
      sex:"母"
    }
  ]
}
let dogcopy = deepCopy(dog)

dog.firends[0].sex = '公'
console.log(dog)
// { name: '小白',
    sex: '公',
    firends: [ { name: '小黄', sex: '公' }] }

console.log(dogcopy)
// { name: '小白',
    sex: '公',
    firends: [ { name: '小黄', sex: '母' } ] }
```

```
let dog = {
  name:"小白",
  sex:"公",
  firends:[
    {
      name:"小黄",
      sex:"母"
    }
  ]
}

function deepCopy(obj) {
  if(obj === null) return null
  if(typeof obj !== 'object') return obj
  if(obj instanceof RegExp) return new RegExp(obj)
  if(obj instanceof Date) return new Date(obj)
  let newObj = new obj.constructor
  for(let key in obj){
    newObj[key] = deepCopy(obj[key])
  }
  return newObj
}

let dogcopy = deepCopy(dog)
dog.firends[0].sex = '公'
console.log(dogcopy)
// { name: '小白',
    sex: '公',
    firends: [ { name: '小黄', sex: '母' } ] }
```