# string and array

## 搜索字符串

### match()

- 字符串匹配 找不到返回null
- var str="1 plus 2 equal 3"
- str.match(/\d+/g)   返回['1','2','3'] length = 3
- str.match(/\d+/)和 str.match("1")一样   返回['1', index: 0, input: '1 plus 2 equal 3'] length = 1

### stringObject.replace(regexp/substr,replacement)

- stringObject.replace(regexp/substr,"$1 $& $` $' $$")
- $1 与 regexp 中的第 1 个子表达式相匹配的文本。
- $& 与regexp相匹配的子串
- $` 位于匹配子串左侧的文本
- $' 位于匹配子串右侧的文本
- $$ 直接量符号$

### stringObject.search(regexp)

- 只返回第一个匹配的索引值
- 不执行全局匹配/g
- 找不到返回-1
- 对大小写敏感

### stringObject.indexOf(searchvalue,fromindex)

- searchvalue需检索的字符串值
- fromindex字符串中开始检索的位置
- 只返回第一个匹配的索引值
- 找不到返回-1
- 对大小写敏感
- 可对数组进行检索

### charAt() 返回指定索引位置的字符

### charCodeAt() 返回指定索引位置字符的 Unicode 值

## 数组操作

### arrayObject.slice(start,end)

- 返回一个新的数组副本，包含从 start 到 end （不包括该元素）的 arrayObject 中的元素。
- 可以不传参数，只是单纯的为数组创建并返回一个新数组
- end为可选参数
- 不传参数时对类数组对象调用slice会将其转换成数组

```js
var obj = {
    0: 'prop0',
    1: "prop1",
    2: "prop2",
    3: "prop3",
    length: 4
};
var kkk = Array.prototype.slice.call(obj); // ['prop0','prop1','prop2','prop3']
```

### arrayObject.splice(index,howmany,item1,.....,itemX)

- 从数组中添加/删除项目，改变原数组。
- index为添加/删除项目的位置。
- howmany为删除项目的数量，为0 则不删除项目，且item1,.....,itemX一定在数组索引index位置开始填入。
- 返回被删除的元素组成的数组

```javascript
    fruits.splice(2,0,"Lemon","Kiwi");
    // 从索引值为2的位置（包括2）开始增删
```

### arrayObject.pop() 删除并返回数组的最后一个元素

### arrayObject.push(newelement1,newelement2,....,newelementX) 向数组的末尾添加一个或多个元素，并返回新的长度

### arrayObject.shift() 删除并返回数组的第一个元素

### arrayObject.unshift(newelement1,newelement2,....,newelementX) 向数组的开头添加一个或多个元素，并返回新的长度

### 数组排序

- 直接改变原数组，返回新的数组
- 字母升序（包括纯数字组成的字符串） fruits.sort()
- 数字升序 points.sort( (a, b) => a - b );
- 数字降序 points.sort( (a, b) => b - a );
- 反转排序 arrayObject.reverse()

### 数组去空

```js
function arrayNoEmpty(arr) {
    let target = arr.filter(current => {
        return current !== null && current !== undefined && current !== "";
    });
    return target;
}
```

### 数组去重

- 只适合基础数据类型的数组

```js
function arrayUniq(arr) {
    return [...new Set(arr)];
}
```

## 字符串操作

### stringObject.substr(start,length)

- 在字符串中抽取从 start 下标开始的指定数目的字符。
- length为可选参数

### stringObject.substring(start,stop)

- start和stop均为非负整数
- stop为可选参数
- 返回的子串包括 start 处的字符，stop 处的字符
- start = stop 返回空串
- start > stop 自动交换这2个参数再截取字符串

### stringObject.trim() 移除字符串首尾空白

## 字符串转为数组

### stringObject.split(" ",5)

- 5为规定返回数组的最大长度，可选参数。

## 数组转为字符串

### arrayObject.join(separator)

- 通过指定的分隔符separator进行分隔的。把数组所有项都放入字符串并返回
- separator为可选参数，不写则默认为逗号

## Boolean转化为字符串

### booleanObject.toString() 或者 String(booleanObject) 返回"true"或"false"

## 合并操作

### arrayObject.concat(arrayX,stringX,......,numberX)

- 返回合并的对象且对象类型和objectA一样
- 数组之间的合并为浅拷贝

## 数组遍历

### item, index

- arr.forEach( function  (item, index, arr ) { } [, thisValue] )
  - thisValue为传递给函数的值,一般用 "this" 值。如果这个参数为空， "undefined" 会传递给 "this" 值

- arr.map( function ( item, index, arr ) {  return } )

- $.map( arr, function( item, index ) { return } )

### index, item

- $.each( arr, function( index, item ) { } )

- $('').each( function( index, item ) { } )

- $('').map( function( index, item ) { return } )

### for (var i=0; i<5; i++) for循环

### for (var value of myArray) for of 循环

- for of遍历的只是数组内的元素，而不包括数组的原型属性method和索引name
- value是数组的元素

## 对象遍历

### for (var index in myArray) for in循环

#### 用于遍历数组时

- index索引为string类型数字，不能直接进行几何运算
- 遍历顺序有可能不是按照实际数组的内部顺序
- for in会遍历数组所有的可枚举属性，包括原型

#### 用于遍历对象时

- 遍历对象时，hasOwnProperty方法可以判断某属性是否是该对象的实例属性

```javascript
    for (var key in myObject) {
    　　if(myObject.hasOwnProperty(key)){
    　　　　console.log(key);
    　　}
    }
