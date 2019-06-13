# php (PHP：Hypertext Preprocessor) 弱类型

- PHP 最强大最显著的特性之一，是它支持很大范围的数据库
- PHP 可在不同的平台上运行
- PHP 会在输出时自动删除其结束符 ?> 后的一个换行
- PHP 可以生成动态页面内容
- PHP 可以创建、打开、读取、写入、关闭服务器上的文件
- PHP 可以收集表单数据
- PHP 可以发送和接收 cookies
- PHP 可以添加、删除、修改您的数据库中的数据
- PHP 可以限制用户访问您的网站上的一些页面
- PHP 可以加密数据

## 应用领域

- 服务端脚本
  - 条件：PHP 解析器（CGI 或者服务器模块）、web 服务器和 web 浏览器
- 命令行脚本
  - 条件：PHP 解析器（CGI 或者服务器模块）
- 编写桌面应用程序
  - PHP-GTK

## 安装

- 安装 Web 服务器（由于 PHP 是免费的，大多数的 Web 主机都提供对 PHP 的支持，可以跳过这步）
- 安装 PHP
- 安装数据库，比如 MySQL

## 语法

```php
<?php  // 短标记 <?  只有在通过激活 php.ini 中的 short_open_tag 配置指令或者在编译 PHP 时使用了配置选项 --enable-short-tags 时才能使用
// php代码
/*
多行
注释
*/
$txt="Hello world!"
?> // 如果文件内容是纯 PHP 代码，最好在文件末尾删除 PHP 结束标记，防止意外出现的空格或换行符
```

- PHP 中的每个代码行都必须以分号结束。分号是一种分隔符，用于把指令集区分开来。
- 在一个 PHP 代码段中的最后一行可以不用分号结束
- 通过 PHP，有两种在浏览器输出文本的基础指令：echo 和 print。
- PHP 语句和 PHP 变量都是区分大小写的

## 变量

- 变量以 $ 符号开始，后面跟着变量的名称
- 变量名必须以字母或者下划线字符开始
- 变量名只能包含字母数字字符以及下划线（A-z、0-9 和 _ ）
- 变量名不能包含空格
- 变量名是区分大小写的（$y 和 $Y 是两个不同的变量）
- PHP 没有声明变量的命令；变量在第一次赋值给它的时候被创建
- $this 是一个特殊的变量，它不能被赋值。
- 只有有名字的变量才可以引用赋值
- isset() 语言结构可以用来检测一个变量是否已被初始化

## 可变变量

- 一个变量的变量名可以动态的设置和使用
- $this不能被动态引用

```php
$a = 'hello';
$$a = 'world';
echo "$a ${$a}";
```

- 数组： ${$a[1]}

```php
class foo {
    var $bar = 'I am bar.';
    var $arr = array('I am A.', 'I am B.', 'I am C.');
    var $r   = 'I am r.';
}

$arr = 'arr';
// I am r.
echo $foo->$arr[1] . "\n";  // $foo->'r'  ？？？？？？？
// I am B.
echo $foo->{$arr}[1] . "\n";
```

- 类： $foo->$bar

## 预定义变量

- register_globals默认值为off,影响到预定义变量集在全局范围内的有效性。如：必须使用 $_SERVER['DOCUMENT_ROOT'] 代替 $DOCUMENT_ROOT
- 自动全局变量（autoglobals）或者超全局变量（superglobals）
  - PHP 提供了一套附加的预定数组，这些数组变量包含了来自 web 服务器（如果可用），运行环境，和用户输入的数据，它们在全局范围内自动生效
  - 旧的预定义数组（$HTTP_*_VARS）仍旧存在，但是它和超全局变量并不是同一个变量，所以改变其中一个的值并不会对另一个产生影响
  - 超级全局变量不能被用作函数或类方法中的可变变量。

## 变量作用域

- local、global、static、parameter四种不同的变量作用域

### 全局作用域

- 在所有函数外部定义的变量，拥有全局作用域，它的作用域从定义处一直到文件结尾
- 全局变量可以被脚本中的任何部分访问
- 要在一个函数中访问一个全局变量，需要使用 global 关键字
- PHP 将所有全局变量存储在一个名为 $GLOBALS[index] 的数组中。 index 保存变量的名称。这个数组可以在函数内部访问，也可以直接用来更新全局变量。

### 局部作用域

- 在 PHP 函数内部声明的变量是局部变量，仅能在函数内部访问，它的作用域为函数定义范围内

```php
<?php
$x=5;
$y=10;

$a=3;
$b=4;

function myTest() {
    global $x,$y;
    $y=$x+$y;

    $GLOBALS['a']=$GLOBALS['a']+$GLOBALS['b']; // 不用global关键字
}

myTest();
echo $y; // 输出 15
?>
```

### static 作用域

- 当一个函数完成时，它的所有变量通常都会被删除
- 第一次声明变量时使用 static 关键字可以让该局部变量不被删除
- 每次调用该函数时，该变量将会保留着函数前一次被调用时的值
- 该变量仍然是函数的局部变量
- 静态声明是在编译时解析的

```php
<?php
function myTest() {
    static $x=0;
    echo $x;
    $x++;
}

myTest();
myTest();
myTest();
?>
```

### 参数作用域

- 参数是通过调用代码将值传递给函数的局部变量
- 参数是在参数列表中声明的，作为函数声明的一部分

### 输出

- echo 输出的速度比 print 快， echo 没有返回值，print有返回值1

### echo

- 可以输出一个或多个字符串
- 可以不用加括号，也可以加上括号： echo 或 echo()
- 显示变量

```php
<?php
$txt1="study PHP";
$txt2="good";
$cars=array("Volvo","BMW","Toyota");

echo $txt1;
echo "<br>";
echo " $txt2 PHP ";
echo "<br>";
echo "brand is {$cars[0]}", "maybe"; // print不能这样写
?>
```

### print

- 只允许输出一个字符串，返回值总为 1
- 可以使用括号，也可以不使用括号： print 或 print()
- 写法与echo一样，但一次只能输出一个字符串

### print_r

- 打印关于变量的易于理解的信息
- 变量为 string、integer 或 float，将打印变量值本身
- 变量为 array、object，将会按照一定格式显示键和元素
- print_r() 将把数组的指针移到最后边。使用 reset() 可让指针回到开始处。

### var_dump

- 显示关于一个或多个表达式的结构信息，包括表达式的类型与值
- 数组将递归展开值，通过缩进显示其结构
- var_dump 返回表达式的类型与值而 print_r 仅返回结果

### 定界符（EOT、EOD、EOF等等）

- PHP 定界符 EOF 的作用就是按照原样，包括换行格式什么的，输出在其内部的东西
- 在 PHP 定界符 EOF 中的任何特殊字符都不需要转义
- 写法规则
  - 必须后接分号，否则编译通不过
  - EOF 可以用任意其它字符代替，只需保证结束标识与开始标识一致
  - 结束标识必须顶格独自占一行(即必须从行首开始，前后不能衔接任何空白和字符)
  - 开始标识可以不带引号或带单双引号，不带引号与带双引号效果一致，解释内嵌的变量和转义符号，带单引号则不解释内嵌的变量和转义符号
  - 当内容需要内嵌引号（单引号或双引号）时，不需要加转义符，本身对单双引号转义，此处相当与q和qq的用法

```php
<?php
echo <<<EOF
    <h1>one</h1>
    <p>two</p>
EOF;
// 结束需要独立一行且前后不能空格
$name="ddee";
$a= <<<EOF
    "abc"$name
    "123"
EOF;
echo $a;
?>
```

## 数据类型

- String、Integer、Float、Boolean、 Array、Object、NULL
- gettype() 获取变量的类型，返回字符串。但不能用来判断属于某种类型，返回的值不是永久固定的
- is_*() 方法判断属于某个类型，比如is_bool() 判断是否属于boolean类型

### Boolean类型

- TRUE 或 FALSE 不区分大小
- FALSE
  - 布尔值 FALSE 本身
  - 整型值 0（零）
  - 浮点型值 0.0（零）
  - 空字符串，以及字符串 "0"
  - 不包括任何元素的数组
  - 特殊类型 NULL（包括尚未赋值的变量）
  - 从空标记生成的 SimpleXML 对象
- 所有其它值都被认为是 TRUE（包括任何资源 和 NAN）。

### Integer

- 整数可以是正数或负数
- 整型可以用三种格式来指定：十进制， 十六进制（ 以 0x 为前缀）或八进制（前缀为 0）
- Integer 值的字长可以用常量 PHP_INT_SIZE来表示
- 最大值可以用常量 PHP_INT_MAX 来表示
- 最小值可以在 PHP 7.0.0 及以后的版本中用常量 PHP_INT_MIN 表示
- 整数溢出：如果给定的一个数超出了 integer 的范围，将会被解释为 float。同样如果执行的运算结果超出了 integer 范围，也会返回 float
- PHP 中没有整除的运算符，1/2 产生出 float 0.5，可以使用强转或者round()方法四舍五入，intval()获取变量的整数值(默认是十进制)
- 将 resource 转换成 integer 时， 结果会是 PHP 运行时为 resource 分配的唯一资源号
- Boolean值  FALSE 将产生出 0（零），TRUE 将产生出 1（壹）
- 当从浮点数转换成整数时，将向下取整
  - 如果浮点数超出了整数范围，则结果为未定义，因为没有足够的精度给出一个确切的整数结果。在此情况下没有警告，甚至没有任何通知
  - PHP 7.0.0 起，NaN 和 Infinity 在转换成 integer 时，不再是 undefined 或者依赖于平台，而是都会变成零

```php
$a = 1234; // 十进制数
$a = -123; // 负数
$a = 0123; // 八进制数 (等于十进制 83)
$a = 0x1A; // 十六进制数 (等于十进制 26)
$a = 0b11111111; // 二进制数字 (等于十进制 255)

// 绝不要将未知的分数强制转换为 integer，这样有时会导致不可预料的结果。
echo (int) ( (0.1+0.7) * 10 ); // 显示 7! 浮点数的精度的问题
```

### Float

- 浮点数是带小数部分的数字，或是指数形式
- 也叫浮点数 float，双精度数 double 或实数 real
- 浮点数的精度
  - 永远不要相信浮点数结果精确到了最后一位，也永远不要比较两个浮点数是否相等
  - 更高的精度，应该使用任意精度数学函数或者 gmp 函数
