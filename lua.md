# 1、lua的基本语法

### 1.1、注释

```lua
--单行注释--
--[[
多行
注释
--]]
sss
```

### 1.2、标识符

- 标示符以一个字母 A 到 Z 或 a 到 z 或下划线 **_** 开头后加上 0 个或多个字母，下划线，数字（0 到 9）。

- 不使用下划线+大写字母；不允许使用特殊字符如 **@**, **$**, 和 **%** 来定义标示符；
- 区分大小写

```lua
--正常的标识符--
mohd         zara      abc     move_name    a_123
myname50     _temp     j       a23b9        retVal
```

### 1.3、全局变量

- 变量总是全局的
- 无需声明，给变量赋值后即创建了该全局变量。当访问一个未被初始化的全局变量也不会出错，只不过得到nil的结果。

### 1.4、删除变量

当想删除变量的时候，只需要将该变量赋值给nil。

# 2、lua的数据类型

lua是动态类型语言，变量无需创建，只需赋值，下面介绍8种基本数据类型。

| 数据类型 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| nil      | 这个最简单，只有值nil属于该类，表示一个无效值（在条件表达式中相当于false）。 |
| boolean  | 包含两个值：false和true。                                    |
| number   | 表示双精度类型的实浮点数                                     |
| string   | 字符串由一对双引号或单引号来表示                             |
| function | 由 C 或 Lua 编写的函数                                       |
| userdata | 表示任意存储在变量中的C数据结构                              |
| thread   | 表示执行的独立线路，用于执行协同程序                         |
| table    | Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字、字符串或表类型。在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。 |

```lua
print(type("Hello world"))      --> string
print(type(10.4*3))             --> number
print(type(print))              --> function
print(type(type))               --> function
print(type(true))               --> boolean
print(type(nil))                --> nil
print(type(type(X)))            --> string
```

### nil

- 它表示无有效值
- 将其赋值给变量表示“删除”
- type(X)，X无定义，实际上其返回值的nil是一个String类型。

### boolean

- lua将false和nil看作是false
- 将其他的包括数字皆为true

### number

lua默认只有一种number类型--double(双精度)类型

### String

- 字符串可有单引号和双引号来表示

```lua
string1 = "this is string1"
string2 = 'this is string2'
```

- 也可用2个方括号表示"一块"字符串

```lua
html = [[
<html>
<head></head>
<body>
    <a href="http://www.runoob.com/">菜鸟教程</a>
</body>
</html>
]]
print(html)
```

- lua会自动的将数字字符串转成数字进行运算
- 若想进行字符串的拼接，则使用的是".."

```lua
print("a" .. 'b')
--ab--
print(157 .. 428)
--157428--
```

- 使用#放在字符串前面，用来计算字符串的长度

```lua
len = "www.runoob.com"
print(#len)
--14--
print(#"www.runoob.com")
--14--
```

### table

1、table的创建时通过“构造表达式”来完成，最简单的构造表达式是{}，用来创建空表，或直接填入数据，进行表的初始化。

```lua
-- 创建一个空的 table--
local tbl1 = {}
-- 直接初始表--
local tbl2 = {"apple", "pear", "orange", "grape"}
```

2、Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字或者是字符串。

```lua
a = {}
a["key"] = "value"
key = 10
a[key] = 22
a[key] = a[key] + 11
for k, v in pairs(a) do
    print(k .. " : " .. v)
end
--[[
key : value
10 : 33
--]]
```

3、lua里表的默认初始索引一般以1开始

```lua
local tbl = {"apple", "pear", "orange", "grape"}
for key, val in pairs(tbl) do
    print("Key", key)
end
--[[
Key    1
Key    2
Key    3
Key    4
--]]
```

4、table没有固定长度，有新数据时会自动增长，没初始化的table都是nil

```lua
a3 = {}
for i = 1, 10 do
    a3[i] = i
end
a3["key"] = "val"
print(a3["key"])
print(a3["none"])
--[[
val
nil
--]]
```

### function(函数)

1、函数在lua中被看作时“第一类值(Firs-Class Value)”，函数可以存在变量里：

```lua
-- function_test.lua 脚本文件
function factorial1(n)
    if n == 0 then
        return 1
    else
        return n * factorial1(n - 1)
    end
end
print(factorial1(5))
factorial2 = factorial1
print(factorial2(5))
--[[
120
120
--]]
```

2、作为匿名函数(anonymous function)的方式通过参数传递

```lua
-- function_test2.lua 脚本文件
function testFun(tab,fun)
        for k ,v in pairs(tab) do
                print(fun(k,v));
        end
end


tab={key1="val1",key2="val2"};
testFun(tab,
function(key,val)--匿名函数
        return key.."="..val;
end
);
--[[
key1 = val1
key2 = val2
--]]
```

### thread(线程)