```

- 对象深拷贝
  - 简单字面量对象的深拷贝可以使用 JSON.parse(JSON.stringify(obj)) 方法进行拷贝 （注意undefined 和 function 会丢失）
  - Objext.assign() 或者extends方法

```js
    function deepCopy(obj) {
        if(obj === null) return null;
        if(typeof obj !== 'object') return obj;
        if(obj.constructor===Date) return new Date(obj);
        var result = Array.isArray(obj) ? [] : {};
        for (var key in obj) {
          if (obj.hasOwnProperty(key)) {
            if (typeof obj[key] === 'object') {
              result[key] = deepCopy(obj[key]);   //递归复制
            } else {
              result[key] = obj[key];
            }
          }
        }
        return result;
    }
```

## Object.keys(myObject)

- 获取对象的实例属性组成的数组，不包括原型方法和属性。

```javascript
    Object.prototype.method=function(){
    　　console.log(this);
    }
    var myObject={
    　　a:1,
    　　b:2,
    　　c:3
    }
    Object.keys(myObject);
    //返回["a", "b", "c"]
```

## 类型

### 5 种不同的数据类型

- string
- number
- boolean
- object
- function

### 3 种对象类型

- Object
- Date
- Array

### 2 个不包含任何值的数据类型

- null
- undefined

### null 和 undefined 和 NaN

- null为对象类型，undefined为undefined类型，NaN为数值类型
- 值相等，类型不相等

### 分别数组和date类型

- js无法通过typeOf来分辨
- constructor 属性 返回所有 JavaScript 变量的构造函数。
  - [1,2,3,4].constructor.toString() 返回 "function Array() { [native code] }" 搜索字段Array即可
- arr instanceof Array 返回Boolean值

## call | apply | bind

### call 和 apply

- 只有传参形式的区别
- `apply` 方法传入两个参数：一个是作为函数上下文的对象，另外一个是作为函数参数所组成的数组

```js
// obj 是作为函数上下文的对象，函数 func 中 this 指向了 obj 这个对象。参数 A 和 B 是放在数组中传入 func 函数，分别对应 func 参数的列表元素
var obj = {
    name : 'study'
}

function func(firstName, lastName){
    console.log(firstName + ' ' + this.name + ' ' + lastName);
}

func.apply(obj, ['A', 'B']);    // A study B
```

- `call` 方法第一个参数也是作为函数上下文的对象，但是后面传入的是一个参数列表，而不是单个数组

```js
var obj = {
    name: 'study'
}

function func(firstName, lastName) {
    console.log(firstName + ' ' + this.name + ' ' + lastName);
}

func.call(obj, 'C', 'D');       // C study D
```

- 如果你的参数本来就存在一个数组中，那自然就用 `apply`，如果参数比较散乱相互之间没什么关联，就用 `call`

#### 用法

##### 改变this指向

- `call` 方法的第一个参数是作为函数上下文的对象，这里把 `obj` 作为参数传给了 `func`，此时函数里的 `this` 便指向了 `obj` 对象

```js
var obj = {
    name: 'study'
}

function func() {
    console.log(this.name);
}

func.call(obj);       // study

// 相当于
function func() {
    console.log(obj.name);
}
```

##### 借用别的对象的方法

- `Array.prototype.slice.call(null,[1,2])` 或者 `[].slice.call(null,[1,2])`

```js
// Person2 实例化出来的对象 person 通过 getname 方法拿到了 Person1 中的 name。因为在 Person2 中，Person1.call(this) 的作用就是使用 Person1 对象代替 this 对象，那么 Person2 就有了 Person1 中的所有属性和方法了，相当于 Person2 继承了 Person1 的属性和方法
var Person1  = function () {
    this.name = 'study';
}
var Person2 = function () {
    this.getname = function () {
        console.log(this.name);
    }
    Person1.call(this);
}
var person = new Person2();
person.getname();       // study
```

##### 调用函数

- `apply`、`call` 方法都会使函数立即执行，因此它们也可以用来调用函数

```js
function func() {
    console.log('study');
}
func.call();            // study
```

#### call与bind的区别

- `bind` 的方法，在低版本的 IE 中不兼容。它和 `call` 很相似，接受的参数有两部分，第一个参数是是作为函数上下文的对象，第二部分参数是个列表，可以接受多个参数。

##### bind 返回值是函数

- `bind` 方法不会立即执行，而是返回一个改变了上下文 `this` 后的函数。而原函数 `func` 中的 `this` 并没有被改变，依旧指向全局对象 `window`

```js
var obj = {
    name: 'study'
}

function func() {
    console.log(this.name);
}

var func1 = func.bind(obj);
func1();                        // study
```

##### 参数的使用

- `call` 是把第二个及以后的参数作为 `func` 方法的实参传进去，而 `func1` 方法的实参实则是在 `bind` 中参数的基础上再往后排

```js
function func(a, b, c) {
    console.log(a, b, c);
}
var func1 = func.bind(null,'study');

func('A', 'B', 'C');            // A B C
func1('A', 'B', 'C');           // study A B
func1('B', 'C');                // study B C
func.call(null, 'study');      // study undefined undefined
```

##### bind方法的手动实现

```js
if (!Function.prototype.bind) {
        Function.prototype.bind = function () {
            var self = this,                        // 保存原函数
                context = [].shift.call(arguments), // 保存需要绑定的this上下文
                args = [].slice.call(arguments);    // 剩余的参数转为数组
            return function () {                    // 返回一个新函数
                self.apply(context,[].concat.call(args, [].slice.call(arguments)));
            }
        }
    }
```