- 其他类型转换为浮点数，一般都是先转换成整型，再转换成浮点型
- 比较浮点数
  - 使用机器极小值（epsilon）或最小单元取整数，使用一个仅比该数值大一丁点的最小误差值，判断2个浮点数之间的差值小于误差值就可以认为相等
- NAN
  - 代表着一个在浮点数运算中未定义或不可表述的值
  - 任何拿此值与其它任何值（除了 TRUE）进行的松散或严格比较的结果都是 FALSE
  - 由于 NAN 代表着任何不同值，不应拿 NAN 去和其它值进行比较，包括其自身，应该用 is_nan() 来检查。

### string

- 由一系列的字符组成，其中每个字符等同于一个字节
- PHP 只能支持 256 的字符集，因此不支持 Unicode
- string 最大可以达到 2GB
- 单引号除了会对\`和\\进行解析，其他都是原样输入内容
- 双引号会对内容进行解析
  - 双引号能解析的所有特殊字符
    - \n 换行
    - \r 回车
    - \t 水品制表符
    - \v 垂直制表符
    - \e Escape
    - \f 换页
    - \\ 反斜线
    - \$ 美元标记
    - \" 双引号
    - \[0-7]{1,3} 符合该正则表达式序列的是一个以八进制方式来表达的字符
    - \x[0-9A-Fa-f]{1,2}  符合该正则表达式序列的是一个以十六进制方式来表达的字符
- 字符串的heredoc结构（即定界符），类似于双引号解析
  - 当heredoc 包含变量时，此时Heredocs 结构不能用来初始化类的属性
- 字符串的nowdoc结构，类似于单引号解析
  - nowdoc 结构可以用在任意的静态数据环境中，最典型的示例是用来初始化类的属性或常量

```php
// nowdoc结构
$str = <<<'EOD'
Example of string
spanning multiple lines
using nowdoc syntax.
EOD;
```

- 变量解析
  - 简单语法： $变量名
  - 复杂语法： {表达式}   {}与表达式不能有空格
- 存取和修改字符串中的字符
  - string 中的字符可以通过一个从 0 开始的下标，用类似 array 结构中的方括号包含对应的数字来访问和修改，比如 $str[42]
  - string 也可用花括号访问，比如 $str{42}
  - 用超出字符串长度的下标写入将会拉长该字符串并以空格填充
  - 非整数类型下标会被转换成整数
  - 非法下标类型会产生一个 E_NOTICE 级别错误
  - 用负数下标写入字符串时会产生一个 E_NOTICE 级别错误
  - 用负数下标读取字符串时返回空字符串
  - 写入时只用到了赋值字符串的第一个字符
  - 用空字符串赋值则赋给的值是 NULL 字符
  - PHP 的字符串在内部是字节组成的数组。因此用花括号访问或修改字符串对多字节字符集很不安全,仅应对单字节编码的字符串进行此类操作
  - 字符串下标必须为整数或可转换为整数的字符串，否则会发出警告
- 转换成字符串
  - boolean值TRUE转换成 "1", FALSE转换成""
  - 数组 array 总是转换成字符串 "Array"
  - 对象 object 总是被转换成字符串 "Object", 为了得到对象的类的名称，可以用 get_class() 函数, 适当时可以用 __toString 方法
  - 资源 resource 总会被转变成 "Resource id #1" 这种结构的字符串，其中的 1 是 PHP 在运行时分配给该 resource 的唯一值。不要依赖此结构，可能会有变更。要得到一个 resource 的类型，可以用函数 get_resource_type()
  - NULL 总是被转变成空字符串
  - 大部分的 PHP 值可以转变成 string 来永久保存，这被称作串行化，可以用函数 serialize() 来实现
- 字符串转换成数值
  - 如果该字符串没有包含 '.'，'e' 或 'E' 并且其数字值在整型的范围之内（由 PHP_INT_MAX 所定义），该字符串将被当成 integer 来取值。其它所有情况下都被作为 float 来取值
  - 如果该字符串以合法的数值开始，则使用该数值。否则其值为 0（零）。合法数值由可选的正负号，后面跟着一个或多个数字（可能有小数点），再跟着可选的指数部分。指数部分由 'e' 或 'E' 后面跟着一个或多个数字构成

```php
$foo = 1 + "10.5";                // $foo is float (11.5)
$foo = 1 + "-1.3e3";              // $foo is float (-1299)
$foo = 1 + "bob-1.3e3";           // $foo is integer (1)
$foo = 1 + "bob3";                // $foo is integer (1)
$foo = 1 + "10 Small Pigs";       // $foo is integer (11)
$foo = 4 + "10.2 Little Piggies"; // $foo is float (14.2)
$foo = "10.0 pigs " + 1;          // $foo is float (11)
$foo = "10.0 pigs " + 1.0;        // $foo is float (11)
```

### Array

- array(  key =>  value, ...)
  - 键（key）可是是一个整数 integer 或字符串 string
  - 值（value）可以是任意类型的值
- 可以用[]代替array()
- key值的强制转换
  - 包含有合法整型值的字符串会被转换为整型。例如键名 "8" 实际会被储存为 8。但是 "08" 则不会强制转换，因为其不是一个合法的十进制数值
  - 浮点数也会被转换为整型，意味着其小数部分会被舍去。例如键名 8.7 实际会被储存为 8
  - 布尔值也会被转换成整型。即键名 true 实际会被储存为 1 而键名 false 会被储存为 0
  - Null 会被转换为空字符串，即键名 null 实际会被储存为 ""
  - 数组和对象不能被用为键名。坚持这么做会导致警告：Illegal offset type
  - 如果在数组定义中多个单元都使用了同一个键名，则只使用了最后一个，之前的都被覆盖了
- PHP 数组可以同时含有 integer 和 string 类型的键名，因为 PHP 实际并不区分索引数组和关联数组
- 如果对给出的值没有指定键名，则取当前最大的整数索引值，而新的键名将是该值加一。如果指定的键名已经有了值，则该值会被覆盖
- 仅对部分单元指定键名

```php
$array = array(
         "a",
         "b",
    6 => "c",
         "d",
);
var_dump($array);
// array(4) {
//   [0]=>
//   string(1) "a"
//   [1]=>
//   string(1) "b"
//   [6]=>
//   string(1) "c"
//   [7]=>
//   string(1) "d"
// }
```

- 新建与修改数组
  - 如果给出方括号但没有指定键名，则取当前最大整数索引值，新的键名将是该值加上 1（但是最小为 0）。如果当前还没有整数索引，则键名将为 0

    ```php
    $arr = array(5 => 1, 12 => 2);

    $arr[] = 56;    // This is the same as $arr[13] = 56;

    $arr["x"] = 42; // This adds a new element

    unset($arr[5]); // This removes the element from the array

    unset($arr);    // This deletes the whole array
    ```

  - 这里所使用的最大整数键名不一定当前就在数组中。它只要在上次数组重新生成索引后曾经存在过就行了
  
    ```php
    $array = array(1, 2, 3, 4, 5);
    print_r($array);

    // 现在删除其中的所有元素，但保持数组本身不变:
    foreach ($array as $i => $value) {
        //unset() 函数允许删除数组中的某个键。但要注意数组将不会重建索引。如果需要删除后重建索引，可以用 array_values() 函数。
        unset($array[$i]);
    }
    print_r($array);

    // 添加一个单元（注意新的键名是 5，而不是你可能以为的 0）
    $array[] = 6;
    print_r($array);
    <!-- Array
    (
        [5] => 6
    ) -->

    // 重新索引：
    $array = array_values($array);
    $array[] = 7;
    print_r($array);
    <!-- Array
    (
        [0] => 6
        [1] => 7
    ) -->
    ```

- 转换为数组
  - (array)$scalarValue 与 array($scalarValue) 完全一样
  - 将 NULL 转换为 array 会得到一个空的数组
- 可以用 array_diff() 和数组运算符来比较数组
- 数组遍历 foreach
- count() 函数来数出数组中元素的个数。
- 数组排序sort($files)
- 引用运算符&

```php
// PHP 的引用允许用两个变量来指向同一个内容
$a="ABC";
$b =&$a;
echo $a;//这里输出:ABC
echo $b;//这里输出:ABC
$b="EFG";
echo $a;//这里$a的值变为EFG 所以输出EFG
echo $b;//这里输出EFG
```

### Object

- 在 PHP 中，对象必须声明
- 使用class关键字声明类对象
- 创建一个新的对象object,就要使用new语句实例化一个类

### Resource

- 资源 resource 是一种特殊变量，保存了到外部资源的一个引用
- 资源是通过专门的函数来建立和使用的， get_resource_type()
- 由于资源类型变量保存有为打开文件、数据库连接、图形画布区域等的特殊句柄，因此将其它类型的值转换为资源没有意义
- 释放资源：引用计数系统是 Zend 引擎的一部分，可以自动检测到一个资源不再被引用了（和 Java 一样）。这种情况下此资源使用的所有外部资源都会被垃圾回收系统释放。因此，很少需要手工释放内存
- 持久数据库连接比较特殊，它们不会被垃圾回收系统销毁

### NULL

- NULL 类型只有一个值，就是不区分大小写的常量 NULL
- 通过设置变量值为 NULL 来清空变量数据
- 变量为null的情况
  - 被赋值为 NULL
  - 尚未被赋值
  - 被 unset()
- 转换为NULL: 使用 (unset) $var 将一个变量转换为 null 将不会删除该变量或 unset 其值。仅是返回 NULL 值而已。

### Callback / Callable 类型

- 可用 callable 类型指定回调类型 callback

```php
function aaa(callable $g): void {
    $g();
}
function go() {

    echo 'hello';
}
aaa('go');
// callable规定参数必须是可调用执行的
```

- 一些函数如 call_user_func() 或 usort() 可以接受用户自定义的回调函数作为参数
- 回调函数不止可以是简单函数，还可以是对象的方法，包括静态类方法
- 传递：
  - PHP是将函数以string形式传递的。可以使用任何内置或用户自定义函数，但除了语言结构例如：array()，echo，empty()，eval()，exit()，isset()，list()，print 或 unset()
  - 一个已实例化的 object 的方法被作为 array 传递，下标 0 包含该 object，下标 1 包含方法名。 在同一个类里可以访问 protected 和 private 方法。
  - 静态类方法也可不经实例化该类的对象而传递，只要在下标 0 中包含类名而不是对象。自 PHP 5.2.3 起，也可以传递 'ClassName::methodName'
  - 除了普通的用户自定义函数外，也可传递 匿名函数 给回调参数
  - 在函数中注册有多个回调内容时(如使用 call_user_func() 与 call_user_func_array())，如在前一个回调中有未捕获的异常，其后的将不再被调用。

```php
function my_callback_function() {
    echo 'hello world!';
}

