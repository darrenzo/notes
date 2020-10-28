# less (3.8.1)

## Variables

```less
    // 选择器变量
    @Wrap: #wrap;
    @{Wrap}{  
        color:#ccc;
    }

    // 属性变量
    @images: "/img";//需要加引号 ----变量一般不加引号，否则输出值也会带引号
    body {
        background: url("@{images}/dog.png");//变量名 必须使用大括号包裹
    }

    // @声明变量必须要有变量名后的 : 号  与  结尾的 ; 号
    @background: {
        background:red;
    };
    #main{
        @background();
    }
```

## Mixins

```less
    // 规则集 推荐 . 带 ( ) 写法
    .bordered() { //也可以省略()写成#bordered或者.bordered，但是这两种写法会把规则集当做样式解析到css里
        border-top: dotted 1px black;
        border-bottom: solid 2px black;
    }
    #menu a {
        color: #111;
        .bordered();//或者.bordered; 推荐()
    }

    .border(@a:10px,@b:50px,@c:30px,@color:#000){ //如果 没有传参数，那么将使用默认参数。
        border:solid 1px @color;
        box-shadow: @arguments;//指代的是 全部参数
    }
    #wrap{
        .border(0px);
    }
    #content{
        .border();//等价于 .border，推荐()
    }
```

## 匹配模式(类似多态)

```less
    .triangle(bottom,@width:20px,@color:#000){
          border-color:@color transparent  transparent  transparent ;
    }
    .triangle(left,@width:20px,@color:#000){
        border-color:transparent  transparent  transparent @color;
    }
    .triangle(@_,@width:20px,@color:#000){
        border-style: solid;
        border-width: @width;
    }
    #main{
        .triangle(left, 50px, #999)
    }
    // output
    #main{
        border-color:transparent  transparent  transparent #999;
        border-style: solid;
        border-width: 50px;
    }
```

- 第一个参数 `left` 会找到方法中匹配程度最高的，如果匹配程度相同，将全部选择，并存在着样式覆盖替换(按从上往下的匹配顺序编译到css中)
- 如果匹配的参数是变量，则将会匹配，如 `@_` 。

## 数量不定的参数

```less
    .boxShadow(...){  // 类似于es6的扩展运算符
        box-shadow: @arguments;
    }
    .textShadow(@a,...){
        text-shadow: @arguments;
    }

    #main{
        .boxShadow(1px,4px,30px,red);
        .textShadow(1px,4px,30px,red);
    }
```

## 方法的 !important

- 会给该方法的每个属性加上 !important

```less
    .border{
        border: solid 1px red;
        margin: 50px;
    }
    #main{
        .border() !important;
    }
    /* 生成后的 CSS */
    #main {
        border: solid 1px red !important;
        margin: 50px !important;
    }
```

## Nesting

```less
    #header {
        color: black;
        .navigation {
            font-size: 12px;
        }
        &:after { //&代表父元素#header
            content: " ";
        }
    }
```

### Nested Directives and Bubbling (at-rule)

```less
    //@Media, @supports and @document
    .screen-color {
        @media screen {
            color: green;
            @media (min-width: 768px) { // 可以省略screen,用外层的。 (min-width: 768px)最好提成变量，不然会出现标红提示，但不影响编译
                color: red;
            }
        }
        @media tv {
            color: black;
        }
    }
    //outputs
    @media screen {
        .screen-color {
            color: green;
        }
    }
    @media screen and (min-width: 768px) {
        .screen-color {
            color: red;
        }
    }
    @media tv {
        .screen-color {
            color: black;
        }
    }

    //@font-face or @keyframes
    #a {
        color: blue;
        @font-face {
            src: made-up-url;
        }
        padding: 2 2 2 2;
    }
    //outputs
    #a {
        color: blue;
    }
    @font-face {
        src: made-up-url;
    }
    #a {
        padding: 2 2 2 2;
    }
```

## 属性拼接

- +代表逗号