在lua中，最主要的线程是协同程序(coroutine)。它跟线程(thread)差不多，有自己的独立的栈、局部变量和指令指针，可以跟其他协同程序共享全局变量和其他大部分东西。

线程和协程的区别：线程可以同时多个运行，协程任意时刻只能运行一个，并且处于运行状态的协程只有被挂起(suspend)时才会暂停。

### userdata(自定义类型)

userdata 是一种用户自定义数据，用于表示一种由应用程序或 C/C++ 语言库所创建的类型，可以将任意 C/C++ 的任意数据类型的数据（通常是 struct 和 指针）存储到 Lua 变量中调用。

# 3、lua变量

### 3.1、lua变量有三种类型

- 全局变量

  所创建的变量都是全局变量，哪怕是在语句块和函数中，除非用local显式声明

- 局部变量

  使用local进行显式声明，其作用域从声明位置开始到所在语句块结束。

# 4、lua循环

### 4.1、while循环

### 4.2、for循环

**4.2.1、数值for循环**

```lua
for var=exp1,exp2,exp3 do
    <执行体>
end
--exp1到exp2，以exp3为步长
for i=1,f(x) do
    print()
end
for i=10,f(x) do
    print()
end
```

**4.2.2、泛型for循环**

```lua
a = {'one','two','three'}
for i , v in ipairs(a) do
    print(i)
end    
```

i为数组下标，v为对应索引的数组元素值。ipairs是lua提供的一个迭代器函数

### 4.3、repeat..until循环

```lua
repeat
    statements
until(condition)    
```

# 5、lua流程控制

主要学习下if-else语句

```lua
if( 布尔表达式 1)
then
   --[ 布尔表达式 1 为 true 时执行该语句块 --]
   if(布尔表达式 2)
   then
      --[ 布尔表达式 2 为 true 时执行该语句块 --]
   end
end
--ex
a = 100;
b = 200;

--[ 检查条件 --]
if( a == 100 )
then
   --[ if 条件为 true 时执行以下 if 条件判断 --]
   if( b == 200 )
   then
      --[ if 条件为 true 时执行该语句块 --]
      print("a 的值为 100 b 的值为 200" );
   end
end
print("a 的值为 :", a );
print("b 的值为 :", b );
```

# 6、lua函数

### 6.1、函数定义格式

```lua
optional_function_scope function function_name( argument1, argument2, argument3..., argumentn)
    function_body
    return result_params_comma_separated
end
```

- **optional_function_scope:** 该参数是可选的制定函数是全局函数还是局部函数，未设置该参数默认为全局函数，如果你需要设置函数为局部函数需要使用关键字 **local**。
- **function_name:** 指定函数名称。
- **argument1, argument2, argument3..., argumentn:** 函数参数，多个参数以逗号隔开，函数也可以不带参数。
- **function_body:** 函数体，函数中需要执行的代码语句块。
- **result_params_comma_separated:** 函数返回值，Lua语言函数可以返回多个值，每个值以逗号隔开。

#### 6.1.1、ex

```lua
function maximum (a)
    local mi = 1             -- 最大值索引
    local m = a[mi]          -- 最大值
    for i,val in ipairs(a) do
       if val > m then
           mi = i
           m = val
       end
    end
    return m, mi
end

print(maximum({8,10,23,12,5}))
```

#### 6.1.2、可变函数

如同C语言，使用`...`表示函数有可变的参数

```lua
function average(...)
   result = 0
   local arg={...}    --> arg 为一个表，局部变量
   for i,v in ipairs(arg) do
      result = result + v
   end
   print("总共传入 " .. #arg .. " 个数")
   return result/#arg
end

print("平均值为",average(10,5,3,4,5,6))
```

- 通常在遍历变长参数的时候只需要使用 **{…}**，然而变长参数可能会包含一些 **nil**，那么就可以用 **select** 函数来访问变长参数了：**select('#', …)** 或者 **select(n, …)**

- - **select('#', …)** 返回可变参数的长度。
  - **select(n, …)** 用于返回从起点 **n** 开始到结束位置的所有参数列表。

- 调用 select 时，必须传入一个固定实参 selector(选择开关) 和一系列变长参数。如果 selector 为数字 n，那么 select 返回参数列表中从索引 **n** 开始到结束位置的所有参数列表，否则只能为字符串 **#**，这样 select 返回变长参数的总数。

# 7、lua的运算符

特别的：

- ~=:不等于，检测两个值是否相等，不等返回true
- 逻辑运算符
  - and：（逻辑或）同true为true，有false返false
  - or：（逻辑与）
  - not
- #：一元运算符，返回字符串的长度

# 8、lua的字符串

### 8.1、一些常用的字符串函数

1. string.upper(arg)

2. string.lower(arg)

3. string.gsub(mainString,findString,replaceString,num)

   - 字符串替换，mainString 为要操作的字符串， findString 为被替换的字符，replaceString 要替换的字符，num 替换次数（可以忽略，则全部替换），

   ```lua
   > string.gsub("aaaa","a","z",3);
   zzza    3
   ```

   