class MyClass {
    static function myCallbackMethod() {
        echo 'Hello World!';
    }
}

call_user_func('my_callback_function');

call_user_func(array('MyClass', 'myCallbackMethod'));

$obj = new MyClass();
call_user_func(array($obj, 'myCallbackMethod'));

call_user_func('MyClass::myCallbackMethod');

// Relative static class method call
class A {
    public static function who() {
        echo "A\n";
    }
}

class B extends A {
    public static function who() {
        echo "B\n";
    }
}

call_user_func(array('B', 'parent::who')); // A

// Objects implementing __invoke can be used as callables
class C {
    public function __invoke($name) {
        echo 'Hello ', $name, "\n";
    }
}

$c = new C();
call_user_func($c, 'PHP!');

// Our closure
$double = function($a) {
    return $a * 2;
};

$numbers = range(1, 5);

$new_numbers = array_map($double, $numbers);

print implode(' ', $new_numbers);
```

### 伪类型与变量

- 伪类型是 PHP 文档里用于指示参数可以使用的类型和值
- 它们不是 PHP 语言里原生类型
- 不能把伪类型用于自定义函数里的类型约束
- mixed: 说明一个参数可以接受多种不同的（但不一定是所有的）类型
- number: 说明一个参数可以是 integer 或者 float
- callback相当于callable
- array|object: 参数既可以是 array 也可以是 object。
- void: void 作为返回类型意味着函数的返回值是无用的。void 作为参数列表意味着函数不接受任何参数
- ...  在函数原型中，$... 表示等等的意思。当一个函数可以接受任意个参数时使用此变量名。

## 类型检查

- php的类型检查指的是运行时的类型检查
- 目前只有函数参数的类型检查和函数返回值的类型检查

```php
function aaa(callable $g): void {}
```

- 除部分基础类型以外，其他的类型检查都在5.0+支持，比如callable,array等

```php
// 7.0+后原不支持的部分基础类型检查开始支持，但是必须加上这句声明，作用域只在当前php文件以内
declare (strict_types = 1);
function aaaaaa(int $h) {}
```

## 类型强制转换

- (int), (integer)  
- (bool), (boolean)
- (float), (double), (real)
- (string), "${para}"
- (array)
- (object)
- (unset) 转换为NULL
- (binary), b前缀 将字符串文字和变量转换为二进制字符串

```php
$binary = (binary)$string;
$binary = b"binary string";
```

## 常量

- 一个常量由英文字母、下划线、和数字组成,但数字不能作为首字母出现。
- 常量名不需要加 $ 修饰符
- 常量在整个脚本中都可以直接使用
- define ( $name , mixed $value [, $case_insensitive = false ] )   设置常量
  - name：必选参数，常量名称，即标志符
  - value：必选参数，常量的值。
  - case_insensitive ：可选参数，如果设置为 TRUE，该常量则大小写不敏感。默认是大小写敏感的
- 如果只想检查是否定义了某常量，用 defined() 函数
- 如果常量名是动态的，也可以用函数 constant() 来获取常量的值
- 用 get_defined_constants() 可以获得所有已定义的常量列表
- 与变量不同，不应该在常量前面加上 $ 符号
- 常量和（全局）变量在不同的名字空间中。这意味着例如 TRUE 和 $TRUE 是不同的
- 常量只能包含标量数据, 常量的值只能是标量
  - 标量值不能拆分为更小的单元
  - PHP 支持四种标量值（boolean，integer，float 和 string）
  - PHP 也支持两种复合类型：数组和对象
- 使用define可以在任意地方定义常量，使用const只能处于最顶端的作用区域定义常量，因为用此方法是在编译时定义的，因此不能在函数内，循环内以及 if 语句之内用 const 来定义常量

```php
define("GREETING", "welcome");
define("GREETING", "欢迎访问 123.com", true) // 不区分大小写的常量名
```

## 魔术常量

- 有八个魔术常量它们的值随着它们在代码中的位置改变而改变
- 这些特殊的常量大部分不区分大小写

### __LINE__

- 文件中的当前行号

```php
<?php
echo '这是第 " '  . __LINE__ . ' " 行'; // 2
?>
```

### __FILE__

- 文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名
- 总是包含一个绝对路径（如果是符号连接，则是解析后的绝对路径）

```php
<?php
echo __FILE__; // E:\wamp\www\test\index.php
?>
```

### __DIR__

- 文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录
- 等价于 dirname(__FILE__)
- 除非是根目录，否则目录中名不包括末尾的斜杠

```php
<?php
echo __DIR__; // E:\wamp\www\test
?>
```

### __FUNCTION__

- 返回该函数被定义时的名字（区分大小写）

```php
<?php
function test() {
    echo  '函数名为：' . __FUNCTION__ ;
}
test(); // 函数名为：test
?>
```

### __METHOD__

- 类的方法名
- 返回该方法被定义时的名字（区分大小写）

### __CLASS__

- 返回该类被定义时的名字（区分大小写）
- 类名包括其被声明的作用区域（例如 Foo\Bar）
- 当用在 trait 方法中时，调用 trait 方法的类的名字

```php
<?php
class test {
    function _print() {
        echo '类名为：'  . __CLASS__ . ";";
        echo  '函数名为：' . __FUNCTION__ ;
    }
}
$t = new test();
$t->_print(); // 类名为：test;函数名为：_print
?>
```

### __TRAIT__

- Trait 名包括其被声明的作用区域（例如 Foo\Bar）

### __NAMESPACE__

- 当前命名空间的名称（区分大小写）
- 此常量是在编译时定义的

## 字符串变量

```php
$txt="Hello world!";
```

### 并置运算符 (.)

- 在 PHP 中唯一的一个字符串运算符
- 用于把两个字符串值连接起来

```php
$txt1="Hello world!";
$txt2="What a nice day!";
echo $txt1 . " " . $txt2;
```

### strpos() 函数

- 用于在字符串内查找一个字符或一段指定的文本
- 如果在字符串中找到匹配，该函数会返回第一个匹配的字符位置。如果未找到匹配，则返回 FALSE

```php
echo strpos("Hello world!","world");
```

## 表达式

- PHP 是一种面向表达式的语言，从这一方面来讲几乎一切都是表达式
- 赋值语句本身求值为被赋的值

```php
$a = 5
// 附值语句本身的值就是5
// 不必管内部是做什么的，它就是一个值为 5 的表达式
$b = ($a = 5)
$a =5; $b=5
$b = $a =5
// 这三种写法一致

$a = ($b = 4) + 5
// $a=9 $b=4
```

## 运算符

- 赋值运算符 = 用于给变量赋值
- 算术运算符 + 用于把值加在一起
- 优先级口诀：括（号）、单（操作数）、算（术）、移（位）、关（系）；位（运算符）、逻（辑）、条（件）、赋（值）、逗（号）

### 算术运算符

- +、-、*、/、%
- 取反和并置
- 整除 intdiv()  (版本PHP7+)
- 除法运算符总是返回浮点数。只有在下列情况例外：两个操作数都是整数（或字符串转换成的整数）并且正好能整除，这时它返回一个整数
- 取模运算符的操作数在运算之前都会转换成整数（除去小数部分）
  - $a % $b 的结果和 $a 的符号相同

```php
var_dump(intdiv(10, 3)); // int(3)
```

### 赋值运算符

- =、+=、-=、*=、/=、%=、.=

```php
$b = "Hello ";
$b .= "There!"; // $b = $b . "There!"
```

### 递增/递减运算符

- ++、--

### 比较运算符

- ==、===、!=、<>、!==、>、<、>=、<=

### 逻辑运算符

- and、or、xor、&&、||、!
- xor  (异或) 有且仅有一个为 true，则返回 true
- 优先级
  - &&  >  ||  >  =  >  and

### 数组运算符

- 集合
  - x + y  得到x 和 y 的集合
- 相等
  - x == y 如果 x 和 y 具有相同的键/值对，则返回 true
- 恒等
  - x === y 如果 x 和 y 具有相同的键/值对，且顺序相同类型相同，则返回 true
- 不相等
  - x != y  x <> y  如果 x 不等于 y，则返回 true
- 不恒等
  - x !== y 如果 x 不等于 y，则返回 true

### 三元运算符 (?:)

- (expr1) ? (expr2) : (expr3)
  - 自 PHP 5.3+ 起 简写为  expr1 ?: expr3

```php
$test = 'string';
// 普通写法
$username = isset($test) ? $test : 'nobody';
echo $username, PHP_EOL;  // PHP_EOL 是一个换行符，兼容更大平台, 会根据当前系统自动实现对应的换行符windows是\r\n，mac是 \r，linux是\n

// PHP 5.3+ 版本写法
$username = $test ?: 'nobody';
echo $username, PHP_EOL;
```

### NULL 合并运算符 (??)  自PHP7+起

- 与 ?:简写形式类似

### 组合比较符(PHP7+)

```php
// 整型
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1

// 浮点型
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1

// 字符串
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
```

### 位运算符和引用（&）

- $c = &$a;
  - c 是 a 的引用,也就是说 c 和 a 是同一个变量了
  - & 是引用的意思，php 没有指针的概念

## if...else if...else 和switch 判断语法

## 数组 array()

- $cars=array("Volvo","BMW","Toyota");
- 计算数组长度
  - count($cars);
- 数值数组 - 带有数字 ID 键的数组
- 关联数组 - 带有指定的键的数组，每个键关联一个值
- 多维数组 - 包含一个或多个数组的数组

### 数值数组

- 带有数字 ID 键的数组
- ID 键总是从 0 开始

### 关联数组

- 带有指定的键的数组，每个键关联一个值

```php
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");