```less
    .boxShadow() {
        box-shadow+: inset 0 0 10px #555;  // 属性键带 + ,不进行拼接也不会报错
    }
    .main {
        .boxShadow();
        box-shadow+: 0 0 20px black;
    }
    // output
    .main {
        box-shadow: inset 0 0 10px #555, 0 0 20px black;
    }
```

- +_代表空格

```less
    .Animation() {
        transform+_: scale(2); // 属性键带 +_ ,不进行拼接也不会报错
    }
    .main {
        .Animation();
        transform+_: rotate(15deg);
    }
    // output
    .main {
        transform: scale(2) rotate(15deg);
    }
```

## Operations

- 加减法时 以第一个数据的单位为基准进行转化
- 乘除法时 注意单位一定要统一
- 单位无法转化的，则忽略该单位
- 运算符左右必须要有空格

```less
    // numbers are converted into the same units
    @conversion-1: 5cm + 10mm; // result is 6cm
    @conversion-2: 2 - 3cm - 5mm; // result is -1.5cm

    // conversion is impossible
    @incompatible-units: 2 + 5px - 3cm; // result is 4px

    // example with variables
    @base: 5%;
    @filler: @base * 2; // result is 10%
    @other: @base + @filler; // result is 15%

    html {
        font-size: e("calc( 100% + 14 * (100vw - 150px) / 100 )"); //预判函数 e("")
    }
```

## Escaping

- 可以被变量赋任何字符串值使用

```less
    @min768: ~"(min-width: 768px)"; // less 3.5+ 可以省去~""  css里的关键字不能赋值给变量，否则失效
    .element {
        @media @min768 {
            font-size: 1.2rem;
        }
    }
    //output
    @media (min-width: 768px) {
        .element {
            font-size: 1.2rem;
        }
    }
```

## Functions (包括转换颜色、处理字符串、算术运算等)

