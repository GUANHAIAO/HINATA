## Lesson2  Shell Operation

#### 序
* C++、Java：工程语言，先编译再运行|涉及对底层硬件的操作，针对平台
* Shell、Python、awk：高级语言|简单|脚本语言|可解释语言，仅关注上层数学逻辑，没有数据声明，可跨平台使用
* Shell中Linux命令要用反引号括起
```
  Linux运行java|运行工程语言涉及很多配置文件
编译：javac -cp ./ Hello.java
运行：java -cp ./ Hello.java
```
#### 一、Shell的种类
* Bourne Shell（/usr/bin/sh或/bin/sh）
* Bourne Again Shell（/bin/bash）
* C Shell（/usr/bin/csh）
* K Shell（/usr/bin/ksh）
* Shell for Root（/sbin/sh）<br>
<br>
一般使用Bash

```
"""
#! 告诉系统其后路径所指的程序是解释此脚本文件的shell程序| 即使用什么解释器
"""

#!/bin/bash
echo “Hello World!”       # echo是输出命令
```

#### 二、运行Shell脚本的三方法
#### 0. sh test.sh
#### 1. 作为可执行程序
将代码保存为`test.sh`，cd到相应目录执行
```
"""chmod命令见Linux第二点11"""

chmod +x ./test.sh    # 不可写成test.sh，仅在PATH中寻找。./test.sh表示当前目录
./test.sh                         # 表示执行脚本
```
亦可
```
sh test.sh
```
#### 2. 作为解释器参数
直接运行解释器，其参数为shell脚本的文件名
```
/bin/sh test.sh
/bin/php test.php              # 用php做解释器
```
此种运行方式无需在第一行指定解释器信息`!#`

#### 三、Shell变量|字符串|数组|注释
定义时无需美元符号，调用时需要
```
your_name=“ken”              # 变量和等号之间不能有空格
echo ${your_name}
echo $your_name              # 大括号可选，为避免与变量后字符串混淆，一般要用
```
#### 1. 变量的类型
```
1.局部变量
在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问
2.环境变量
所有程序，包括shell启动的，都可以访问环境变量
3.Shell变量
由shell程序设置的特殊变量，shell变量中一部分局部变量，一部分是环境变量
```
#### 2. 针对变量的操作
1）只读变量<br>
readonly命令，使变量变为只读变量，其值不能被改变
```
#! /bin/bash
myUrl="http://www.google.com"
readonly myUrl
myUrl=”http://www.runoob.com”
```
结果报错
```
/bin/sh: NAME: This variable is read only.
```
2）删除变量——unset命令
```
unset variale_name    # 无需美元符号，不可用于删除只读变量
```
#### 3. Shell字符串
1）单引号<br>
* 任何字符原样输出，所以变量无效，会直接连美元符号一起出书
* 不可出现单独的单引号
2）双引号<br>
* 内部可以使用变量和转义字符
```
your_name='runoob'
str="Hello, I know you are \"$your_name\"! \n" 
# 转义字符可让字符后的字符原样输出
# 此处转义字符是为了让两个双引号可原样输出
```
注shell中`\n`在变量中原样输出，若想将其作为换行符，如下
```
echo -e ${str}
使用-e，且将\n放于双引号中
```
3）获取字符串长度
```
str="abcd"
echo ${#string} 
# 变量前加#，类$#，用于计算个数
```
4）提取子字符串<br>
从0开始
```
str="runoob"
echo ${str:1:4}
# 输出 unoo|空格算一个字符
```
5）查找子字符串
```
str="runoob"
echo `expr index "$str" io`
# 反引号|查找字符中的io，那个先遇到先返回哪个
```
#### 4. Shell注释
```
1. 单行注释
#
2. 多行注释
:<<EOF|!  两符号任选其一
注释...
注释...
EOF|!
```

#### 三、Shell_参数传递
* `$#`表示传入的参数个数
* `$0`表示当前脚本的文件名
* `$n`表示第n个参数

```
# sh test.sh ken shawn win
#!/bin/bash
echo "Filename：$0"  # tesh.sh
echo "第一个参数为：$1"  # ken
echo "第一个参数为：$2"  # shawn
echo "第一个参数为：$3"  # win
# 当n>=10，需要用${n}表示第n个参数
```

