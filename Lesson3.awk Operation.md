## Lesson3      awk|行处理器
#### 序
text processing tools<br>
听完一个需求，快速写成代码
```
【面试题——时间复杂度|空间复杂度】
同样的逻辑，用java写时间较长
脚本语言已进行过多线程优化
```
#### 基本语法

【文件解读】
```
第一列搜索query，搜索词已加密
第二列 1 1：此广告词被展示show|此广告词被惦记click
第四列是搜索后点击的广告词
191.00 广告价格
```
#### 0. 句式解析
```
''：引用代码块
{}：命令代码块，可包含一条或多条命令，用分号隔开
END：结尾代码块
```
#### 1. 统计列数
```
"""
$0 指整行数据
$1 用tab键切分整个文本后第一列的数据，从1开始计数非0
"""
[origin]
awk '{print $1,$4}' filename            # tab分隔后，输出文本1，4项

[lesson]
底行模式\t：在文本中找tab的位置，13个切分呈n列

cat zx.test | awk -F'\t'
cat test| awk -F'\t' '{print $0"\t"$1}'
```

#### 2. 打印某一列内容|第一列所有query和第二列shw_clk
```
\t隔开文本，$i表示第i列，花括号内表示$1和$2间用\t隔开
cat test.txt | awk -F'\t' ‘{print $1'\t'$2}’ > query.txt
```
#### 3. 设置变量|统计行数
```
[origin]
awk -va=value -vb=s ‘{print $1,$1+a,$1s}' test.txt
""" $1s 即第一列内容后加s输出"""  10->10s 
```
```
[lesson]
"""
shell统计时间很长

第一个{}|并行处理，表示for i in eachline of the file; command; 
        =对每行都执行此操作
第二个{}|汇总处理，整个文档处理完后进行统计
-v 表示定义变量
# 第一个{}
while read line
do
    sum = `expr $sum + 1`
done > test.sh
"""
cat filename | awk -f'\t' '{command}END{}'
cat filename | awk -f'\t' -vsum==0'{sum+=1}END{print sum}'
```
#### 4. 数组
```
最有价值的前100个query
"""
计算query的总点击*价格=每一次出现加一次价格
*指正则匹配
sort值排列
"""
cat zx_log_md5_201507*.txt | awk -F'\t' '{split($2,cs," "); arr[$1]+=cs[printi"\t"arr[i]}' | sort -k2nr > query-rank.txt

1.数组赋值
"""
key-value：键值对（唯一标识ID|属性值）
arr[key]=value
$1指数组的键
$11指数组的值
+=value：当循环又遇到同一个query（key）时，值不断累加
=value：每次循环都给此key赋值value
"""
awk -F'\t' '{arr[$1]+=$11}END{}'
awk -F'\t' '{arr[$1]=1}END{}'
# 仅输出key
awk -F'\t' '{arr[$1]=1}END{for ( i in arr) print i"\t"arr[i]}' 
```
#### 5. 按要求输出
```
条件和command于同一对单引号中,command位于一对花括号中
1. 输出第一列大于2的行
awk '$1>2 {print $1}' test
2. 输出第一列大于2且第二列等于'is'的行
awk '$1>2 && $2=="is" {print $1}' test
```
#### 6. split命令
允许将字符串分割为单词并存储在数组中
```
split(string, array_name, 自定义分隔符)
[e.g]
time="12:34:56"
awk '{split($0,a,":");print a[1],a[2],a[3]}'    # 注意自定义字符串需要双引号
```
#### 7. sort命令
将文件每一行作为一个单位
```
-t: 设定分隔符
-k: 定义域，即分隔
-n：纯数字排序，升序
-nr：数字降序

# 对第三个域数字降序排列
cat xxx | awk xxxx| sort -k3nr
```
