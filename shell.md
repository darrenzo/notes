# shell

- 一般情况下, 不区分 Bourne Shell 和 Bourne Again Shell, #!/bin/sh 同样也可以改为 #!/bin/bash。

## 执行脚本的四种方法

### 相对路径 (sh文件需要可执行权限)

```shell
./hello.sh
```

- ./ 的意思是说在当前的工作目录下执行hello.sh。如果不加上 ./，bash可能会因找不到相应hello.sh而报错，因为不在环境变量PASH中（echo $PATH）

### 绝对路径 (sh文件需要可执行权限)

- /data/shell/hello.sh

### 使用 bash 或者 sh 命令 (sh 文件 不需要可执行权限)

- bash hello.sh
- sh hello.sh
- 可以不必事先设定shell的执行权限，甚至都不用写shell文件中的第一行（指定bash路径）
  - 将hello.sh作为参数传给sh(bash)命令来执行的。这时不是hello.sh自己来执行，而是被人家调用执行，所以不要执行权限

### 在当前的shell环境中执行bash shell脚本

- . hello.sh
- source hello.sh
- 不创建子shell，在当前shell环境下读取并执行脚本中的命令，相当于顺序执行脚本里面的命令
  - 前三种方法执行shell脚本时都是在当前shell（称为父shell）开启一个子shell环境，此shell脚本就在这个子shell环境中执行。shell脚本执行完后子shell环境随即关闭，然后又回到父shell中
- 子Shell从父Shell继承得来的属性如下：
  - 当前工作目录
  - 环境变量
  - 标准输入、标准输出和标准错误输出
  - 所有已打开的文件标识符
- 子Shell不能从父Shell继承的属性(除非 父shell的变量 使用export声明为当前环境变量)：
  - 除环境变量和.bashrc文件中定义变量之外的Shell变量

## 变量

- 变量命名格式类似js变量
- your_name="runoob"，变量名和等号之间不能有空格
- shell中所有变量都定义为字符串，且变量名称只能是英文字母与数字，但是数字不能是开头字符
- 若有空格符可以使用双引号  "  或单引号  '  来将变量内容结合起来，但须要特别留意， 双引号内的特殊字符可以保有变量特性，但是单引号内的特殊字符则仅为一般字符
  - 即 "$ab" 是取变量ab，而'$ab' 就是一个字符串
- 双引号内转义还是用\
- 定义变量时，变量名不加美元符号（$，PHP语言中变量需要）
- 只有使用变量时需要加$,  $name或者${name}。  第二次赋值的时候也不能写$your_name="alibaba"，只有使用时才能写。
- readonly myUrl，readonly将变量myUrl变为只读,值不能改变，不能删除
- unset variable_name，删除变量，但不能删除只读变量，变量名不加$

```shell
    for file in `ls /etc` # 为什么字段会自动识别为命令？？？？
    或
    for file in $(ls /etc)
    # 用语句给变量赋值，此处将/etc下目录的文件名循环出来
```

### 变量类型

- 全局变量
  - 在当前shell进程中都有效（子进程或父进程无效）
  - 在 Shell 中定义的变量，默认就是全局变量
  - 在 Shell 函数中定义的变量默认也是全局变量，它和在函数外部定义变量拥有一样的效果
- 局部变量
  - 函数内部，可以在定义变量时加上local命令，此时该变量就成了局部变量
  - local a=99
- 环境变量
  - 通过export生成环境变量，在父子关系的shell进程中都可以访问到
  - 只能向下传递而不能向上传递，即“传子不传父”

## 字符串

- 字符串可以用单引号，也可以用双引号，也可以不用引号

### 单引号

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单引号（对单引号使用转义符后也不行）。

### 双引号

- 双引号里可以有变量
- 双引号里可以出现转义字符

```shell
    your_name="qinjx"
    greeting="hello, "$your_name" !"
    greeting_1="hello, ${your_name} !"
    # 都行
    # 反引号里不能用${}取值写法，只能用 `  "$变量名"  `
```

## 输出字符串总结

```shell
            能否引用变量  能否引用转义符  能否引用文本格式（换行符、制表符等等）
    单引号      -              -                    -
    双引号      能             能                   能
    无引号      能             能                   -
```

### 反引号

- 括起来的字符串被shell解释为命令行，在执行时，shell首先执行命令行，执行结果会取代包括反引号在内的部分

### 获取字符串长度

```shell
    string="abcd"
    echo ${#string}
    # 4
```

### 提取子字符串

- 序号从0开始

```shell
    string="runoob is a great site"
    echo ${string:1:4}
    # unoo
```

### 查找字符串

- 序号从1开始

```shell
    string="runoob is a great company"
    echo `expr index "$string" run`
    # 输出1
    # 查找字符 "run" 的位置
```