* `echo $*`：输出传入的所有参数，直接以单字符串输出  "ken win shawn"
* `echo $@`：输出所有参数，每个参数各自作为一个字符串 "ken" "win" "shawn"
```
for i in "$*"; do   #实则$*中仅一个字符串
    echo $i
done

for i in "$@"; do   #实则循环了三次
    echo $i
done
```
#### 四、Shell的运算符|expr命令
#### 1. 算术运算符
* 算式中数字和运算符间必须空格
* expr是表达式计算工具，用于完成表达式的求值
```
# 利用反引号
val=`expr 2 + 2`
# 利用美元符号
val1=$(expr $a + $b)    #expr组合括号
val2=$[$a+$b]           #expr组合中括号

1. plus
`expr $a + $b`
2. minus
`expr $a - $b`
3. multiple
`expr $a \* $b`   # shell中*有其独特含义，需转义符
4. divide
`expr $a / $b`
5.  求余
`expr $a % $b`
6. 赋值
`a=$b`
```
#### 2. 关系运算符

|运算符|意义|运用|
|-----|-----|------|
|-eq或==|等于| $a -eq $b|
|-ne或!=|不等于| $a -ne $b|
|-gt|greater than 大于| $a -gt $b|
|-lt|less than 小于| $a -lt $b|
|-ge|大于等于| $a -ge $b|
|-ls|小于等于| $a -le $b|

> 字符串相等 if[[ ${a} = "shw" ]]，等号两侧必空格

#### 3. 布尔运算符|逻辑运算符

|运算符|意义|运用|
|--------|--------|---------|
|！|非| !false == true|
|-a|and与运算，均true才返回true| $a -lt 20 -a $b -lt 20|
|-o|or或运算| $a -lt 20 -o $b -lt 20|
|&&|逻辑与|$a -lt 20 && $b -lt 20|
|\|\||逻辑或|$a -lt 20 \|\| $b -lt 20|

#### 五、echo命令
不自带换行符
#### 1. 显示普通字符串
```
echo "Hello world!"

# 双引号可以省略，效果同上
echo Hello world!   
```
#### 2. 显示转义字符
```
# 若想显示双引号，则在引号前加转义字符
echo "\"Hello world!\""
```
#### 3. 显示变量
```
echo "Enter name:"
read name
echo "Hi, $name!"
```
#### 4. 显示换行|不换行
```
# -e开启转义
echo -e "It is a test!\n"     # \n:换行
echo -e "It is a test!\c"     # \c:不换行
echo "It is a test!\n"        # \n原样输出
```
#### 5. 显示命令执行结果
```
# 利用反引号
echo `date`           # 显示当前日期 Tue Sep 24 23:39:21 CST 2019
echo `expr 1 + 3`     # 4
```

#### 6. 字符串和变量同时输出
```
echo "ddd:" $(expr ${a} + ${b})
echo "ddd:"$($a)
```

#### 六、流程控制
> 循环语句中 i++ => let "i++"(仅bash中用)|let语句变量计算时无需加上$符号引用
#### 1. if语句
* POINT：勿漏`fi`|中括号首尾两空格|shell无缩进,空四格
```
（1） 简单if语句
if [[ condition ]]    # $a = $b 等号两侧空格勿漏
then
    command1;         # 分号可有可无
    command2
    command3
fi
# 写成单行
if [[ condition ]];then command;fi

（2）if-else语句
if [[ condition ]]
then
    command;
elif [[ condition ]]
then
    command;
else
    command;
fi
```