foreach($age as $x=>$x_value)
{
    echo "Key=" . $x . ", Value=" . $x_value;
}
```

### 数组排序

- 以下方法会改变原数组
- sort() - 对数组进行升序排列
- rsort() - 对数组进行降序排列
- asort() - 根据关联数组的值，对数组进行升序排列
- ksort() - 根据关联数组的键，对数组进行升序排列
- arsort() - 根据关联数组的值，对数组进行降序排列
- krsort() - 根据关联数组的键，对数组进行降序排列

## 超级全局变量（PHP 4.1.0+）

- 在一个脚本的全部作用域中都可用
- 不需要特别说明，就可以在函数及类中使用

### $GLOBALS

- 包含了全部变量的全局组合数组，变量的名字就是数组的键。

```php
$x = 75;
$y = 25;

function addition()
{
    $GLOBALS['z'] = $GLOBALS['x'] + $GLOBALS['y'];
}
```

### $_SERVER

- 包含了诸如头信息(header)、路径(path)、以及脚本位置(script locations)等等信息的数组
- 数组中的项目由 Web 服务器创建
- 不能保证每个服务器都提供全部项目,服务器可能会忽略一些，或者提供一些没有在这里列举出来的项目
- $_SERVER['PHP_SELF']
  - 当前执行脚本的文件名，与 document root 有关
  - 在地址为 `http://example.com/test.php/foo.bar` 的脚本中使用 $_SERVER['PHP_SELF'] 将得到 /test.php/foo.bar
  - __FILE__ 常量包含当前(例如包含)文件的完整路径和文件名
- $_SERVER['GATEWAY_INTERFACE']
  - 服务器使用的 CGI 规范的版本；例如，"CGI/1.1"。
- $_SERVER['SERVER_ADDR']
  - 当前运行脚本所在的服务器的 IP 地址
- $_SERVER['SERVER_NAME']
  - 当前运行脚本所在的服务器的主机名。(如: www.baidu.com)
  - 如果脚本运行于虚拟主机中，该名称是由那个虚拟主机所设置的值决定
- $_SERVER['SERVER_SOFTWARE']
  - 服务器标识字符串，在响应请求时的头信息中给出。 (如：Apache/2.2.24)
- $_SERVER['SERVER_PROTOCOL']
  - 请求页面时通信协议的名称和版本。例如，"HTTP/1.0"。
- $_SERVER['REQUEST_METHOD']
  - 访问页面使用的请求方法；例如，"GET", "HEAD"，"POST"，"PUT"。
- $_SERVER['REQUEST_TIME']
  - 请求开始时的时间戳。 (如：1377687496)
- $_SERVER['QUERY_STRING']
  - query string（查询字符串），如果有的话，通过它进行页面访问
- $_SERVER['HTTP_ACCEPT']
  - 当前请求头中 Accept: 项的内容，如果存在的话
- $_SERVER['HTTP_ACCEPT_CHARSET']
  - 当前请求头中 Accept-Charset: 项的内容，如果存在的话。例如："iso-8859-1,*,utf-8"
- $_SERVER['HTTP_HOST']
  - 当前请求头中 Host: 项的内容，如果存在的话。
- $_SERVER['HTTP_REFERER']
  - 引导用户代理到当前页的前一页的地址（如果存在）,由 user agent 设置决定
  - 并不是所有的用户代理都会设置该项，有的还提供了修改 HTTP_REFERER 的功能,所以不一定真实
- $_SERVER['HTTPS']
  - 如果脚本是通过 HTTPS 协议被访问，则被设为一个非空的值
- $_SERVER['REMOTE_ADDR']
  - 浏览当前页面的用户的 IP 地址
- $_SERVER['REMOTE_HOST']
  - 浏览当前页面的用户的主机名
  - DNS 反向解析不依赖于用户的 REMOTE_ADDR
- $_SERVER['REMOTE_PORT']
  - 用户机器上连接到 Web 服务器所使用的端口号
- $_SERVER['SCRIPT_FILENAME']
  - 当前执行脚本的绝对路径
- $_SERVER['SERVER_ADMIN']
  - 该值指明了 Apache 服务器配置文件中的 SERVER_ADMIN 参数。(如：someone@baidu.com)
  - 如果脚本运行在一个虚拟主机上，则该值是那个虚拟主机的值
- $_SERVER['SERVER_PORT']
  - Web 服务器使用的端口。默认值为 "80"。
  - 如果使用 SSL 安全连接，则这个值为用户设置的 HTTP 端口。
- $_SERVER['SERVER_SIGNATURE']
  - 包含了服务器版本和虚拟主机名的字符串。
- $_SERVER['PATH_TRANSLATED']
  - 当前脚本所在文件系统（非文档根目录）的基本路径
  - 这是在服务器进行虚拟到真实路径的映像后的结果
- $_SERVER['SCRIPT_NAME']
  - 包含当前脚本的路径
  - 这在页面需要指向自己时非常有用
  - __FILE__ 常量包含当前脚本(例如包含文件)的完整路径和文件名
- $_SERVER['SCRIPT_URI']
  - URI 用来指定要访问的页面。例如 "/index.html"。

### $_REQUEST

- 用于收集HTML表单提交的数据
- 支持两种方式发送过来的请求，即 post 和 get 它都可以接受，显示不显示要看传递方法，get 会显示在 url 中（有字符数限制），post 不会在 url 中显示，可以传递任意多的数据（只要服务器支持）
- 变量名中的点和空格被转换成下划线。例如 `<input name="a.b" />` 变成了 `$_REQUEST["a_b"]`。
- `<input type="image" src="image.gif" name="sub" />`点击提交会加上两个变量sub_x 和 sub_y，它们包含了用户点击图像的坐标（实际上是sub.x 和 sub.y，但 PHP 自动将点转换成了下划线）

```php
<form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
Name: <input type="text" name="fname">
<input type="submit">
</form>

<?php
$name = $_REQUEST['fname'];
echo $name;
?>
```

### $_POST

- 应用于收集表单数据，在HTML form标签的指定该属性："method="post"

```php
<form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
Name: <input type="text" name="fname">
<input type="submit">
</form>

<?php
$name = $_POST['fname']; // 和 $_REQUEST类似
echo $name;
?>
```

### $_GET

- 应用于收集表单数据，在HTML form标签的指定该属性："method="get"
- 接受所有以 get 方式发送的请求，及浏览器地址栏中的 ? 之后的内容

```php
<a href="test_get.php?subject=PHP&web=runoob.com">Test $GET</a> // 点击发送数据到 test_get.php

<?php
echo "Study " . $_GET['subject'] . " at " . $_GET['web']; // 通过 $_GET获取
?>
```

## HTTP Cookies

- 可以用 setcookie() 函数设定 cookies
- Cookies 是 HTTP 信息头中的一部分，因此 SetCookie 函数必须在向浏览器发送任何输出之前调用，header()方法一样需要。
- Cookie 数据会在相应的 cookie 数据数组中可用，例如 $_COOKIE，$HTTP_COOKIE_VARS 和 $_REQUEST
- 如果要将多个值赋给一个 cookie 变量，必须将其赋成数组

```php
// 这将会建立两个单独的 cookie，尽管 MyCookie 在脚本中是一个单一的数组
// 如果想在仅仅一个 cookie 中设定多个值，考虑先在值上使用 serialize() 或 explode()
setcookie("MyCookie[foo]", 'Testing 1', time()+3600);
setcookie("MyCookie[bar]", 'Testing 2', time()+3600);
```

## 循环

- while - 只要指定的条件成立，则循环执行代码块
- do...while - 首先执行一次代码块，然后在指定的条件成立时重复这个循环
- for - 循环执行代码块指定的次数
- foreach - 根据数组中每个元素来循环代码块

```php
<?php
$x=array("one","two","three");
foreach ($x as $value)
{
    echo $value . "<br>";
}
?>

// 冒泡排序
<?php
$arr = array(5,3,6,2,8,10);
for($i = count($arr)-1;$i>=0;$i--){
    for($j = 0 ; $j < $i ; $j++){
        if($arr[$j+1] > $arr[$j] ){
            $aa = $arr[$j+1];
            $arr[$j+1] = $arr[$j];
            $arr[$j] = $aa;
        }
    }
}
print_r($arr);
?>
```

## namespace

- 解决用户编写的代码与PHP内部的类/函数/常量或第三方类/函数/常量之间的名字冲突
- 为很长的标识符名称(通常是为了缓解第一类问题而定义的)创建一个别名（或简短）的名称，提高源代码的可读性
- 如果一个文件中包含命名空间，它必须在其它所有代码之前声明命名空间

```php
// 在声明命名空间之前唯一合法的代码是用于定义源文件编码方式的 declare 语句
// 所有非 PHP 代码包括空白符都不能出现在命名空间的声明之前
declare(encoding='UTF-8'); //定义多个命名空间和不包含在命名空间中的代码
namespace MyProject {
    const CONNECT_OK = 1;
    class Connection { /* ... */ }
    function connect() { /* ... */  }
}

namespace AnotherProject {
    const CONNECT_OK = 1;
    class Connection { /* ... */ }
    function connect() { /* ... */  }
}
// 将全局的非命名空间中的代码与命名空间中的代码组合在一起，只能使用大括号形式的语法
namespace { // 全局代码
session_start();
$a = MyProject\connect();
echo MyProject\Connection::start();
}
```

### 子命名空间

```php
<?php
namespace MyProject\Sub\Level;  //声明分层次的单个命名空间

const CONNECT_OK = 1;  // MyProject\Sub\Level\CONNECT_OK
class Connection { /* ... */ } // MyProject\Sub\Level\Connection
function Connect() { /* ... */  } // MyProject\Sub\Level\Connect

?>
```

### 命名空间使用

- 如果命名空间中的函数或常量未定义，则该非限定的函数名称或常量名称会被解析为全局函数名称或常量名称

- 非限定名称，或不包含前缀的类名称

```php
class foo {
    static function staticmethod() {}
}
$a=new foo();
$b=foo::staticmethod();
// 如果当前命名空间是 currentnamespace, foo 将被解析为 currentnamespace\foo
// 如果使用 foo 的代码是全局的，不包含在任何命名空间中的代码，则 foo 会被解析为foo
```

