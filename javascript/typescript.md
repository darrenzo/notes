# TS

- [类型检查工具](https://github.com/litert/type-guard)
- 案例项目
  - [云服务使用的日志库](https://github.com/litert/logger.js)
  - [TLS SNI 库](https://github.com/litert/tls-sni.js)
  - [事件功能封装库](https://github.com/litert/events.js)

## 安装

- npm install -g typescript
- tsc -init    <!-- 初始化生成tsconfig.json文件。需要先配好环境变量 -->
- tsc greeter.ts

## 编译原则

- 当要编译时，一定不能有任何lint提示的错误和警告信息

## 基础类型

- 声明变量时，可在变量名后加上变量值类型限制 let param: type = value
- let username: string = "bob"

### boolean number string

### 数组

```typescript
  let list: number[] = [1, 2, 3];
  let list: Array<number> = [1, 2, 3];
```

### 元组

- 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同
  - 数组元素数量是不固定的

```typescript
    let x: [string, number];
    x = ['hello', 10];
    x = [10, 'hello']; // 报错
    // 访问越界的元素，涉及到联合类型

    let y: [string, number, ...string[]];
    x = ['hello', 10, 'a', 'b', 'c'];
```

```typescript
let sourceArr = [['1', '2'], ['3', '4']] as Array<[string, string]>;
let arr: [string, string][] = [];
for (const [key, val] of sourceArr) {
    arr.push([val, key]);
}
let newArr = new Map([...sourceArr, ...arr]) as Map<string, string>;
// sourceArr 和 arr 必须标记为元组类型而不能是string[][]这样的数组类型，因为Map结构每一项有数量限制
```

### 枚举 enum

- 使用枚举类型可以为一组数值赋予友好的名字

```typescript
    enum Color {Red, Green, Blue}
    let c: Color = Color.Green;

    enum Color {Red = 1, Green, Blue} // 枚举默认从0开始为元素编号，可以手动指定数值,也可以全部都指定
    let colorName: string = Color[2]; // 'Green'
```

### any

- 允许你在编译时可选择地包含或移除类型检查(不能完全清楚数据的类型时)
- 以any类型定义的变量可以赋予任何值，也可以执行任意方法

```typescript
    let notSure: any = 4;
    notSure.toFixed(); // 但是如果是字符串，结果会报错
```

### void

- 没有任何类型
- 只能赋值 undefined 和 null
- 当一个函数没有返回值时，通常返回值类型是void
- 相当于return undefined 或 return null;

```typescript
    function warnUser(): void {
        alert("This is my warning message");
    }
```

### undefined 和 null

- 默认null和undefined是所有类型的子类型，可以赋值给其他类型
- 如果指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自（推荐）

### never

- never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型
- never类型是任何类型的子类型，也可以赋值给任何类型
- 没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never

```typescript
    function error(message: string): never {
        throw new Error(message);
    }
```

### object

- 表示非原始类型, 也就是除number，string，boolean，symbol，null或undefined之外的类型

## 类型断言

- 清楚地知道一个实体具有比它现有类型更确切的类型
- 能解除只读
- 能避开函数参数的额外的属性检查
- 能配合接口使用，例如强行给一个函数使用，断言使用接口的限定的函数类型 （详见本文 混合类型）

```typescript
    let someValue: any = "this is a string";

    let strLength: number = (<string>someValue).length; // <>写法
    let strLength: number = (someValue as string).length; // as写法
    // 两种断言写法都可以，jsx只支持as写法
```

## 重定义及屏蔽

- 在一个嵌套作用域里引入一个新名字的行为称做屏蔽

```typescript
    function sumMatrix(matrix: number[]) {
        let sum = 0;
        for (let i = 0; i < matrix.length; i++) {
            var currentRow = matrix[i];
            for (let i = 0; i < currentRow.length; i++) { // 屏蔽外层的i
                sum += currentRow[i];
            }
        }

        return sum;
    }
```

## 解构赋值

- let {a, b}: {a: string, b: number} = o; 解构赋值时同时限定数据类型

## 默认值

- b?: number 非必须参数

```typescript
    function keepWholeObject(wholeObject: { a: string, b?: number }) {
        let { a, b = 1001 } = wholeObject;
    }
```

### 函数应用

- 函数声明(较少用)

```typescript
    type C = { a: string, b?: number }
    function f({ a, b }: C): void {
        // ...
    }
```

- 函数参数默认值

```typescript
    // 详见es6.md 函数参数的解构赋值
    function f({ a, b } = { a: "", b: 0 }): void {
        // ...
    }
    f(); // ok, default to { a: "", b: 0 }  只为{}这个参数对象设置默认值

    function f({ a, b = 0 } = { a: "" }): void {
        // ...
    }
    f(); // ok, default to {a: ""}, which then defaults b = 0 为{}这个参数对象的b变量设置默认值
```

## ...扩展符

- 展开对象时，只针对对象自身的可枚举属性，所以会丢失对象的方法 ？？？？实测不会。。。。
- 在赋值时，运用对象的扩展运算符，会出现相同属性覆盖问题

## 只读属性

- 创建后便不允许修改

### 变量

- 在属性名前用 readonly来指定只读属性

```typescript
    interface Option {
        readonly x: number;
        readonly y: number;
    }
```

### 对象

- 通过赋值一个对象字面量来构造一个Point

```typescript
    interface Point {
        readonly x: number;
        readonly y: number;
    }
    let p1: Point = { x: 10, y: 20 };
    p1.x = 5; // error!
```

### array

- 设置`ReadonlyArray<type>`类型, 把所有可变方法去掉了

```typescript
    let a: number[] = [1, 2, 3, 4];
    let ro: ReadonlyArray<number> = a;

    ro[0] = 12; // error!
    a = ro; // error! 复制到普通数组也不行
```

### 解除只读

- 类型断言重写  a = ro as number[];

### const 和 readonly

- 作为变量使用的话用 const，若作为属性则使用readonly

## 类型

- 类型是抽象范畴，实际不存在

```typescript
    type AliasNumber = number; // 定义一个number类型的别名
    let i: AliasNumber = 21; // OK

    function aa(x: number, y: number): number {
        return x + y;
    }
    // 定义上述一种此函数类型，仅表示此函数的类型, 常用的有2种写法, 即函数声明的左右2侧的写法
    type IFn = (x: number, y: number) => number; // 通过箭头函数形式表示函数类型

    interface IFn2 {
        (x: number, y: number): number;
    }

    const aaa: IFn = aa;
    const aaac: IFn2 = aa;

    aaa(1, 2);
    aaac(1, 2);
```

## 联合类型

- 例如 number | string | boolean
- 如果一个值是联合类型，我们只能直接访问此联合类型的所有类型里共有的成员。

```typescript
    interface Bird {
        fly();
        layEggs();
    }

    interface Fish {
        swim();
        layEggs();
    }

    function getSmallPet(): Fish | Bird {
        // ...
    }

    let pet = getSmallPet();
    pet.layEggs(); // okay
    pet.swim();    // errors
```

- 当一个联合类型要使用内部方法，却不是所有类型的公有成员时，可以利用类型保护机制判断出具体是属于哪种类型以便正确调用

```typescript
    // 接上部代码
    function isFish(pet: Fish | Bird): pet is Fish {
        return (<Fish>pet).swim !== undefined;
    }

    if (isFish(pet)) {
        pet.swim();
    }
    else {
        pet.fly();
    }

    // pet is Fish 就是类型谓词。 谓词为 parameterName is Type 格式
    // TypeScript不仅知道在 if分支里 pet是 Fish类型， 它还清楚在 else分支里，一定 不是 Fish类型，一定是 Bird类型
```

- 当一个对象中各属性返回不同类型的值，随机对这个对象中某个属性赋值有可能会报错(在vetur插件中)

```typescript
interface A {
    aa: number;
    bb: boolean;
}
let a: A = {
    aa: 1,
    bb: false
};

let b: A = {
    aa: 2,
    bb: true
};

let arr: Array<'aa' | 'bb'> = ['aa', 'bb'];

for (const item of arr) {
    //  此时 a[item] 出现错误下曲线
    // 等号左侧的值类型一定是确定的，解析为 boolean 或者解析为 number, 但等号右侧的值类型却是 boolean | number 这种非确定的
    a[item] = b[item];
}

// 解决办法1
a[item] = b[item] 改为 (a as any)[item] = b[item]; // 跳过类型检查

// 解决办法2
a[item] = b[item] 改为
(<K extends keyof A(k: K, v: A[K]) => {

    a[k] = v; // 通过参数明确了等号左右的值类型一定是正确对应的

})(item, b[item]);
```

- typeof 类型保护 可以直接使用判断类型    typeof padding === "number" 返回Boolean
- instanceof 类型保护
  - 类A和类B都实现了接口C，变量c: C， 所以c也就是是 A | B联合类型，可以用instanceof判断具体是哪个 c instanceof A 返回Boolean

## 类型收敛

```typescript
   let c: a; // a 为接口，c不能包含除a定义以外的东西
   let d: b;  // b为类， d就是b的一个实例，不能包含b定义以外的东西
   let e: new() => A; // A为类，e相当于A的一个子类，包含A所应有的东西，也可以拥有除A以外的东西
```

```typescript
    interface X {
        a: number;
    }

    interface Y extends X {
        y: number;
    }

    let xxx: X = { a: 213 };

    let yyy: Y = { a: 23, y: 666 };

    xxx = yyy;
    yyy = xxx; // error， 不能把小范围的赋值给大范围的，因为可能找不到y报错 ？？？？？为什么never类型可以赋值给所有类型
```

## 比较两个函数

```typescript
    let x = (a: number) => 0;
    let y = (b: number, s: string) => 0;

    y = x; // OK  x的每个参数的类型在y中都能找到对应的参数类型，所以允许赋值
    x = y; // Error
```

## 接口

- 接口： 规定所有class所必须的属性和方法，并规定属性的类型，属于纯抽象范畴，仅起限定作用
- class:  定义所有实例所能参考复用的模板 属于半抽象范畴，除了readonly可以声明初始化变量值（基本不用）和static成员初始化，类内不允许出现初始化变量的情况
- 接口和class的区别： 接口只声明成员方法，不做实现。 类声明并实现方法。

```typescript
    interface SquareConfig {
        color?: string;
        width?: number;
    }

    function createSquare(config: SquareConfig): {color: string; area: number} { // {color: string; area: number}相当于返回值的类型，检查函数的返回值，推荐都写成interface
        let newSquare = {color: "white", area: 100};
        if (config.color) {
            newSquare.color = config.color;
        }
        if (config.width) {
            newSquare.area = config.width * config.width;
        }
        return newSquare;
    }

    let mySquare = createSquare({color: "black"});
```

### 额外的属性检查 (尽量不使用这些方法绕过检查)

- 对象字面量会被特殊对待而且会经过额外属性检查，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误

```typescript
    let mySquare = createSquare({ colour: "red", width: 100 });
    // error: 'colour' not expected in type 'SquareConfig'
```

- 绕开检查的方法

- 最简单的就是使用类型断言

```typescript
    let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
```

- 最佳的方式是能够添加一个字符串索引签名，前提是你能够确定这个对象可能具有某些做为特殊用途使用的额外属性

```typescript
    interface SquareConfig {
        color?: string;
        width?: number;
        [propName: string]: any;  // 任意数量的其它属性 只要它们不是color和width，那么就无所谓它们的类型是什么
    }
```

- 将这个参数对象赋值给另一个变量再传入

```typescript
    let squareOptions = { colour: "red", width: 100 };
    let mySquare = createSquare(squareOptions); // squareOptions不会经过额外属性检查，所以编译器不会报错
```

### 函数类型

- 只有参数列表和返回值类型的函数定义

```typescript
    interface SearchFunc {
        (source: string, subString: string): boolean;
    }

    // 对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配
    let mySearch: SearchFunc;
    mySearch = function(src: string, sub: string): boolean { // 如果不指定类型，ts会自动推断
        let result = src.search(sub);
        return result > -1;
    }
```

### 可索引的类型

- 可索引类型具有一个 索引签名，它描述了对象索引的类型，还有相应的索引返回值类型
- 支持两种索引签名：字符串和数字。可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型
- 原理：当使用 number来索引时，JavaScript会将它转换成string然后再去索引对象

```typescript
    interface StringArray {
        [index: number]: string;  // 索引类型是number(都会自动转换为字符串，不根据这里检查)，返回值类型是string
    }

    let myArray: StringArray;
    myArray = ["Bob", "Fred"];

    let myStr: string = myArray[0];

    interface NumberDictionary {
        [index: string]: number; // 一旦定义了这行，则字典里所有的返回值都必须是定义的（此处number）
        length: number;    // 可以，length是number类型
        123: number;        // 同上 123会转换成‘123’
        name: string       // 错误，`name`的类型与索引类型返回值的类型不匹配
    }

```

- 索引签名设置为只读， 防止给索引赋值

```typescript
    interface ReadonlyStringArray {
        readonly [index: number]: string;
    }
    let myArray: ReadonlyStringArray = ["Alice", "Bob"];
    myArray[2] = "Mallory"; // error!
```

### 实现接口

- 接口描述了类的公共部分，而不是公共和私有两部分，因为不同的类实现同一个接口时，必须实现接口内所有的属性和方法

```typescript
    interface ClockInterface {
        currentTime: Date;
        setTime(d: Date): void;
    }

    class Clock implements ClockInterface {
        currentTime: Date; // 必须
        setTime(d: Date): void { // 必须
            this.currentTime = d;
        }
        constructor(theTime: Date) { this.currentTime = theTime }
    }
```

### 类 静态部分和实例部分的区别

- 类具有两个类型的：静态部分（只有static）的类型和实例的类型
- 静态部分即特别用static声明的部分和constructor构造函数，静态部分不能被new实例化，不能直接用this获取。static访问只能用类本身，可以在类内部，或者在类外部
- static private protected 都可以被继承，但是访问会被限制，各有不同
- static成员可以保留其值(因为开辟了他自己内部的空间)
- 当一个类实现了一个接口时，只对其实例部分进行类型检查。 constructor构造函数存在于类的静态部分(构造函数其实就是class本身)，所以不在检查的范围内
- 静态方法只能调用静态属性

```typescript  
    let sees: Employee; //  声明 sees 是类Employee的一种实例
    let sees1: typeof Employee; // 声明sees1是 类Employee的类型
    let sees2: typeof Employee = Employee;  

    interface IPoint {

        x: number;

        y: number;
    }

    interface IPointFactory {

        new (x?: number, y?: number): IPoint; // 不用new，就是指函数了，用new就表示的是类
    }

    let ip: IPointFactory; // 指定 ip是指向某个类类型的类或者实现某个接口的类类型的类 (不是实例，实例就是把类里的所有方法和属性列在interface里)




    interface ClockConstructor {
        new (hour: number, minute: number): ClockInterface;
    }

    interface ClockInterface {
        tick(): void;
    }

    class DigitalClock implements ClockInterface {
        constructor(h: number, m: number) { }
        tick() {
            console.log("beep beep");
        }
    }
    class AnalogClock implements ClockInterface {
        constructor(h: number, m: number) { }
        tick() {
            console.log("tick tock");
        }
    }

    function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
        return new ctor(hour, minute);
    }

    let digital = createClock(DigitalClock, 12, 17);
    let analog = createClock(AnalogClock, 7, 32);
```

### 把接口当做类使用(一般不这样用)

- 类定义会创建 类的实例类型和一个构造函数
- 类可以创建出类型，所以接口就能当做类使用

```typescript
    class Point {
        x: number;
        y: number;
    }

    interface Point3d extends Point { // 不会继承类的实现
        z: number;
    }
    // 当前接口 Point3d内规定了x , y , z 三个变量的类型
    let point3d: Point3d = {x: 1, y: 2, z: 3}; // 与实现时的要求一样
```

### 继承接口

```typescript
    interface Shape {
        color: string;
    }

    interface PenStroke {
        penWidth: number;
    }

    // 同时继承多个的时候，不能使用类似于 Shape['aaa'] 的写法，所要继承的类型必须是单独明确定义的。可以使用 & 符号合并两个类型，且支持Shape['aaa']的写法
    interface Square extends Shape, PenStroke { // 同时继承多个
        sideLength: number;
    }

    let square = {} as Square;
    square.color = "blue";
    square.sideLength = 10;
    square.penWidth = 5.0;
```

### 混合类型

```typescript
    interface Counter {
        (start: number): string;
        interval: number;
        reset(): void; // 方法尽量在类定义中写
    }

    function getCounter(): Counter {
        let counter = <Counter>function (start: number) { }; // 强行使用接口第一条，剩余两条额外声明  这里相当于创建构造函数即couter类，一个Function类型  这个在ts里不能使用 new
        counter.interval = 123; // 给couter这个构造函数创建static属性
        counter.reset = function () { }; // 给counter这个构造函数创建静态方法reset()
        return counter; // 返回构造函数
    }

    let c = getCounter();
    console.log(c); // { [Function: counter] interval: 123, reset: [Function] }
    c(10); // 给公有属性start赋值
    c.reset(); // 执行静态方法
    c.interval = 5.0; // 给静态属性重赋值
```

### 接口继承类 （基本没人这样写）

- 当接口继承了一个类类型时，它会继承类的成员但不包括其实现
- 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现

```typescript
    class Control {
        private state: any;
    }

    interface SelectableControl extends Control {
        select(): void;
    }

    class Button extends Control implements SelectableControl {
        select() { }
    }

    class TextBox extends Control {
        select() { }
    }

    // 错误：“Image”类型缺少“state”属性。
    class Image implements SelectableControl {
        select() { }
    }
```

## 继承

- 派生类通常被称作子类，基类通常被称作超类
- 构造函数里访问父级this的属性之前，一定要调用 super()

```typescript
    class Animal {
        name: string;
        constructor(theName: string) { this.name = theName; }
        move(distanceInMeters: number = 0): void {
            console.log(`${this.name} moved ${distanceInMeters}m.`);
        }
    }

    class Snake extends Animal {
        age: number;
        constructor(name: string, theAge: number) { super(name); this.age = theAge} // 必须调用 super()方法，向基类的构造函数传参并执行
        move(distanceInMeters = 5): void {  // 重写move()
            console.log("Slithering...");
            super.move(distanceInMeters); // 只能写在子类的方法里
        }

        print(): void {
            console.log(1234);
        }
    }

    class Horse extends Animal {
        constructor(name: string) { super(name); }
        move(distanceInMeters = 45) { // 重写move()
            console.log("Galloping...");
            super.move(distanceInMeters);
        }
        printName(): void {
            console.log(2323);
        }
    }

    let sam = new Snake("Sammy the Python", 123);
    let tom: Animal = new Horse("Tommy the Palomino");

    sam.move();
    sam.print();
    sam.age;
    tom.move(34); // 即使 tom被声明为 Animal类型，但因为它的值是 Horse，调用 tom.move(34)时，它会调用 Horse里重写的方法
    tom.printName(); // error, 因为tom引用的是Animal的类型,所以只有Animal类里声明的方法，属性也是一样
```

## 修饰符

### 默认为 public可见

### private 私有   只能在本类内部中使用

```typescript
    class Animal {
        private name: string;
        constructor(theName: string) { this.name = theName; }
    }

    new Animal("Cat").name; // 错误: 'name' 是私有的. 只能在Animal类内部才能访问
```

### protected

- 与private类似，但是protected成员可以在本类及其子类中访问（通过实例方法）

```typescript
    class Person {
        protected name: string;
        constructor(name: string) { this.name = name; }
    }

    class Employee extends Person {
        private department: string;

        constructor(name: string, department: string) {
            super(name)
            this.department = department;
        }

        public getElevatorPitch() {
            return `Hello, my name is ${this.name} and I work in ${this.department}.`;
        }
    }

    let howard = new Employee("Howard", "Sales");
    console.log(howard.getElevatorPitch());
    console.log(howard.name); // 错误
```

- 构造函数标记 protected，意味着这个类不能在包含它的类外被实例化，但是能被继承

```typescript
    class Person {
        protected name: string;
        protected constructor(theName: string) { this.name = theName; }
    }

    // Employee 能够继承 Person
    class Employee extends Person {
        private department: string;

        constructor(name: string, department: string) {
            super(name);
            this.department = department;
        }

        public getElevatorPitch() {
            return `Hello, my name is ${this.name} and I work in ${this.department}.`;
        }
    }

    let howard = new Employee("Howard", "Sales");
    let john = new Person("John"); // 错误: 'Person' 的构造函数是被保护的.
```

### readonly修饰符

- 只读属性必须在声明时或构造函数里被初始化。

```typescript
    class Octopus {
        readonly name: string;
        readonly numberOfLegs: number = 8;
        constructor (theName: string) {
            this.name = theName;
        }
    }
    let dad = new Octopus("Man with the 8 strong legs");
    dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
```

## 参数属性

```typescript
    class Animal {
        constructor(private name: string) { } // 同适用其他的修饰符
        //  private name: string;
        //  constructor(theName: string) { this.name = theName; }
    }
```

## 存取器

- 只带有 get不带有 set的存取器自动被推断为 readonly

```typescript
    let passcode = "secret passcode";

    class Employee {
        private _fullName: string;

        get fullName(): string {  
            return this._fullName;
        }

        set fullName(newName: string) {
            if (passcode && passcode == "secret passcode") {
                this._fullName = newName;
            }
            else {
                console.log("Error: Unauthorized update of employee!");
            }
        }
    }

    let employee = new Employee();
    employee.fullName = "Bob Smith"; // set
    if (employee.fullName) { // get
        alert(employee.fullName);
    }
```

## static属性

- 直接用类名访问static属性，而不用this

```typescript
    class Grid {
        static origin = {x: 0, y: 0};
        scale: number;
        constructor(scale: number) {this.scale = scale};
        calculateDistanceFromOrigin(point: {x: number; y: number;}) {
            let xDist = (point.x - Grid.origin.x);
            let yDist = (point.y - Grid.origin.y);
            return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
        }
    }
```

## 抽象类 abstract

- 抽象类作为其它派生类的基类使用，一般不会直接被实例化
- 抽象类可以包含成员的实现细节
- abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法
- 抽象类中的抽象方法不包含具体实现并且必须在派生类中实现
- 抽象方法必须包含 abstract关键字并且可以包含访问修饰符

```typescript
    abstract class Department {

        constructor(public name: string) {
        }

        printName(): void {
            console.log('Department name: ' + this.name);
        }

        abstract printMeeting(): void; // 必须在派生类中实现
    }

    class AccountingDepartment extends Department {

        constructor() {
            super('Accounting and Auditing'); // 在派生类的构造函数中必须调用 super()
        }

        printMeeting(): void {
            console.log('The Accounting Department meets each Monday at 10am.');
        }

        generateReports(): void {
            console.log('Generating accounting reports...');
        }
    }

    let department: Department; // 允许创建一个对抽象类型的引用 ？？？？？？？这种声明不就是值department是Department的实例么？
    department = new Department(); // 错误: 不能创建一个抽象类的实例
    department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
    department.printName();
    department.printMeeting();
    department.generateReports(); // 错误: 方法在声明的抽象类中不存在，因为声明的时候，department是对Department抽象类的引用
```

## 函数

- js里，函数也是对象， 是 Function 的实例

```javascript
    function person(){ // 把 person 当作构造函数 即类
        this.name = 'Tom'; // 创建person类的公有属性name，相当于在原型上声明了一个属性，不赋值（初始化）即不存在
    }

    const demo = new person(); // 创建demo实例；

    const demo1 = new Function(...paramString[], "要执行的代码"); // 创建demo1函数实例

    person.age = 13; // 创建 person类的static属性age

    person.reset() = function(){}; // 创建静态方法

    person.prototype.reset() = function(){}; // 创建公共方法
```

```typescript
    let myAdd: (x: number, y: number) => number = function(x: number, y: number): number { return x + y; }; // 完整写法
    // 箭头函数可以只写一侧的类型，另一侧自动推断出来，对应位置。 叫 “按上下文归类”
    let myAdd: (baseValue: number, increment: number) => number = function(x, y) { return x + y; };
    // 或
    let myAdd = function(x: number, y: number): number { return x + y; }; // 推荐
```

### 默认值参数

- 默认值参数放在必须参数前，则必须传入undefined来获取默认值

### 剩余参数

```typescript
    function buildName(firstName: string, ...restOfName: string[]): string {}
    // 剩余参数restOfName是个数组


    function buildName(firstName: string, ...restOfName: string[]): string {
        return firstName + " " + restOfName.join(" ");
    }

    let buildNameFun: (fname: string, ...rest: string[]) => string = buildName; //一定要有一侧已经声明了参数类型
```

### this和箭头函数

```javascript
    let deck = {
            suits: ["hearts", "spades", "clubs", "diamonds"],
            cards: Array(52),
            createCardPicker: function() {
                return function() {
                    let pickedCard = Math.floor(Math.random() * 52);
                    let pickedSuit = Math.floor(pickedCard / 13);

                    return {suit: this.suits[pickedSuit], card: pickedCard % 13};
                }
            }
        }

    let cardPicker = deck.createCardPicker();
    let pickedCard = cardPicker();

    alert("card: " + pickedCard.card + " of " + pickedCard.suit);
    // this只有在被调用的时候才会指定 此处会指向window  严格模式下指向undefined
```

```typescript
    // 需要用箭头函数绑定this 由于this来自对象字面量里的函数表达式，得提供一个显式的this参数
    // 为了类型重用清晰简单，所以添加Card和Deck接口

    interface Card {
        suit: string;
        card: number;
    }
    interface Deck {
        suits: string[];
        cards: number[];
        createCardPicker(this: Deck): () => Card;
    }
    let deck: Deck = {
            suits: ["hearts", "spades", "clubs", "diamonds"],
            cards: Array(52),
            createCardPicker(this: Deck) {
                return () => {
                    let pickedCard = Math.floor(Math.random() * 52);
                    let pickedSuit = Math.floor(pickedCard / 13);

                    return {suit: this.suits[pickedSuit], card: pickedCard % 13};
                }
            }
        }

    let cardPicker = deck.createCardPicker();
    let pickedCard = cardPicker();

    alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

### 重载

- 查找重载列表，从上往下试，所以要把最精准的放前

```typescript
    let suits = ["hearts", "spades", "clubs", "diamonds"];

    function pickCard(x: {suit: string; card: number; }[]): number; // 传入对象参数 ？？？？？为什么加个[]
    function pickCard(x: number): {suit: string; card: number; }; // 传入number

    function pickCard(x: any): any {
        // Check to see if we're working with an object/array
        // if so, they gave us the deck and we'll pick the card
        if (typeof x == "object") {
            let pickedCard = Math.floor(Math.random() * x.length);
            return pickedCard;
        }
        // Otherwise just let them pick the card
        else if (typeof x == "number") {
            let pickedSuit = Math.floor(x / 13);
            return { suit: suits[pickedSuit], card: x % 13 };
        }
    }
```

## 泛型

- 可以使用泛型来创建可重用的组件，一个组件可以支持多种类型的数据

### 泛型变量

- 类型变量 T 只用于表示类型而不是值
- 可以使用别的字母代表类型变量

```typescript
    function identity<T>(arg: T): T {  // 当设置某个T类型时，返回的依旧只能是T这个类型
        return arg;
    }

    let output = identity<string>("myString"); // 返回只能是string
    let output = identity("myString"); // 编辑器会进行类型推论，查看arg参数类型，自行推断返回的类型，但是复杂的情况下可能不能推断出来
```

- T类型变量可以是任何类型或者值，所以不能直接对其添加方法

```typescript
    function loggingIdentity<T>(arg: T): T {
        console.log(arg.length);  // Error: T doesn't have .length
        return arg;
    }

    function loggingIdentity<T>(arg: T[]): T[] { // 把泛型范围缩小
        console.log(arg.length);  // Array has a .length, so no more error
        return arg;
    }
```

- T类型变量的可能值

```typescript
function loggingIdentity<T extends string[] | number>(arg: T): T {
    return arg;
}
```

- T类型变量的默认值

```typescript
function loggingIdentity<T extends string[] | number = number>(arg: T): T {
    return arg;
}
```

### 泛型函数

```typescript
    function identity<T>(arg: T): T {
        return arg;
    }

    let myIdentity: <T>(arg: T) => T = identity;
    // let myIdentity: <U>(arg: U) => U = identity; 也可
```

### 泛型接口

```typescript
    interface GenericIdentityFn {
        <T>(arg: T): T;
    }

    function identity<T>(arg: T): T {
        return arg;
    }

    let myIdentity: GenericIdentityFn = identity;

    // 把泛型参数当作整个接口的一个参数,就能清楚的知道使用的具体是哪个泛型类型
    interface GenericIdentityFn<T> {
        (arg: T): T;
    }

    function identity<T>(arg: T): T {
        return arg;
    }

    let myIdentity: GenericIdentityFn<number> = identity;
```

### 泛型类

- 类有两部分：静态部分和实例部分。 泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型  ?????????

```typescript
    // ?????????
    class GenericNumber<T> {
        zeroValue: T;
        add: (x: T, y: T) => T;
    }

    let myGenericNumber = new GenericNumber<number>();
    myGenericNumber.zeroValue = 0;
    myGenericNumber.add = function(x, y) { return x + y; };
```

### 无法创建泛型枚举和泛型命名空间

### 泛型约束

```typescript
    interface Lengthwise {
        length: number;
    }

    function loggingIdentity<T extends Lengthwise>(arg: T): T {
        console.log(arg.length);  // Now we know it has a .length property, so no more error
        return arg;
    }
    loggingIdentity({length: 10, value: 3});
```

### 在泛型约束中使用类型参数

- 声明一个类型参数，且它被另一个类型参数所约束

```typescript
    // 用属性名从对象里获取这个属性。 并且我们想要确保这个属性存在于对象 obj上，因此我们需要在这两个类型之间使用约束
    function getProperty(obj: T, key: K) {
        return obj[key];
    }

    let x = { a: 1, b: 2, c: 3, d: 4 };

    getProperty(x, "a"); // okay
    getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

### 在泛型里使用类类型

```typescript
    // 使用泛型创建工厂函数时，需要引用构造函数的类类型
    function create<T>(c: {new(): T; }): T {
        return new c();
    }

    // 使用原型属性推断并约束构造函数与类实例的关系
    class BeeKeeper {
        hasMask!: boolean; // 可以不写明构造函数的参数，表示在未来的某刻赋值，不赋值就是undefined
    }

    class ZooKeeper {
        nametag!: string;
    }

    class Animal {
        numLegs!: number;
    }

    class Bee extends Animal {
        keeper!: BeeKeeper;
    }

    class Lion extends Animal {
        keeper!: ZooKeeper;
    }

    function createInstance<A extends Animal>(c: new () => A): A {
        return new c();
    }

    createInstance(Lion).keeper.nametag;  // typechecks!
    createInstance(Bee).keeper.hasMask;   // typechecks!
```

## 枚举

- 基本只是用来列出有限的一堆数据，而且不允许修改，多用const枚举
- 一个枚举类型可以包含零个或多个枚举成员,
- 不会为字符串枚举成员生成反向映射，而且该成员后，成员值不能自动递增。尽量避免为枚举成员赋值字符串类型
- 尽量不要手动为枚举成员赋值，会出现值相同的情况
- 第一项可以赋值为1，避免0这个假值在判断中出现的boolean错误
- 枚举类型可以对同名枚举类型进行扩展合并，所以要注意属性名的选取， ts会提示错误，扩展的枚举类型第一个值需要手动赋值

- 枚举是运行时真正存在的对象

```typescript
    enum E {
        X, Y, Z
    }

    function f(obj: { X: number }) {
        return obj.X;
    }

    f(E);


    enum Response {
        No = 0,
        Yes = 1,
    }

    function respond(recipient: string, message: Response): void {
        // ...
    }

    respond("Princess Caroline", Response.Yes)
```

- 枚举成员被当作是常量的情况

  - 它是枚举的第一个成员且没有初始化器，这种情况下它被赋予值 0

  - 当枚举成员不带有初始化器且它之前的枚举成员是一个数字常量，这个成员的值为它上一个枚举成员的值+1

    ```typescript
        enum Response {
            A = 1, B, C
        }
        // Response["B"]为 2
    ```

  - 枚举成员使用常量枚举表达式初始化
    - 常量表达式：
      - 一个枚举表达式字面量（主要是字符串字面量或数字字面量）
      - 一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的，此时需要加上限定前缀）
      - 带括号的常量枚举表达式 ？？？？
      - 一元运算符 +, -, ~其中之一应用在了常量枚举表达式
      - 常量枚举表达式做为二元运算符 +, -, *, /, %, <<, >>, >>>, &, |, ^的操作对象。 若常数枚举表达式求值后为 NaN或 Infinity，则会在编译阶段报错。

    ```typescript
        enum FileAccess {
            // constant members
            None,
            Read    = 1 << 1,
            Write   = 1 << 2,
            ReadWrite  = Read * Write,
            // computed member  ？？？？
            G = "123".length
        }
    ```

### 数字枚举

```typescript
    enum Direction {
        Up,
        Down,
        Left,
        Right
    }
```

### 字符串枚举（不推荐）

```typescript
    enum Direction {
        Up = "UP",
        Down = "DOWN",
        Left = "LEFT",
        Right = "RIGHT",
    }
```

### 异构枚举（基本不用）

```typescript
    enum BooleanLikeHeterogeneousEnum {
        No = 0,
        Yes = "YES",
    }
```

### const常量枚举

- 常量枚举只能使用常量枚举表达式
- 在编译阶段会被删除
- 常量枚举成员在使用的地方会被内联进来，因为常量枚举不允许包含计算成员 ？？？

```typescript
    const enum Directions {
        Up,
        Down,
        Left,
        Right
    }

    let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]

    // 编译后
    var directions = [0, 1, 2, 3];
```

### declare外部枚举 ？？？？ declare其他地方的用法？？？？

- 只是用来声明一个枚举，以便引用
- 非外部枚举(正常的枚举)里，没有初始化方法的成员被当成常数成员
- 非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的

```typescript
    declare enum Enum {
        A = 1,
        B,
        C = 2
    }

    let a = Enum.A;

    // 编译后
    var a = Enum.A;
```

## Symbols

- Symbols是不可改变且唯一的

```typescript
    let sym2 = Symbol("key");
    let sym3 = Symbol("key");

    sym2 === sym3; // false, symbols是唯一的
```

- 像字符串一样，symbols也可以被用做对象属性的键

```typescript
    let sym = Symbol(); //  ts提示错误

    let obj = {
        [sym]: "value"
    };

    console.log(obj[sym]); // "value"
```

## 高级类型

### 数字字面量类型和字符串字面量类型

```typescript
    // 运用在重载
    interface IAAA {
        (a: "a"): number;

        (a: "b"): string;
    }

    const aaa: IAAA = function(a: "a" | "b"): any {

        return a === "a" ? 123 : "123";
    }

    const a1 = aaa("a");

    const a2 = aaa("b");
```

## 模块

- 任何包含顶级import或者export的文件都被当成一个模块。相反地，如果一个文件不带有顶级的import或者export声明，那么它的内容被视为全局可见的（因此对模块也是可见的）
- “内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”
- module X 相当于 现在的 namespace X

### 导出

- 导出声明

```typescript
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
```

- 导出语句

```typescript
    class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
    export { ZipCodeValidator };
    export { ZipCodeValidator as mainValidator };
```

- 重新导出

```typescript
    class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
    export { ZipCodeValidator };
    export { ZipCodeValidator as mainValidator };
    // 一个模块可以包裹多个模块，并把他们导出的内容联合在一起通过语法：export * from "module"
    export * from "./StringValidator"; // exports interface StringValidator
    export * from "./LettersOnlyValidator"; // exports class LettersOnlyValidator
    export * from "./ZipCodeValidator";  // exports class ZipCodeValidator

    // 别的文件引入时就可以引入上面扩展后的完整模块了
```

### 导入

- 导入一个模块中的某个导出内容

```typescript
    import { ZipCodeValidator } from "./ZipCodeValidator";
    let myValidator = new ZipCodeValidator();

    import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
    let myValidator = new ZCV();
```

- 将整个模块导入到一个变量，并通过它来访问模块的导出部分

```typescript
    import * as validator from "./ZipCodeValidator";
    let myValidator = new validator.ZipCodeValidator();
```

- 具有副作用的导入模块 （不推荐）
  - 一些模块会设置一些全局状态供其它模块使用。 这些模块可能没有任何的导出或用户根本就不关注它的导出

  ```typescript
    import "./my-module.js"; // 没有设置名字怎么引用执行
  ```

### 默认导出

- 一个模块只能够有一个default导出  default导出也可以是一个值

```typescript
    declare let $: JQuery;
    export default $;
```

- 类和函数声明可以直接被标记为默认导出。 标记为默认导出的类和函数的名字是可以省略的。

```typescript
    const numberRegexp = /^[0-9]+$/;

    export default function (s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }

    // 引入文件
    import validate from "./StaticZipCodeValidator";
```

### export = module 和 import module = require("module")  ？？？？？

- CommonJS和AMD都有一个exports对象的概念，它包含了一个模块的所有导出内容。
- export = 语法定义一个模块的导出对象。 它可以是类，接口，命名空间，函数或枚举
- 若要导入一个使用了export =的模块时，必须使用TypeScript提供的特定语法import module = require("module")。

```typescript
    class ZipCodeValidator {
    }
    export = ZipCodeValidator;

    // 引入文件
    import zip = require("./ZipCodeValidator");
    let validator = new zip();
```

### 可选的模块加载和其它高级加载场景 ？？？？

### 外部模块

- .d.ts集中声明文件(类似于头文件) 于js，ts放在一起
- 当有外部文件引用该项目时，js中变量的类型就是通过头文件判断的
- 所有变量的类型都放在头文件中，在ts编译成js文件时，会同时生成一个头文件
- tsconfig.json设置中
  - "declaration": true,  开启自动生成头文件
  - "declarationMap": true,  开启头文件对应表
  - "sourceMap": true,  开启调试对应表

```typescript
    // 外部模块
    declare module "url" {
        export interface Url {
            protocol?: string;
            hostname?: string;
            pathname?: string;
        }

        export function parse(urlStr: string): Url;
    }

    declare module "path" {
        export function normalize(p: string): string;
        export function join(...paths: any[]): string;
        export let sep: string;
    }

    // 引用模块

    /// <reference path="node.d.ts"/>
    import * as URL from "url";
    let myUrl = URL.parse("http://www.typescriptlang.org");
```

- 外部模块简写

- 简写模块里所有导出的类型将是any

```typescript
    declare module "hot-new-module";

    // 引入文件
    import x, {y} from "hot-new-module";
    x(y);
```

## 命名空间

```typescript
    namespace Validation {
        export interface aaa{}
        let a: aaa;
    }
```

- 别名 import

```typescript
    import q = x.y.z // 为指定的符号创建一个别名。 你可以用这种方法为任意标识符创建别名，也包括导入的模块中的对象。

    import polygons = Shapes.Polygons
```

<!-- TODO 待续 -->
## Mixins

## 装饰器