#### 2. while语句
* POINT：do和done
```
（1）while语句
while(( condition|<= ))
or while [[ condition| -le ]]
do
    command
done
（2）无do-while语句
```
#### 3. for语句
* POINT：双括号
```
1. java式|带分号
for((i=1;i<=10;i++))     # 双括号
do
    sum=`expr ${sum} + $i`
    or
    ((sum+=i))
done

2. 直接循环|python式
for i in 1 2 3 4 (或str1 str2 str3）        # 空格隔开元素，每一个原样赋给i
do
    echo $i
done

3. 循环序列|python式
echo $(seq a b) => 从自然数a开始输出到b
for i in $(seq 1 10)
do
    sum=`expr ${sum} + $i`
done

4. 循环列表|python式
list="a b c d"
for i in $list
do
    echo $i
done
```
#### 4. until语句
一直循环直至条件为true
```
until [[ condition ]]
do
    command
done
```
#### 5. case语句
* POINT：双分号|esac
```
read num
case $num in
    1) 
    command
    command
    break
    ;;
    2 | 3) 
    command 
    command
    break
    ;;
    "yes") 
    command
    ;;
    *)
    command
    ;;
esac
```
#### 七、函数
```
#!/bin/bash

function name()   #括号仅是一种格式，括号内不写任何内容
{
    command
    [return value]
}

# 传递参数|函数名后以空格隔开，在定义时不可知名参数
name para1 para2
1. 无区别接受参数
function sum()
{
    local sum=0
    for i in $@     # $@表示所有参数
    do
        ((sum+=n))
    done 
    return ${sum}
}

sum 1 2 3
echo $?   # 输出函数返回值，用$?

2. 有序接受参数
function max()
{
    if [ $1 -gt $2 ]
    then
        return $1
    elif [ $1 -lt -$2 ]
    then 
        return $2
    else
        return 0
    fi
}

max 5 15
echo $?
```

#### 八、Shell数组

bash仅支持一维数组<br>
#### 1. 定义数组<br>
用括号定义，元素间用空格隔开
```
arrary_name=(value0 val1 ... valn)
or
for((i=1;i<=10;i++))
do
    arr[i]=`expr $i \* 2`
done
```
可单独定义数组元素，且下标可不连续|未定义的不会自动为0，而是空白|故定义几个则为几个元素
```
array_name[0]=val0
array_name[2]=val1
```
#### 2. 读取数组
```
# 获取单一元素
${array_name[下标]}
# 获取所有元素
echo ${array_name[@]}
```
#### 3. 获取数组长度
```
# 获取元素个数
echo ${#array_name[@]}
# 获取单个元素长度
echo ${#array_name[n]}
```
#### 4. 数组截取
一般是遍历数组后截取
```
str="ILIKEBIGDATA!"
# B在第5个，共截取七个字符
echo ${str:5:7}     # BIGDATA
```
#### 4. 字典
##### 4.1 定义字典
```
declare -A dic
dic=([a]=1 [b]=2)
```
##### 4.2 输出字典
(1) 输出值
```
echo ${dic[key]}
echo ${dic[@]}    # 输出所有的值
```
(2) 输出键
```
echo ${!dic[@]}   # 输出所有key
```
##### 4.3 遍历字典
(1) 基于值遍历(数组元素)
```
for var in ${arr[@]}   
do
   echo $var
done
```
(2) !|基于键遍历(数组索引）
```
for i in ${!arr[@]}   
do
    echo ${arr[$i]}
done
```

#### 特殊命令
#### 1.nohup
* 后台运行，与前端交互
* nohup与&连用，忽略所有发送给子命令的挂断信号sighup => nohup由来
```
nohup sh test.sh &
得到一个进程号PID，需要记录下来
ps aux | grep 43412
# Done 表示运行完成
```
![avatar](https://img-blog.csdn.net/20180623104147434?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaVh1ZVRhbkxhbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
#### 2. crontab
定时部署，工程化自动部署
```
crontab -e      # 进入界面
* *\6 * * * nohup sh 文件名 shw > log.txt > 2&>1 &
"""
# 表示表示行
> 表示重定向
五个* 分别表示分钟|小时|天|周|月
*/60 */6 * * *  每六个小时执行一次，每设置更高级时，注前一级改为0或*/总数
* */6 * * * 每隔六小时，到规定小时数每分钟执行一次，直至该小时段结束
"""
# 在txt中看报错和输出
sh 文件名 > test.txt 2>&1
```
#### 3. 2>&1
```
`cat file1 > file 2>&1`表示将file1重定向到file，将stderr和stdout合并重定向到file

2>&1 用于重定向语句之后
`2`表示standerror，指标准错误，打印出所有错误
`1`表示standout，指标准输出，打印出所有输出
`2>&1`合并，同时输出所有错误警报和标准输出，即日志，查看其异常及正常运行情况

```
#### 4. &
在命令行最后&：表示该命令后台运行