- 限定名称,或包含前缀的名称

```php
class foo {
    static function staticmethod() {}
}
$a=new subnamespace\foo();
$b=subnamespace\foo::staticmethod();
// 如果当前的命名空间是 currentnamespace，则 foo 会被解析为 currentnamespace\subnamespace\foo
// 如果使用 foo 的代码是全局的，不包含在任何命名空间中的代码，foo 会被解析为subnamespace\foo
```

- 完全限定名称，或包含了全局前缀操作符的名称

```php
class foo {
    static function staticmethod() {}
}
$a=new \currentnamespace\foo();
$b=\currentnamespace\foo::staticmethod();
// foo 总是被解析为代码中的文字名currentnamespace\foo
// 在任意地方访问任意全局类、函数或常量，都可以使用完全限定名称，例如 \strlen() 或 \Exception 或 \INI_ALL
```

- 在动态的类名称、函数名称或常量名称中，限定名称和完全限定名称没有区别，因此其前导的反斜杠是不必要的

```php
// example.php
class classname
{
    function __construct()
    {
        echo __METHOD__,"\n";
    }
}
function funcname()
{
    echo __FUNCTION__,"\n";
}
const constname = "global";
```

```php
// index.php
namespace namespacename;
class classname
{
    function __construct()
    {
        echo __METHOD__,"\n";
    }
}
function funcname()
{
    echo __FUNCTION__,"\n";
}
const constname = "namespaced";

include 'example1.php';

$a = 'classname';
$obj = new $a; // 输出 classname::__construct
$b = 'funcname';
$b(); // 输出函数名
echo constant('constname'), "\n"; // 输出 global

/* 如果使用双引号，使用方法为 "\\namespacename\\classname"*/
$a = '\namespacename\classname';
$obj = new $a; // 输出 namespacename\classname::__construct
$b = 'namespacename\funcname';
$b(); // 输出 namespacename\funcname
$b = '\namespacename\funcname';
$b(); // 输出 namespacename\funcname
echo constant('\namespacename\constname'), "\n"; // 输出 namespaced
echo constant('namespacename\constname'), "\n"; // 输出 namespaced
```

### namespace关键字和__NAMESPACE__常量

- 常量__NAMESPACE__的值是包含当前命名空间名称的字符串。在全局的，不包括在任何命名空间中的代码，它包含一个空的字符串

```php
namespace MyProject;

echo '"', __NAMESPACE__, '"'; // 输出 "MyProject"

// 常量 __NAMESPACE__ 在动态创建名称时很有用

function get($classname)
{
    $a = __NAMESPACE__ . '\\' . $classname;
    return new $a;
}
```

- 关键字 namespace 可用来显式访问当前命名空间或子命名空间中的元素。它等价于类中的 self 操作符

```php
namespace MyProject;

use blah\blah as mine; // see "Using namespaces: importing/aliasing"

blah\mine(); // calls function blah\blah\mine()
namespace\blah\mine(); // calls function MyProject\blah\mine()
```

### 使用命名空间：别名/导入

- 为类名称使用别名
- 为命名空间名称使用别名
- 别名是通过操作符 use 来实现的
- 使用导入后，解析的优先策略？？？？

```php
namespace foo;
use My\Full\Classname as Another;

// 下面的例子与 use My\Full\NSname as NSname 相同
use My\Full\NSname;

// 导入一个全局类
use \ArrayObject;

$obj = new namespace\Another; // 实例化 foo\Another 对象
$obj = new Another; // 实例化 My\Full\Classname　对象
NSname\subns\func(); // 调用函数 My\Full\NSname\subns\func
$a = new ArrayObject(array(1)); // 实例化 ArrayObject 对象
// 如果不使用 "use \ArrayObject" ，则实例化一个 foo\ArrayObject 对象
```

- 导入和动态名称
  - 导入操作是在编译执行的，但动态的类名称、函数名称或常量名称则不是

```php
use My\Full\Classname as Another, My\Full\NSname;

$obj = new Another; // 实例化一个 My\Full\Classname 对象
$a = 'Another';
$obj = new $a;      // 实际化一个 Another 对象
```

- 导入和完全限定名称
  - 导入操作只影响非限定名称和限定名称
  - 完全限定名称由于是确定的，故不受导入的影响

```php
use My\Full\Classname as Another, My\Full\NSname; // 导入多个

$obj = new Another; // 实例化 My\Full\Classname 类
$obj = new \Another; // 实例化 Another 类
$obj = new Another\thing; // 实例化 My\Full\Classname\thing 类
$obj = new \Another\thing; // 实例化 Another\thing 类
```

### 使用命名空间：后备全局函数/常量

- 在一个命名空间中，当 PHP 遇到一个非限定的类、函数或常量名称时，它使用不同的优先策略来解析该名称

- 类
  - 类名称总是解析到当前命名空间中的名称
  - 在访问系统内部或不包含在命名空间中的类名称时，必须使用完全限定名称，如果不使用，且内部没有对应的类，则报错

- 函数和常量
  - 如果当前命名空间中不存在该函数或常量，PHP 会退而使用全局空间中的函数或常量

### 命名空间的顺序

- 编译时解析
  - 对完全限定名称的函数，类和常量的调用在编译时解析。如：new \A\B 解析为类 A\B
  - 所有的非限定名称和限定名称（非完全限定名称）根据当前的导入规则在编译时进行转换。例如，如果命名空间 A\B\C 被导入为 C，那么对 C\D\e() 的调用就会被转换为 A\B\C\D\e()
  - 在命名空间内部，所有的没有根据导入规则转换的限定名称均会在其前面加上当前的命名空间名称。例如，在命名空间 A\B 内部调用 C\D\e()，则 C\D\e() 会被转换为 A\B\C\D\e()
  - 非限定类名根据当前的导入规则在编译时转换（用全名代替短的导入名称）。例如，如果命名空间 A\B\C 导入为C，则 new C() 被转换为 new A\B\C()

- 运行时解析
  - 在命名空间内部（例如A\B），对非限定名称的函数调用是在运行时解析的
    - 对函数 foo() 的调用
    - 在当前命名空间中查找名为 A\B\foo() 的函数
    - 尝试查找并调用 全局(global) 空间中的函数 foo()
  - 在命名空间（例如A\B）内部对非限定名称或限定名称类（非完全限定名称）的调用是在运行时解析的
    - new C()的解析:
    - 在当前命名空间中查找A\B\C类
    - 尝试自动装载类A\B\C

```php
namespace A;
use B\D, C\E as F; // 此处的别名 D E F指的都是类

// 函数调用
foo();      // 首先尝试调用定义在命名空间"A"中的函数foo()
            // 再尝试调用全局函数 "foo"

\foo();     // 调用全局空间函数 "foo"

my\foo();   // 调用定义在命名空间"A\my"中函数 "foo"

F();        // 首先尝试调用定义在命名空间"A"中的函数 "F"，此处F指的是方法
            // 再尝试调用全局函数 "F"

// 类引用
new F();    // 使用导入规则，创建命名空间 "C" 中定义的类 "E" 的一个对象
            // 如果未找到，则尝试自动装载类 "C\E"
new \F();   // 创建定义在全局空间中的类 "F" 的一个对象
            // 如果未发现，则尝试自动装载类 "F"

// 调用另一个命名空间中的静态方法或命名空间函数
B\foo();    // 调用命名空间 "A\B" 中函数 "foo"

B::foo();   // 调用命名空间 "A" 中定义的类 "B" 的 "foo" 方法
            // 如果未找到类 "A\B" ，则尝试自动装载类 "A\B"

\B\foo();   // 调用命名空间 "B" 中的函数 "foo"

\B::foo();  // 调用全局空间中的类 "B" 的 "foo" 方法
            // 如果类 "B" 未找到，则尝试自动装载类 "B"
```

## 面向对象

- 类
  - 定义了一件事物的抽象特点。类的定义包含了数据的形式以及对数据的操作
  - 可以用一个变量来动态调用类。但该变量的值不能为关键字 self，parent 或 static
- 对象
  - 是类的实例
- 成员变量
  - 定义在类内部的变量。该变量的值对外是不可见的，但是可以通过成员函数访问，在类被实例化为对象后，该变量即可称为对象的属性
- 成员函数
  - 定义在类的内部，可用于访问对象的数据
- 继承
  - 继承性是子类自动共享父类数据结构和方法的机制，这是类之间的一种关系。在定义和实现一个类的时候，可以在一个已经存在的类的基础之上来进行，把这个已经存在的类所定义的内容作为自己的内容，并加入若干新的内容
- 父类
  - 一个类被其他类继承，可将该类称为父类，或基类，或超类
- 子类
  - 一个类继承其他类称为子类，也可称为派生类
- 多态
  - 多态性是指相同的函数或方法可作用于多种类型的对象上并获得不同的结果。不同的对象，收到同一消息可以产生不同的结果，这种现象称为多态性
- 重载
  - 就是函数或者方法有同样的名称，但是参数列表不相同的情形，这样的同名不同参数的函数或者方法之间，互相称之为重载函数或者方法
- 抽象性
  - 是指将具有一致的数据结构（属性）和行为（操作）的对象抽象成类。一个类就是这样一种抽象，它反映了与应用有关的重要性质，而忽略其他一些无关内容。任何类的划分都是主观的，但必须与具体的应用有关
- 封装
  - 是指将现实世界中存在的某个客体的属性与行为绑定在一起，并放置在一个逻辑单元内
- 构造函数
  - 主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，总与new运算符一起使用在创建对象的语句中
- 析构函数
  - 析构函数(destructor) 与构造函数相反，当对象结束其生命周期时（例如对象所在的函数已调用完毕），系统自动执行析构函数。析构函数往往用来做"清理善后" 的工作（例如在建立对象时用new开辟了一片内存空间，应在退出前在析构函数中用delete释放）

```php
class Site {
  /* 成员变量 */
  var $url; // var 声明
  var $title = "constant string"; // 可以初始化值
  
  /* 成员函数 */
  function getTitle(){
     echo $this->title . PHP_EOL; // PHP_EOL是换行符
  }
}

$google = new Site;

// 调用成员函数
$google->getTitle();
```