4. string.find(str,substr,[init,[end]])

   - 在一个指定的目标字符串中搜索指定的内容(第三个参数为索引),返回其具体位置。不存在则返回 nil

   ```lua
   > string.find("Hello Lua user", "Lua", 1) 
   7    9
   ```

5. string.reverse

6. string.format(...)

   - 返回一个类似printf的格式化字符串

   ```lua
   > string.format("the value is:%d",4)
   the value is:4
   ```

7. string.char(arg)和string.byte(arg,[int])

   - char将整形转化成字符并连接，byte转换字符为整数值（可以指定，默认第一个），转成对应ASII码值

   ```lua
   > string.char(97,98,99,100)
   abcd
   > string.byte("ABCD",4)
   68
   > string.byte("ABCD")
   65
   ```

8. string.len(arg),字符串长度

9. string.rep(String n)

   - 返回string的n个拷贝

10. string.gmatch(str,pattern)

    - 回一个迭代器函数，每一次调用该函数，返回一个在字符串str找到的下一个符合pattern描述的子串。若参数pattern描述的字符串没有找到，迭代函数返回nil

    ```lua
    > for 
    word in string.gmatch("Hello Lua user", "%a+") do print(word) 
    end
    Hello
    Lua
    user
    ```

11. string.match(str,pattern,init)

    - 只寻找源字符串中的第一个配对。参数init可选，指定搜寻起点，默认为1.成功配对后，函数将返回配对表达式中的所有捕获结果；若没有设置捕获标记，则返回整个配对字符串，当没有成功的配对时，返回nil

    ```lua
    > = string.match("I have 2 questions for you.", "%d+ %a+")
    2 questions
    
    > = string.format("%d, %q", string.match("I have 2 questions for you.", "(%d+) (%a+)"))
    2, "questions"
    ```

# 9、字符串格式化

Lua 提供了 **string.format()** 函数来生成具有特定格式的字符串, 函数的第一个参数是格式 , 之后是对应格式中每个代号的各种数据。

由于格式字符串的存在, 使得产生的长字符串可读性大大提高了。这个函数的格式很像 C 语言中的 printf()。

以下实例演示了如何对字符串进行格式化操作：

格式字符串可能包含以下的转义码:

- %c - 接受一个数字, 并将其转化为ASCII码表中对应的字符
- %d, %i - 接受一个数字并将其转化为有符号的整数格式
- %o - 接受一个数字并将其转化为八进制数格式
- %u - 接受一个数字并将其转化为无符号整数格式
- %x - 接受一个数字并将其转化为十六进制数格式, 使用小写字母
- %X - 接受一个数字并将其转化为十六进制数格式, 使用大写字母
- %e - 接受一个数字并将其转化为科学记数法格式, 使用小写字母e
- %E - 接受一个数字并将其转化为科学记数法格式, 使用大写字母E
- %f - 接受一个数字并将其转化为浮点数格式
- %g(%G) - 接受一个数字并将其转化为%e(%E, 对应%G)及%f中较短的一种格式
- %q - 接受一个字符串并将其转化为可安全被Lua编译器读入的格式
- %s - 接受一个字符串并按照给定的参数格式化该字符串

为进一步细化格式, 可以在%号后添加参数. 参数将以如下的顺序读入:

- (1) 符号: 一个+号表示其后的数字转义符将让正数显示正号. 默认情况下只有负数显示符号.
- (2) 占位符: 一个0, 在后面指定了字串宽度时占位用. 不填时的默认占位符是空格.
- (3) 对齐标识: 在指定了字串宽度时, 默认为右对齐, 增加-号可以改为左对齐.
- (4) 宽度数值
- (5) 小数位数/字串裁切: 在宽度数值后增加的小数部分n, 若后接f(浮点数转义符, 如%6.3f)则设定该浮点数的小数只保留n位, 若后接s(字符串转义符, 如%5.3s)则设定该字符串只显示前n位.

# 10、lua table（表）

### 10.1、简单方法

1. table.concat(table [, sep [, start [, end]]]):。

   concat是concatenate ，分从start位置到end位置的所有元素, 元素间以指定的分隔符(sep)隔开。

2. table.insert(table,[pos,] value):

   在数组部分指定位置(pos)插入值为value的一个元素，默认为数组部分末尾

3. table.remove(table,[,pos])

   返回table数组部分位于pos位置的元素，其后的元素会被前移，pos参数可选。默认为table最后一个元素，即从最后一个元素删起。

4. table.sort(table,[,comp])

   对给定的table进行升降序排序

# 11、lua模块与包

# 12、lua元表

# 13、lua协同程序

# 14、lua文件I/O

# 15、lua错误处理

# 16、lua调试

# 17、lua垃圾回收

# 18、lua面向对象

# 19、lua数据库访问

   

   

   

   