## echo 命令

- echo "It is a test" 与  echo It is a test 相同，可省略双引号
- echo "\"It is a test\"" 显示转义字符，结果是 "It is a test"
- 显示换行

```shell
    # -e 开启转义  \n换行符
    echo -e "OK! \n"
    echo "It it a test"
    # OK!
    # It it a test

    # -e 开启转义 \c 不换行
    echo -e "OK! \c"
    echo "It is a test"
    # OK! It is a test
```

- 显示结果定向至文件  echo "It is a test" > myfile
- 原样输出字符串，不进行转义或取变量(用单引号)

```shell
    echo '$name\"'
    $name\"
```

- 显示命令执行结果

```shell
    echo `date`
    # Thu Jul 24 10:08:46 CST 2014
```

## printf 命令

- printf  format-string  [arguments...]   format-string: 为格式控制字符串，arguments: 为参数列表。

```shell
    printf "Hello, Shell\n"
    printf "Hello, Shell2\n"
    # 两个printf不会自动换行，得需要加\n

    # format-string为双引号或者单引号，效果一样
    printf "%d %s\n" 1 "abc"   #  1 abc

    # format-string为无引号，效果一样，但不能引用文本格式，会当成转义符
    printf %s\n abcdef #  nabcdef

    # 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
    printf %s abc def  # abcdef
    printf "%s\n" abc def
    #  abc
    #  def
    printf "%s %s\n" a b c d
    #  ab
    #  cd
    # 如果没有 arguments，那么 %s 用NULL代替，%d 用 0 代替
    printf "%s and %d \n"  #     and 0

    printf "a string, no processing:<%s>\n" "A\nB"
    #  a string, no processing:<A\nB>

    printf "a string, no processing:<%b>\n" "A\nB"
    # a string, no processing:<A
    # B>

    $ printf "www.runoob.com \a"
    # www.runoob.com $  
```

- %d %s %c %f 格式替代符
  - d: Decimal 十进制整数 -- 对应位置参数必须是十进制整数，否则报错！
  - s: String 字符串 -- 对应位置参数必须是字符串或者字符型，否则报错！
  - c: Char 字符 -- 对应位置参数必须是字符串或者字符型，否则报错！
  - f: Float 浮点 -- 对应位置参数必须是数字型，否则报错！

```shell
    printf "%d %s %c\n" 1 "abc" "def"
    # 1 abc d
    # 其中最后一个参数是 "def"，%c 自动截取字符串的第一个字符作为结果输出。
```

## shell数组

- 只支持一维数组，不限定大小
- 用括号来表示数组，数组元素用"空格"符号分割开：数组名=(值1 值2 ... 值n)

```shell
    array_name[0]=value0
    # 定义分量
```

### 读取数组 ${数组名[下标]}

- valuen=${array_name[n]}
- echo ${array_name} 默认输出第一个元素
- @符号可以获取数组中所有的元素 ${array_name[@]}  或者  ${array_name[*]}
- echo ${!array_name[@]} 输出数组所有的下标

### 获取数组的长度