### 构造函数 void __construct ([ mixed $args [, $... ]] )

```php
class Site {
    // 成员变量
    var $url;
    var $title;

    // 构造函数
    function __construct( $par1, $par2 ) {
        $this->url = $par1;
        $this->title = $par2;
    }
}

$google = new Site('www.google.com', 'Google 搜索'); // 初始化对象成员变量url和title
```

### 析构函数 void __destruct ( void )

```php
class MyDestructableClass {
   function __construct() {
       print "构造函数\n";
       $this->name = "MyDestructableClass";
   }

   function __destruct() {
       print "销毁 " . $this->name . "\n";
   }
}

$obj = new MyDestructableClass();
// 构造函数
// 销毁 MyDestructableClass
```

### 继承 extends

```php
class Child extends Parent {
   // 代码部分
   // 可新加方法和属性
}
```

### 方法重写

- 如果从父类继承的方法不能满足子类的需求，可以对其进行改写，这个过程叫方法的覆盖（override），也称为方法的重写
- 就是在子类中，再声明一个要改写的方法

### 访问控制

- PHP 对属性或方法的访问控制，是通过在前面添加关键字来实现的
- public（公有）
  - 公有的类成员可以在任何地方被访问
- protected（受保护）
  - 受保护的类成员则可以被其自身以及其子类和父类访问
- private（私有）
  - 私有的类成员则只能被其定义所在的类访问

#### 属性的访问控制

- 类属性必须定义为公有，受保护，私有之一
- 如果用 var 定义，则被视为公有

```php
class MyClass {
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj = new MyClass();
echo $obj->public; // 这行能被正常执行
echo $obj->protected; // 这行会产生一个致命错误
echo $obj->private; // 这行也会产生一个致命错误
$obj->printHello(); // 输出 Public、Protected 和 Private

class MyClass2 extends MyClass {
    // 可以对 public 和 protected 进行重定义，但 private 而不能
    protected $protected = 'Protected2';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj2 = new MyClass2();
echo $obj2->public; // 这行能被正常执行
echo $obj2->private; // 未定义 private
echo $obj2->protected; // 这行会产生一个致命错误
$obj2->printHello(); // 输出 Public、Protected2 和 Undefined
```

#### 方法的访问控制

- 类中的方法可以被定义为公有，私有或受保护
- 如果没有设置这些关键字，则该方法默认为公有

### 接口

- 可以指定某个类必须实现哪些方法，但不需要定义这些方法的具体内容
- 通过 interface 关键字来定义的，就像定义一个标准的类一样，但其中定义所有的方法都是空的
- 接口中定义的所有方法都必须是公有，这是接口的特性
- 要实现一个接口，使用 implements 操作符
- 类中必须实现接口中定义的所有方法，否则会报一个致命错误
- 类可以实现多个接口，用逗号来分隔多个接口的名称

```php
// 声明一个'iTemplate'接口
interface iTemplate {
    public function setVariable($name, $var);
    public function getHtml($template);
}

// 实现接口
class Template implements iTemplate {
    private $vars = array();
  
    public function setVariable($name, $var)
    {
        $this->vars[$name] = $var;
    }
  
    public function getHtml($template)
    {
        foreach($this->vars as $name => $value) {
            $template = str_replace('{' . $name . '}', $value, $template);
        }

        return $template;
    }
}
```

### 抽象类

- 任何一个类，如果它里面至少有一个方法是被声明为抽象的，那么这个类就必须被声明为抽象的
- 通过 abstract 关键字声明
- 定义为抽象的类不能被实例化
- 被定义为抽象的方法只是声明了其调用方式（参数），不能定义其具体的功能实现
- 继承一个抽象类的时候，子类必须定义父类中的所有抽象方法
  - 这些方法的访问控制必须和父类中一样（或者更为宽松）
  - 例如某个抽象方法被声明为受保护的，那么子类中实现的方法就应该声明为受保护的或者公有的，而不能定义为私有的

```php
abstract class AbstractClass {
 // 强制要求子类定义这些方法
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);

    // 普通方法（非抽象方法）
    public function printOut() {
        print $this->getValue() . PHP_EOL;
    }
}

class ConcreteClass1 extends AbstractClass {

    protected function getValue() {
        return "ConcreteClass1";
    }

    // 子类方法可以包含父类抽象方法中不存在的可选参数
    public function prefixValue($prefix, $separator = ".") {
        return "{$prefix}ConcreteClass1{$separator}";
    }
}
```

### Static 关键字

- 声明类属性或方法为 static(静态)，就可以不实例化类而直接访问
- 静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）
- 由于静态方法不需要通过对象即可调用，所以伪变量 $this 在静态方法中不可用
- 静态属性不可以由对象通过 -> 操作符来访问

```php
class Foo {
  public static $my_static = 'foo';
  
  public function staticValue() {
     return self::$my_static;
  }
}

print Foo::$my_static . PHP_EOL;
$foo = new Foo();

print $foo->staticValue() . PHP_EOL;
```

### Final 关键字

- 如果父类中的方法被声明为 final，则子类无法覆盖该方法
- 如果一个类被声明为 final，则不能被继承

```php
final class BaseClass {

    public function test() {
        echo "BaseClass::test() called" . PHP_EOL;
    }

    final public function moreTesting() {
        echo "BaseClass::moreTesting() called"  . PHP_EOL;
    }
}
```

## 表单

### 下拉菜单单选

```php
<?php
$q = isset($_GET['q'])? htmlspecialchars($_GET['q']) : '';
if($q) {
        if($q =='GOOGLE') {
                echo 'Google 搜索<br>http://www.google.com';
        } else if($q =='TAOBAO') {
                echo '淘宝<br>http://www.taobao.com';
        }
} else {
?>
<form action="" method="get"> // action 属性值为空表示提交到当前脚本
    <select name="q">
    <option value="">选择一个站点:</option>
    <option value="GOOGLE">Google</option>
    <option value="TAOBAO">Taobao</option>
    </select>
    <input type="submit" value="提交">
    </form>
<?php
}
?>
```

### 下拉菜单多选

- 设置 select name="q[]" 以数组的方式获取

```php
<?php
$q = isset($_POST['q'])? $_POST['q'] : '';
if(is_array($q)) {
    $sites = array(
            'GOOGLE' => 'Google 搜索: http://www.google.com',
            'TAOBAO' => '淘宝: http://www.taobao.com',
    );
    foreach($q as $val) {
        // PHP_EOL 为常量，用于换行
        echo $sites[$val] . PHP_EOL;
    }

} else {
?>
<form action="" method="post">
    <select multiple="multiple" name="q[]">
    <option value="">选择一个站点:</option>
    <option value="GOOGLE">Google</option>
    <option value="TAOBAO">Taobao</option>
    </select>
    <input type="submit" value="提交">
    </form>
<?php
}
?>
```

### 单选按钮表单

- 单选按钮表单中 name 属性的值是一致的，value 值是不同的

```php
<?php
$q = isset($_GET['q'])? htmlspecialchars($_GET['q']) : '';
if($q) {
        if($q =='GOOGLE') {
                echo 'Google 搜索<br>http://www.google.com';
        } else if($q =='TAOBAO') {
                echo '淘宝<br>http://www.taobao.com';
        }
} else {
?><form action="" method="get">
    <input type="radio" name="q" value="GOOGLE" />Google
    <input type="radio" name="q" value="TAOBAO" />Taobao
    <input type="submit" value="提交">
</form>
<?php
}
?>
```

### checkbox 复选框

```php
<?php
$q = isset($_POST['q'])? $_POST['q'] : '';
if(is_array($q)) {
    $sites = array(
            'GOOGLE' => 'Google 搜索: http://www.google.com',
            'TAOBAO' => '淘宝: http://www.taobao.com',
    );
    foreach($q as $val) {
        // PHP_EOL 为常量，用于换行
        echo $sites[$val] . PHP_EOL;
    }

} else {
?><form action="" method="post">
    <input type="checkbox" name="q[]" value="GOOGLE"> Google<br>
    <input type="checkbox" name="q[]" value="TAOBAO"> Taobao<br>
    <input type="submit" value="提交">
</form>
<?php
}
?>
```

### 表单验证

- htmlspecialchars() 函数把一些预定义的字符转换为 HTML 实体
  - 预定义的字符：
    - & （和号） 成为 &amp;
    - " （双引号） 成为 &quot;
    - ' （单引号） 成为 &#039;
    - < （小于） 成为 &lt;
    - > （大于） 成为 &gt;
- htmlspecialchars()可以防止XSS及CSS攻击，往地址中插入的script标签将被html实体化
- 当用户提交表单时
  - 使用 PHP trim() 函数去除用户输入数据中不必要的字符 (如：空格，tab，换行)
  - 使用 PHP stripslashes()函数去除用户输入数据中的反斜杠 (\)
- 通过$_SERVER["REQUEST_METHOD"]来检测表单是否被提交
- preg_match(pattern, subject[, matches])
  - 搜索subject与pattern给定的正则表达式的一个匹配
  - pattern
    - 要搜索的模式，字符串类型(正则表达式)
  - subject
    - 输入的字符串
  - matches
    - 如果提供了参数matches，它将被填充为搜索结果。 $matches[0]将包含完整模式匹配到的文本(即subject值)， $matches[1] 将包含第一个捕获子组匹配到的文本，以此类推
  - preg_match()返回 pattern 的匹配次数。 它的值将是0次（不匹配）或1次，因为preg_match()在第一次匹配后 将会停止搜索
  - 如果发生错误preg_match()返回 FALSE
  - preg_match_all()不同于此，它会一直搜索subject 直到到达结尾

```php
// 尽量提交表单到当前页面，方便
<form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">
```

## 多维数组

```php
$sites = array (
    "google"=>array (
        "Google 搜索",
        "http://www.google.com"
    ),
    "taobao"=>array (
        "淘宝",
        "http://www.taobao.com"
    )
);
```

## data( $format [, $timestamp] ) 函数

- 格式化本地日期和时间，并返回格式化的日期字符串
- timestamp
  - 规定时间戳。默认是当前的日期和时间

```php
// 设置时区
date_default_timezone_set("PRC");

$time = strtotime("2018-01-18 08:08:08"); // 将指定日期转成时间戳

echo date("Y-m-d H:i:s", $time); // 2018-01-18 08:08:08
```