- [函数手册](https://less.bootcss.com/functions/)

- 将代表颜色的字符串解析为颜色

```less
    a {
        color('#aaa'); // #aaa
    }
```

- 获取图片的尺寸

```less
    image-size("file.png"); // 10px 10px   v2.2.0+  only node env
    image-width("file.png"); // 10px   v2.2.0+  only node env
    image-height("file.png"); // 10px  v2.2.0+  only node env
```

- 单位换算
  - unit groups:
    - lengths: m, cm, mm, in, pt and pc
    - time: s and ms
    - angle: rad, deg, grad and turn

```less
    convert(9s, "ms")  // 9000ms 第二个参数可不带引号
    convert(14cm, mm) // 140mm
    convert(8, mm) // incompatible unit types  8
```

- 移除或者改变属性值的单位(不进行换算)

```less
    unit(num[,unit]) // num单位可带可不带， unit目标单位，省略则只移除num单位
    unit(5, px) // 5px
    unit(5em) // 5
```

- 获取数值的单位

```less
a {
    get-unit(num) // 返回num的单位，没有单位就返回空行  问题：与数值拼接的时候单位前会有空格
}
```

- 自动根据引用的资源的大小，判断是否使用内嵌URI资源，过大就直接返回普通的url地址进行http请求
  - 将一个资源内嵌到样式文件，如果开启了ieCompat选项，而且资源文件的体积过大，或者是在浏览器中使用，则会使用url()进行回退。如果没有指定MIME，则Node.js会使用MIME包来决定正确的MIME。
  - 如果未提供mimetype，则根据文件名后缀， text和svg用UTF-8解码，其他用base64解码

```less
    data-uri([mimetype,] url);
    data-uri('../data/image.jpg'); // 输出 url('data:image/jpeg;base64,bm90IGFjdHVhbGx5IGEganBlZyBmaWxlCg=='); web显示 url('../data/image.jpg');
    data-uri('image/jpeg;base64', '../data/image.jpg'); // 同上
    data-uri('image/svg+xml;charset=UTF-8', 'image.svg'); // 输出 url("data:image/svg+xml;charset=UTF-8,%3Csvg%3E%3Ccircle%20r%3D%229%22%2F%3E%3C%2Fsvg%3E"); web显示 url('image.svg');
```

- default() 默认函数 只能用在判断里

```less
    .mixin(1)                   {x: 11}
    .mixin(2)                   {y: 22}
    .mixin(@x) when (default()) {z: @x} // 没有匹配到minxin函数的其他情况，则返回true,否则false
    div {
        .mixin(3); // 3
    }
    div.special {
        .mixin(1); // 11
    }

    .mixin(@value) when (ispixel(@value)) {width: @value}
    .mixin(@value) when not(default())    {padding: (@value / 5)} // 在至少一个除自身外的自定义函数(mixin)满足条件时被调用

    .m(@x) when (iscolor(@x)) and (default())  {default-color: @x} // 如果判断额外加了default()条件，就不能再新开一条单独的when (default()) 情况，实在要开，就要把此处的default去掉
    .m(blue)                                   {case-2: darkblue}
    .m('foo')                                  {case-1: I am 'foo'}
    .m(@x) when (isstring(@x)) and (default()) {default-string: and I am the default}
    &-green {.m(green)} // .x-green { default-color: #008000;}
    &-foo   {.m('foo')} // .x-foo {case-1: I am 'foo';}
    &-baz   {.m('baz')} // . x-baz {default-string: and I am the default;}
```

- svg渐变
  - 生成多停靠（stop）的svg渐变。至少有三个参数：方向, 第一个和最后一个的颜色及位置（百分比表示）。 第一个和最后一个指定颜色的位置都是可选的，其余颜色必须指定位置
  - 方向只能是to bottom, to right, to bottom right, to top right, ellipse 或 ellipse at center 其中一个

```less
    div {
        background-image: svg-gradient(to right, red, green 30%, blue);
    }
```

## String Functions

### escape 转义函数

- 不转义编码的字符: , / ? @ & + ' ~ ! $

```less
    escape('a=1'); // a%3D1  通过URL-encoding编码字符串, 结果不带引号  
    // 如果一个参数不是一个字符串, 转换规则不明
```

### e （预判函数）

- 认为字符串是一个参数,返回不带引号的原内容
- 该函数也接受 ~"" 转义值和数字做参数，其他类型参数会返回一个错误。
- ~"" 写法本身就具备原样输出字符串的功能

```less
    html {
        font-size: e("calc( 100% + 14 * (100vw - 150px) / 100 )");// 原样输出，不计算
    }
```

### 函数 %(string, arguments ...) 格式化一个字符串

- d, D, a, A 这四个占位符能被任何类型参数替换，使用大写占位符时，会将arguments的字符串进行转义，除了()'~!, 空格会被转义成%20，小写占位符不进行转义。要输出%时，用%%进行转义
- s, S 能被除了颜色值以外任何类型参数替换( 以color()函数得出的值进行字符串格式化 )。只会替换成字符串参数的值，而字符串参数引号都被忽略。

```less
    format-a-d: %("repetitions: %a file: %d", 1 + 2, "directory/file.less");
    format-a-d-upper: %('repetitions: %A file: %D', 1 + 2, "directory/file.less");
    format-s: %("repetitions: %s file: %s", 1 + 2, "directory/file.less");
    format-s-upper: %('repetitions: %S file: %S', 1 + 2, "directory/file.less");
    //output
    format-a-d: "repetitions: 3 file: "directory/file.less"";
    format-a-d-upper: "repetitions: 3 file: %22directory%2Ffile.less%22";
    format-s: "repetitions: 3 file: directory/file.less";
    format-s-upper: "repetitions: 3 file: directory%2Ffile.less";
```

### replace(string, pattern, replacement[, flags]) （替换）

- string: 搜索和替换用的字符串.
- pattern:一个字符串或者能搜索的正则表达式.
- replacement: 匹配模式成功的替换字符串.
- flags: (可选的) 正则表达式匹配标识（全匹配还是...）. 可写多个

```less
    replace("Hello, Mars?", "Mars\?", "Earth!");
    replace("One + one = 4", "one", "2", "gi");
    replace('This is a string.', "(string)\.$", "new $1.");
    replace(~"bar-1", '1', '2');
    // output
    "Hello, Earth!";
    "2 + 2 = 4";
    'This is a new string.';
    bar-2;
```

## List Functions

### length(list)

- list  逗号或者空格隔开的值集合
- 函数返回集合的值的个数

```less
    length(1px solid #0080ff); // 3

    @list: "banana", "tomato", "potato", "peach";
    n: length(@list);  // n: 4
```

### extract(list, index)

- 返回集合中指定索引的值。
- index从1开始

```less
a {
    extract(8px dotted red, 2); // dotted
}
```

## Math Functions

- 参数都可以带参数
- ceil(float) 返回int
- floor(float) 返回int
- percentage(float) 返回百分比
- round(float[, num]) 返回number      num为取整的小数点位置，默认0
- sqrt(num) 计算平方根并保持num的单位
- abs(num) 计算绝对值并保持num的单位
- sin(num) 会将没有单位的num认为是弧度值 cos类似 tan类似
- asin(num) 返回以弧度rad为单位的角度，区间(-π/2 到 π/2)  num为[-1, 1] 之间的浮点数,否则返回NaNrad  acos类似 atan类似(num没有限制)
- pi() 返回 3.141592653589793
- pow(numA, numB) 返回numA的numB次方，返回值单位与numA相同，numB的被忽略
- mod(numA, numB) 返回numA对numB的取余，返回值单位与numA相同，numB的被忽略
- min(num1, num2, ...)  max(num1, num2, ...) 单位要相同

## Type Functions

- isnumber()
- isstring()
- iscolor()
- iskeyword()  判断是否是关键字  iskeyword(keyword);  // true
- isurl()  isurl(url(...)); // true
- ispixel()  判断是否是带 px 长度单位的数字
- isem()  判断是否是带 em 长度单位的数字
- ispercentage()  判断是否是带 % 长度单位的数字
- isunit(val, unit) 判断是否是带指定单位的数字
- isruleset() 判断是否是一个规则集合

```less
    @rules: {
        color: red;
    }
    isruleset(@rules); // true
```

## Color Definition Functions

- rgb()
- rgba()
- argb(color) 创建格式为 #AARRGGBB 的十六进制颜色 (注意不是 #RRGGBBAA ！)。

```less
a {
  color: argb(rgba(90, 23, 148, 0.5)); // #805a1794
}
```

- hsl()  hsl(90, 100%, 50%) // #80ff00
- hsla()
- hsv() hsv(90, 100%, 50%) // #408000   HSV 与 HSL 不同，HSV是另一种在Photoshop中可用的色彩空间
- hsva()

## Color Channel Functions (不常用)

### 以下函数的参数可以是任意色彩对象的写法

- HSL色彩空间操作
  - hue 提取HSL的色相值。 hue(hsl(90, 100%, 50%)) // 90
  - saturation 提取HSL的饱和度值。saturation(hsl(90, 100%, 50%)) // 100%
  - lightness 提取HSL的亮度值。lightness(hsl(90, 100%, 50%)) // 50%

- HSV色彩空间操作
  - hsvhue 提取HSV的色相值。 hsvhue(hsv(90, 100%, 50%)) // 90
  - hsvsaturation 提取HSV的饱和度值。 hsvsaturation(hsv(90, 100%, 50%)) // 100%
  - hsvvalue 提取HSV的色调值。 hsvvalue(hsv(90, 100%, 50%)) // 50%

- RGB色彩空间操作
  - red 提取rgb的红色值 red(rgb(10, 20, 30)) // 10
  - green 提取rgb的绿色值  blue(rgb(10, 20, 30)) // 20
  - blue 提取rgb的蓝色值  blue(rgb(10, 20, 30)) // 30

- alpha 提取颜色对象的透明度值(0-1) alpha(rgba(10, 20, 30, 0.5)) // 0.5
- luma 计算颜色对象luma的值(亮度的百分比表示法, 0-100%) luma(rgb(100, 200, 30)) // 40%
- luminance 在没有伽马校正的情况下计算亮度值。luma(rgb(100, 200, 30)) // 65%

## Color Operation Functions (不常用)

- 参数必须单位/格式相同
- 百分比将作为绝对值处理 10%增加10%,结果为20%
- 参数值只能在限定的范围内
- 除了十六进制的颜色值外将对其他格式自动做简化处理。

- 以下函数参数 color为颜色对象，amount为0-100%
- saturate(color, amount) 增加一定数值的颜色饱和度。 saturate(hsl(90, 80%, 50%), 20%) // 输出#80ff00    相当于hsl(90, 100%, 50%)
- desaturate(color, amount) 降低一定数值的颜色饱和度
- lighten(color, amount) 增加一定数值的颜色亮度。 lighten(hsl(90, 80%, 50%), 20%) // #b3f075
- darken(color, amount) 降低一定数值的颜色亮度。
- fadein(color, amount) 增加颜色的不透明度，令其更不透明，对不透明的颜色无效 fadein(hsla(90, 90%, 50%, 0.5), 10%) // 输出rgba(128, 242, 13, 0.6)  相当于hsla(90, 90%, 50%, 0.6)
- fadeout(color, amount) 降低颜色的不透明度，令其更透明。对不透明的颜色无效
- fade(color, amount) 给颜色（包括不透明的颜色）设定一定数值的透明度  fade(hsl(90, 90%, 50%), 10%) // rgba(128, 242, 13, 0.1)  相当于(90, 90%, 50%, 0.1)

## Namespaces and Accessors

- 在引入命令空间时，如使用 `>` 选择器，父元素不能加括号
- 不得单独使用命名空间的方法 必须先引入命名空间，才能使用 其中方法

```less
    #bundle() {
        .button {
            display: block;
            border: 1px solid black;
            background-color: grey;
            &:hover {
                background-color: white;
            }
        }
        .tab { ... }
        .citation { ... }
    }

    #header a {
        color: orange;
        #bundle.button();  // 或者 #bundle > .button
    }


    #card(){
        background: #723232;
        .d(@w:300px){
            width: @w; // @W只在当前mixin里有效，不能用在子方法里

            #a(@h:300px){  // 和把#a写在.d()外面效果一样
                height: @h;
            }
        }
    }
    #wrap{
        #card > .d > #a(100px); // 父级不能加括号
    }
    #main{
        #card .d(); // #card后面有无空格均可，推荐用 >
    }
    #con{
        //不得单独使用命名空间的方法
        //.d() 如果前面没有引入命名空间 #card ，将会报错

        #card; // 等价于 #card(); 输出#card直接规则集
        .d(20px); //必须先引入 #card
    }
    // output
    #wrap{
        height:100px;
    }
    #main{
        width:300px;
    }
    #con{
        background: #723232;
        width:20px;
    }
```

## 继承(扩展)

- extend 是 Less 的一个伪类。它可继承所匹配声明中的全部样式
- #wrap :extend(dev pre) 允许选择器与扩展之间有空格
- 可以同时有多个扩展pre:hover:extend(div pre):extend(.bucket tr)或者pre:hover:extend(div pre, .bucket tr)
- pre:hover.nth-child(odd):extend(div pre) 扩展必须写在最后
- 如果一个规则集包含多个选择器，所有选择器都可以使用extend关键字。
- 相较与使用方法或命名空间，可以省去在不同的类下重复调用相同方法的麻烦

```less
    .animation {   // 不是纯粹的规则集（带()的），更不是命名空间，只能是普通的css类，否则extend无法扩展，实际生成的css是 , 拼起来的选择器
        transition: all .3s ease-out;
        .hide {
            transform:scale(0);
        }
    }
    #main {
        &:extend(.animation); // 不能继承 .animation .hide 的样式，也就是不会生成#main .hide，除非是用all关键字
    }
    #con {
        &:extend(.animation .hide); // 只继承 .animation .hide 的样式
    }
    // output
    .animation,
    #main {
        transition: all .3s ease-out;
    }
    .animation .hide , #con {
        transform:scale(0);
    }
```

- all 全局搜索 (只搜索第一个参数范围内的所有样式，兄弟和父级的不会搜索)

```less
    #main {
        width: 200px;
    }
    #main {
        &:after {
            content:"Less is good!";
        }
    }
    #wrap:extend(#main all) { // 如果不使用all 关键字，就不会生成#wrap:after样式 只能继承普通的样式
            // 此处 {} 里的样式只是#wrao独有的
    }
    // output
    #main,
    #wrap {
        width: 200px;
    }
    #main:after, #wrap:after {
        content: "Less is good!";
    }
```

## when 判断

- less没有if判断，但是有when判断
- 等于就是用 = 表示  == 报错
- 除去关键字 true 以外的值都被视为 false
- when () and () 并判断
- when (), () 或判断
- when not () 非判断

```less
    // 当出现某种情况时执行某段css,不用写mixin
    & when (flag) {
        .produce-ruleset {
            prop: val;
        }
    }
```

## 循环方法 (使用递归)

```less
    .generate-columns(4);

    .generate-columns(@n, @i: 1) when (@i =< @n) {
        .column-@{i} {
            width: (@i * 100% / @n);
        }
        .generate-columns(@n, (@i + 1));
    }
    // output 栅格系统
    .column-1 {
        width: 25%;
    }
    .column-2 {
        width: 50%;
    }
    .column-3 {
        width: 75%;
    }
    .column-4 {
        width: 100%;
    }
```

## Maps  (v3.5.0+)

- 命名空间作为maps的用法

### [ ]  取值 不用加引号

```less
    @sizes: {
        mobile: 320px;
        tablet: 768px;
        desktop: 1024px;
    };

    .navbar {
        display: block;

        @media (min-width: @sizes[tablet]) {
            display: inline-block;
        }
    }
    // output
    .navbar {
        display: block;
    }
    @media (min-width: 768px) {
        .navbar {
            display: inline-block;
        }
    }

```

### 类似对象的用法

```less
    #library() {
        .colors() {
            primary: green;
            secondary: blue;
        }
        .size() {
            font_size: 20px;
        }
    }

    #library() { // 名字冲突的命名空间，下面的对应属性只会覆盖上面相同的属性，其他属性不变
        .colors() { primary: grey; }
    }

    .button {
        font-size: #library.size[font_size]; //命名空间使用[]取属性值
        @colors: #library.colors(); // 把命名空间中的某个类赋值到一个变量
        color: @colors[primary];
        border-color: @colors[secondary];
    }
    // output
    .button {
        font-size: 20px;
        color: grey;
        border-color: blue;
    }
```

### 属性键也是变量时

```less
    .foods() {
        @dessert: ice cream;
    }

    @key-to-lookup: dessert;

    .lunch {
        treat: .foods[@@key-to-lookup];
    }
```

## Scope

- 在当前作用域 { } 找不到变量声明时,就往上一级 { } 作用域查找,依此类推

## Comments

- 行注释与块注释均可以使用
- // 不会编译到css文件中 /**/会编译到css中

```less
    /* 一个块注释
     * style comment! */
    @var: red;

    // 这一行被注释掉了！
    @var: white;
```

## Importing

```less
    @import "library"; // library.less
    @import "typo.css";
```

### @import (reference) 文件名

- 导入外部文件，但不会把导入的文件编译到最终输出中，只引用。

### @import (once) 文件名

- @import语句的默认行为。这表明相同的文件只会被导入一次，而随后的导入文件的重复代码都不会解析。

### @import (multiple) 文件名

- 允许导入多个同名文件。同时生效出来（存在样式覆盖）

## less中使用js

- 3.0+ 行内js由于安全性问题，默认不支持
- 静态编译需要开启 lessc xxx.less --js
- 动态编译 less-loader 需要设置  javascriptEnabled: true
- 推荐使用 @plugin