- 取得整个数组的长度 length=${#array_name[@]} 或者 length=${#array_name[*]}
- 取得数组单个元素的长度 lengthn=${#array_name[n]}

## shell注释

- #（一个或多个）开头 + 空格 + 注释     shell只有单行注释，每行加#
- 多行注释可以用花括号把要注释的代码括起来，定义成函数，只要没有地方调用，函数就不会执行，相当于注释

```shell
    # 多行注释的另外格式
    :<<EOF
    注释内容...
    注释内容...
    注释内容...
    EOF

    # EOF可以使用其他符号 会出警告
    :<<'
    注释内容...
    注释内容...
    注释内容...
    '

    :<<!
    注释内容...
    注释内容...
    注释内容...
    !
```

## shell 传递参数

- 执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：$n， n 代表一个第几个参数。 $0为执行的文件名
  - $#  传递到脚本的参数个数
  - `$*`以一个单字符串显示所有向脚本传递的参数。 如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。相当于一个字符串
  - `$@` 与`$*`相同，但是使用时加引号，并在引号中返回每个参数。如`"$@"`用「"」括起来的情况、以`"$1" "$2" … "$n"`的形式输出所有参数。假设在脚本运行时写了三个参数 1、2、3, 则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）
  - `$$`  脚本运行的当前进程ID号
  - `$!`  后台运行的最后一个进程的ID号
  - `$-`  显示Shell使用的当前选项，与set命令功能相同。
  - `$?`  显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。

## shell 基本运算符

### 算术运算符

- 原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用
- val=`expr 2 + 2`  得使用反引号，且数值与运算符之间需要有空格： 2 + 2 要有空格，不能2+2
- 条件表达式要放在方括号之间，并且要有空格 if [ $a == $b ]
- 运算符 `*` 乘号前一定要加斜杠转义 `\*`

```shell
    # 条件语句
    if [ $a != $b ]
    then
        echo "a 不等于 b"
    fi
```

- 在MAC中shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 。
- 算术运算符： + - * / % = == !=  （后三位为 赋值 相等 不等）

### 关系运算符

- 关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
- -eq  检测两个数是否相等，相等返回 true。 [ $a -eq $b ] 返回 false。
- -ne  检测两个数是否不相等，不相等返回 true。
- -gt  检测左边的数是否大于右边的，如果是，则返回 true。
- -lt  检测左边的数是否小于右边的，如果是，则返回 true。
- -ge  检测左边的数是否大于等于右边的，如果是，则返回 true。
- -le  检测左边的数是否小于等于右边的，如果是，则返回 true。

### 布尔运算符

- ！  非运算，表达式为 true 则返回 false，否则返回 true。 [ ! false ] 返回 true。
- -o  或运算，有一个表达式为 true 则返回 true。 [ $a -lt 20 -o $b -gt 100 ] 返回 true。
- -a  与运算，两个表达式都为 true 才返回 true。 [ $a -lt 20 -a $b -gt 100 ] 返回 false。

### 逻辑运算符

- &&  逻辑的 AND
- ||  逻辑的 OR

### 字符串运算符

- =   检测两个字符串是否相等，相等返回 true。
- !=  检测两个字符串是否相等，不相等返回 true。
- -z  检测字符串长度是否为0，为0返回 true。    [ -z "$a" ] 返回 false
- -n  检测字符串长度是否为0，不为0返回 true。  [ -n "$a" ] 返回 true。
- str 检测字符串是否为空，不为空返回 true。    [ "$a" ] 返回 true。

### 文件测试运算符

- -b file  检测文件是否是块设备文件，如果是，则返回 true。   [ -b $file ] 返回 false。
- -c file  检测文件是否是字符设备文件，如果是，则返回 true。
- -d file  检测文件是否是目录，如果是，则返回 true。
- -f file  检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。  
- -g file  检测文件是否设置了 SGID 位，如果是，则返回 true。  
- -u file  检测文件是否设置了 SUID 位，如果是，则返回 true。  
- -k file  检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。
- -p file  检测文件是否是有名管道，如果是，则返回 true。  
- -r file  检测文件是否可读，如果是，则返回 true。
- -w file  检测文件是否可写，如果是，则返回 true。
- -x file  检测文件是否可执行，如果是，则返回 true。
- -s file  检测文件是否为空（文件大小是否大于0），不为空返回 true。
- -e file  检测文件（包括目录）是否存在，如果是，则返回 true。

## read命令

- 一个一个词组地接收输入的参数，每个词组需要使用空格进行分隔；如果输入的词组个数大于需要的参数个数，则多出的词组将被作为整体为最后一个参数接收
- -p 输入提示文字   -n 输入字符长度限制(达到最大，自动结束)  -t 输入限时  -s 隐藏输入内容

```shell
    read -p "请输入一段文字:" -n 6 -t 5 -s password
    echo -e "\npassword is $password"
    # $ sh test.sh
    #  请输入一段文字:
    #  password is asdfgh
```

## test命令

- test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试
  - if test $[num1] -eq $[num2]

## 流程控制

- sh的流程控制不可为空，比如if判断语句里，如果else分支没有语句执行，就不要写这个else。

```shell
    if condition
    then
        command1
        command2
        ...
        commandN
    fi

    #  终端写成一行
    if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi
```

- if else-if else

```shell
    if condition1
    then
        command1
    elif condition2
    then
        command2
    else
        commandN
    fi
```

- for循环

```shell
    for name in item1 item2 ... itemN
    do
        command1
        command2
        ...
        commandN
    done

    #  for name in item1 item2 ... itemN; do command1; command2…; done;
    #  in列表可以包含替换、字符串和文件名。in列表是可选的，如果不用它，for循环使用命令行的位置参数。

    for loop in 1 2; do echo "The value is: $loop"; done;
    #  The value is: 1
    #  The value is: 2

    for str in 'This is a string'; do echo $str; done;
    #  This is a string
```

- while循环

```shell
    while condition
    do
        command
    done


    int=1
    while [ $int<=3 ]
    do
        echo $int
        let "int++"
    done
    #  1
    #  2
    #  3
    #  let 命令，它用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量
```

- until 循环

```shell
    #  执行一系列命令直至条件为 true 时停止
    until condition
    do
        command
    done
```

<!-- TODO 待续 -->