## 包含文件

- include 和 require 除了处理错误的方式不同之外，在其他方面都是相同的
  - require 生成一个致命错误（E_COMPILE_ERROR），在错误发生后脚本会停止执行
  - include 生成一个警告（E_WARNING），在错误发生后脚本会继续执行
- 使用区别
  - require 一般放在 PHP 文件的最前面，程序在执行前就会先导入要引用的文件
  - include 一般放在程序的流程控制中，当程序执行时碰到才会引用，简化程序的执行流程

## 文件处理

### fopen(filename,mode[,include_path,context]) 打开文件或者 URL， 如果打开失败，本函数返回 FALSE

- filename
  - 规定要打开的文件或 URL
- mode
  - 规定要求到该文件/流的访问类型
- include_path
  - 如果也需要在 include_path 中检索文件的话，可以将该参数设为 1 或 TRUE
- context
  - 规定文件句柄的环境。Context 是可以修改流的行为的一套选项
- mode值
  - "r"   只读方式打开，将文件指针指向文件头
  - "r+"  读写方式打开，将文件指针指向文件头
  - "w"   写入方式打开，将文件指针指向文件头并将文件大小截为零。如果文件不存在则尝试创建
  - "w+"  读写方式打开，将文件指针指向文件头并将文件大小截为零。如果文件不存在则尝试创建
  - "a"   写入方式打开，将文件指针指向文件末尾。如果文件不存在则尝试创建
  - "a+"  读写方式打开，将文件指针指向文件末尾。如果文件不存在则尝试创建
  - "x"   创建并以写入方式打开，将文件指针指向文件头。如果文件已存在，则 fopen() 调用失败并返回 FALSE，并生成一条 E_WARNING 级别的错误信息。如果文件不存在则尝试创建
  - "x+"  创建并以读写方式打开，将文件指针指向文件头。如果文件已存在，则 fopen() 调用失败并返回 FALSE，并生成一条 E_WARNING 级别的错误信息。如果文件不存在则尝试创建

```php
$file=fopen("welcome.txt","r");

// 如果 fopen() 函数不能打开指定的文件，实例会生成一段消息
$file=fopen("welcome.txt","r") or exit("Unable to open file!");
```

### fclose(file) 函数关闭一个打开文件

- file 是一个文件指针,规定要关闭的文件。fclose() 函数关闭该指针指向的文件
- 成功则返回 true，否则返回 false
- 文件指针必须有效，并且是通过 fopen() 或 fsockopen() 成功打开的

```php
$file = fopen("test.txt","r");

fclose($file);
```

### feof(file) 函数检测是否已到达文件末尾（EOF）

- file 是一个文件指针
- 在 w 、a 和 x 模式下，无法读取打开的文件
- 返回boolean

```php
if (feof($file)) echo "end";
```

### fgets(file) 函数用于从文件中逐行读取文件

- 在调用该函数之后，文件指针会移动到下一行

```php
$file = fopen("welcome.txt", "r") or exit("无法打开文件!");
// 读取文件每一行，直到文件结尾
while(!feof($file))
{
    echo fgets($file). "<br>";
}
fclose($file);
```

### fgetc(file) 函数用于从文件中逐字符地读取文件

- 在调用该函数之后，文件指针会移动到下一个字符

```php
$file=fopen("welcome.txt","r") or exit("无法打开文件!");
while (!feof($file))
{
    echo fgetc($file);
}
fclose($file);
```

### fgetcsv(file) 函数用于从csv文本读入后转为数组存储在变量中

- 需要处理的数据比较少时可以使用逗号分隔符文件(*.csv)（这是一类文本文件）存储数据更加便利
- 注意逗号是半角英文
- CSV文本编码必须和HTML的编码相同，否则用php写到HTML中，用户会看到乱码。也可以使用 iconv 转码函数进行转码

```php
$fh=fopen("a.csv","r");//这里我们只是读取数据，所以采用只读打开文件流
$arr=fgetcsv($fh);//这个函数就是读取CSV文件的函数，他把文本读入后转为数组存储在$arr中
fclose($fh);
foreach($arr as $key=>$value){echo $value;}//循环输出所有的值
```

## 文件上传

- enctype 属性规定了在提交表单时要使用哪种内容类型。在表单需要二进制数据时，比如文件内容，请使用 "multipart/form-data"
- 全局数组 $_FILES[][] 存储了上传文件的信息
  - 第一个[]填入input的name值
  - $_FILES["file"]["name"] 上传文件的名称
  - $_FILES["file"]["type"] 上传文件的类型
  - $_FILES["file"]["size"] 上传文件的大小，以字节计
  - $_FILES["file"]["tmp_name"] 存储在服务器的文件的临时副本的名称
  - $_FILES["file"]["error"] 由文件上传导致的错误代码

```php
<form action="upload_file.php" method="post" enctype="multipart/form-data">
    <label for="file">文件名：</label>
    <input type="file" name="file" id="file"><br>
    <input type="submit" name="submit" value="提交">
</form>
```

- 上传脚本 upload_file.php

```php
// 允许上传的图片后缀
$allowedExts = array("gif", "jpeg", "jpg", "png");
$temp = explode(".", $_FILES["file"]["name"]);
echo $_FILES["file"]["size"];
$extension = end($temp);     // 获取文件后缀名
if ((($_FILES["file"]["type"] == "image/gif")
|| ($_FILES["file"]["type"] == "image/jpeg")
|| ($_FILES["file"]["type"] == "image/jpg")
|| ($_FILES["file"]["type"] == "image/pjpeg")
|| ($_FILES["file"]["type"] == "image/x-png")
|| ($_FILES["file"]["type"] == "image/png"))
&& ($_FILES["file"]["size"] < 204800)   // 小于 200 kb
&& in_array($extension, $allowedExts))
{
    if ($_FILES["file"]["error"] > 0)
    {
        echo "错误：: " . $_FILES["file"]["error"] . "<br>";
    }
    else
    {
        echo "上传文件名: " . $_FILES["file"]["name"] . "<br>";
        echo "文件类型: " . $_FILES["file"]["type"] . "<br>";
        echo "文件大小: " . ($_FILES["file"]["size"] / 1024) . " kB<br>";
        echo "文件临时存储的位置: " . $_FILES["file"]["tmp_name"] . "<br>";

        // 判断当期目录下的 upload 目录是否存在该文件
        // 如果没有 upload 目录，你需要创建它，upload 目录权限为 777
        if (file_exists("upload/" . $_FILES["file"]["name"]))
        {
            echo $_FILES["file"]["name"] . " 文件已经存在。 ";
        }
        else
        {
            // 如果 upload 目录不存在该文件则将文件上传到 upload 目录下
            move_uploaded_file($_FILES["file"]["tmp_name"], "upload/" . $_FILES["file"]["name"]);
            echo "文件存储在: " . "upload/" . $_FILES["file"]["name"];
        }
    }
}
else
{
    echo "非法的文件格式";
}
```

## Cookie

- cookie 常用于识别用户
- cookie 是一种服务器留在用户计算机上的小文件
- 每当同一台计算机通过浏览器请求页面时，这台计算机将会发送 cookie

### setcookie(name, value, expire, path, domain) 设置cookie

- setcookie() 函数必须位于 `<html>` 标签之前。
- 在发送 cookie 时，cookie 的值会自动进行 URL 编码，在取回时进行自动解码。为防止 URL 编码，请使用 setrawcookie() 取而代之

```php
setcookie("user", "jack", time()+3600);
```

### $_COOKIE 变量用于取回 cookie 的值

- 所有cookie的集合
- echo $_COOKIE["user"]  打印名为user的cookie

### 删除cookie

- 当删除 cookie 时，您应当使过期日期变更为过去的时间点
- setcookie("user", "", time()-3600)

## session

- session 变量用于存储关于用户会话（session）的信息，或者更改用户会话（session）的设置
- Session 变量存储单一用户的信息，并且对于应用程序中的所有页面都是可用的
- 工作机制是：为每个访客创建一个唯一的 id (UID)，并基于这个 UID 来存储变量。UID 存储在 cookie 中，或者通过 URL 进行传导
- 把用户信息存储到 PHP session 中之前，首先必须启动会话 session_start()
- 存储/获取 Session 变量
  - $_SESSION 变量
- 销毁 Session 变量
  - unset() 函数用于释放指定的 session 变量 unset($_SESSION['views'])
  - session_destroy() 函数彻底销毁 session, 它将重置 session，将失去所有已存储的 session 数据

```php
// session_start() 函数必须位于 <html> 标签之前
// 代码会向服务器注册用户的会话，以便可以开始保存用户信息，同时会为用户会话分配一个 UID
<?php
session_start();
// 存储 session 数据
$_SESSION['views']=1;
?>
<html>
<body>

</body>
</html>
<?php
// 检索 session 数据
echo "浏览量：". $_SESSION['views'];
?>
```

## mail() 函数

- 用于从脚本中发送电子邮件
- mail(to,subject,message[,headers][,parameters])
  - to: 规定email的接收者
  - subject: 规定email的主题,该参数不能包含任何新行字符
  - message：定义要发送的消息, 应使用 LF (\n) 来分隔各行。每行应该限制在 70 个字符内。
  - headers: 规定附加的标题，比如 From、Cc 和 Bcc。应当使用 CRLF (\r\n) 分隔附加的标题
  - parameters: 对邮件发送程序规定额外的参数
- PHP 运行邮件函数需要一个已安装且正在运行的邮件系统(如：sendmail、postfix、qmail等)。所用的程序通过在 php.ini 文件中的配置设置进行定义
- 使用mail()函数，需要用过滤器过滤和验证表单提交的数据
  - FILTER_SANITIZE_EMAIL 过滤器从字符串中删除电子邮件的非法字符
  - FILTER_VALIDATE_EMAIL 过滤器验证电子邮件地址的值
  - 详见php过滤器章节

## 错误处理

### die(string)

- 终止程序，并输出字符串

### 创建自定义错误处理器

- 在 PHP 中发生错误时调用该函数
- error_function(error_level, error_message[, error_file][, error_line][, error_context])
  - error_level: 为用户定义的错误规定错误报告级别。必须是一个数字。
  - error_message: 为用户定义的错误规定错误消息
  - error_file: 规定错误发生的文件名
  - error_line: 规定错误发生的行号
  - error_context: 规定一个数组，包含了当错误发生时在用的每个变量以及它们的值

#### 错误报告级别 error_level

- 这些错误报告级别是用户自定义的错误处理程序处理的不同类型的错误
- 值为2，常量为E_WARNING
  - 非致命的 run-time 错误。不暂停脚本执行
- 值为8，常量为E_NOTICE
  - run-time 通知。在脚本发现可能有错误时发生，但也可能在脚本正常运行时发生
- 值为256，常量为E_USER_ERROR
  - 致命的用户生成的错误。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_ERROR
- 值为512，常量为E_USER_WARNING
  - 非致命的用户生成的警告。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_WARNING
- 值为1024，常量为E_USER_NOTICE
  - 用户生成的通知。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_NOTICE
- 值为4096，常量为E_RECOVERABLE_ERROR
  - 可捕获的致命错误。类似 E_ERROR，但可被用户定义的处理程序捕获。（参见 set_error_handler()）
- 值为8191，常量为E_ALL
  - 所有错误和警告。（在 PHP 5.4 中，E_STRICT 成为 E_ALL 的一部分）

### 设置错误处理程序

- set_error_handler(error_function[, error_types])
  - 函数设置用户自定义的错误处理函数
  - 该函数会返回旧的错误处理程序，若失败，则返回 null
  - 如果使用了该函数，会完全绕过标准的 PHP 错误处理函数，如果必要，用户定义的错误处理程序必须终止 ( die() ) 脚本
  - 如果在脚本执行前发生错误，由于在那时自定义程序还没有注册，因此就不会用到这个自定义错误处理程序。
  - error_function: 规定发生错误时运行的函数
  - error_types: 规定在哪个错误报告级别会显示用户定义的错误。默认是 "E_ALL"

```php
// 错误处理函数
function customError($errno, $errstr)
{
    echo "<b>Error:</b> [$errno] $errstr";
}

// 设置错误处理函数
set_error_handler("customError");

// 触发变量不存在的错误
echo($test);
```

- set_error_handler()不仅可以接受函数，还可以接受 类的方法（公开的静态方法 及 公开的非静态方法 都可以），但需要以 数组形式 传递，数组的第一值为“类名”，第二个参数为“方法名”
  - set_error_handler(array("App","customError"));

### 触发错误

- 在脚本中用户输入数据的位置，当用户的输入无效时触发错误是很有用的
- trigger_error(error_message[, error_level]) 手动触发并输出
  - error_message 触发错误时的提示内容
  - error_level 规定所触发的错误级别
    - E_USER_NOTICE：默认。用户生成的 run-time 通知。在脚本发现可能有错误时发生，但也可能在脚本正常运行时发生
    - E_USER_WARNING：非致命的用户生成的 run-time 警告。脚本执行不被中断
    - E_USER_ERROR：致命的用户生成的 run-time 错误。错误无法恢复。脚本执行被中断

```php
// 错误处理函数
function customError($errno, $errstr)
{
    echo "<b>Error:</b> [$errno] $errstr<br>";
    echo "脚本结束";
    die();
}

// 设置错误处理函数
set_error_handler("customError",E_USER_WARNING);

// 触发错误
$test=2;
if ($test>1)
{
    trigger_error("变量值必须小于等于 1",E_USER_WARNING);
}

// Error: [512] 变量值必须小于等于 1
// 脚本结束
```

### 错误记录

- 在默认的情况下，根据在 php.ini 中的 error_log 配置，PHP 向服务器的记录系统或文件发送错误记录
- 通过使用 error_log() 函数, 可以向指定的文件或远程目的地发送错误记录
- 通过电子邮件向您自己发送错误消息，是一种获得指定错误的通知的好办法

```php
// 如果特定的错误发生，我们将发送带有错误消息的电子邮件，并结束脚本
// 这个方法不适合所有的错误。常规错误应当通过使用默认的 PHP 记录系统在服务器上进行记录

// 错误处理函数
function customError($errno, $errstr)
{
    echo "<b>Error:</b> [$errno] $errstr<br>";
    echo "已通知网站管理员";
    error_log("Error: [$errno] $errstr",1,
    "someone@example.com","From: webmaster@example.com");
}

// 设置错误处理函数
set_error_handler("customError",E_USER_WARNING);

// 触发错误
$test=2;
if ($test>1)
{
    trigger_error("变量值必须小于等于 1",E_USER_WARNING);
}

// Error: [512] 变量值必须小于等于 1
// 已通知网站管理员

// 邮件显示：
// Error: [512] 变量值必须小于等于 1
```

## 异常处理

- 异常处理用于在指定的错误（异常）情况发生时改变脚本的正常流程。这种情况称为异常
- 如果抛出了异常，就必须捕获它
- 当异常被触发时，通常会发生：
  - 当前代码状态被保存
  - 代码执行被切换到预定义（自定义）的异常处理器函数
  - 根据情况，处理器也许会从保存的代码状态重新开始执行代码，终止脚本执行，或从代码中另外的位置继续执行脚本

### 异常的基本使用

- 当异常被抛出时，其后的代码不会继续执行，PHP 会尝试查找匹配的 "catch" 代码块
- 如果异常没有被捕获，而且又没用使用 set_exception_handler() 作相应的处理的话，那么将发生一个严重的错误（致命错误），并且输出 "Uncaught Exception" （未捕获异常）的错误消息
- throw new Exception("Value must be 1 or below"); 抛出异常 msg为 “Value must be 1 or below”
- Try - 使用异常的函数应该位于 "try" 代码块内。如果没有触发异常，则代码将照常继续执行。但是如果异常被触发，会抛出一个异常
- Throw - 规定如何触发异常。每一个 "throw" 必须对应至少一个 "catch"
- Catch - "catch" 代码块会捕获异常，并创建一个包含异常信息的对象

```php
// 创建一个有异常处理的函数
function checkNum($number)
{
    if($number>1)
    {
        throw new Exception("变量值必须小于等于 1");
    }
        return true;
}

// 在 try 块 触发异常
try
{
    checkNum(2);
    // 如果抛出异常，以下文本不会输出
    echo '如果输出该内容，说明 $number 变量';
}
// 捕获异常
// "catch" 代码块接收到该异常，并创建一个包含异常信息的对象 ($e)
catch(Exception $e)
{
    echo 'Message: ' .$e->getMessage();
}
```

### 创建一个自定义的 Exception 类

- 自定义的类继承了 PHP 的 exception 类的所有属性，可向其添加自定义的函数
- Exception方法：getLine() getFile() getMessage()

```php
class customException extends Exception
{
    public function errorMessage()
    {
        // 错误信息
        $errorMsg = '错误行号 '.$this->getLine().' in '.$this->getFile()
        .': <b>'.$this->getMessage().'</b> 不是一个合法的 E-Mail 地址';
        return $errorMsg;
    }
}

$email = "someone@example.com";

// 抛出多个异常，一个异常对应一个catch
try
{
    // 检测邮箱
    if(filter_var($email, FILTER_VALIDATE_EMAIL) === FALSE)
    {
        // 如果是个不合法的邮箱地址，抛出异常
        throw new customException($email);
    }
    // 检测 "example" 是否在邮箱地址中
    if(strpos($email, "example") !== FALSE)
    {
        throw new Exception("$email 是 example 邮箱");
    }
}
catch (customException $e)
{
    echo $e->errorMessage();
}
catch(Exception $e)
{
    echo $e->getMessage();
}
```

### 重新抛出异常

- 当异常被抛出时，希望以不同于标准的方式对它进行处理。可以在一个 "catch" 代码块中再次抛出异常
- 如果在当前的 "try" 代码块中异常没有被捕获，则它将在更高层级上查找 catch 代码块

```php
class customException extends Exception
{
    public function errorMessage()
    {
        // 错误信息
        $errorMsg = $this->getMessage().' 不是一个合法的 E-Mail 地址。';
        return $errorMsg;
    }
}

$email = "someone@example.com";

try
{
    try
    {
        // 检测 "example" 是否在邮箱地址中
        if(strpos($email, "example") !== FALSE)
        {
            // 如果是个不合法的邮箱地址，抛出异常
            throw new Exception($email);
        }
    }
    catch(Exception $e)
    {
        // 重新抛出异常
        throw new customException($email);
    }
}
catch (customException $e)
{
    // 显示自定义信息
    echo $e->errorMessage();
}
```

### 设置顶层异常处理器

- set_exception_handler() 函数可设置处理所有未捕获异常的用户定义函数

```php
// 代码中，不存在 "catch" 代码块，而是触发顶层的异常处理程序。应该使用此函数来捕获所有未被捕获的异常
function myException($exception)
{
    echo "<b>Exception:</b> " , $exception->getMessage();
}

set_exception_handler('myException');

throw new Exception('Uncaught Exception occurred'); // Exception: Uncaught Exception occurred
```

## PHP 过滤器

- PHP 过滤器用于验证和过滤来自非安全来源的数据
- 应该始终对外部数据进行过滤！
- 输入过滤是最重要的应用程序安全课题之一
- 外部数据：
  - 来自表单的输入数据
  - Cookies
  - Web services data
  - 服务器变量
  - 数据库查询结果

### 变量过滤

- filter_var()
  - 通过一个指定的过滤器来过滤单一的变量
  - filter_var($int, FILTER_VALIDATE_INT) 使用 "FILTER_VALIDATE_INT" 过滤器来过滤变量，如果$int不是整数则返回 FALSE
- filter_var_array()
  - 通过相同的或不同的过滤器来过滤多个变量
- filter_input
  - 获取一个输入变量，并对它进行过滤
- filter_input_array
  - 获取多个输入变量，并通过相同的或不同的过滤器对它们进行过滤

### 两种过滤器 Validating 和 Sanitizing

- Validating 过滤器
  - 用于验证用户输入
  - 严格的格式规则（比如 URL 或 E-Mail 验证）
  - 如果成功则返回预期的类型，如果失败则返回 FALSE
- Sanitizing 过滤器
  - 用于允许或禁止字符串中指定的字符
  - 无数据格式规则
  - 始终返回字符串
