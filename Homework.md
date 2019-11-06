#### Mission Description
```
 1. Find the top 100 valuable queries for each day, rank them by shw, clk and price.
 2. Use crontab to deploy them on linux as daily mission.
 3. Input Requirement:  a parameter to specify rank by what indicators, such as shw, clk or price.
 4. Output Requirement:  specify the log date, and executing date.  
```
#### 语法说明
```
1. crontab -e 
（1）crontab尝试后井号屏蔽，因每隔一段时间在log.txt添加内容，内存不够
     >> 表示追加重定向（不替换，直接补充内容）
（2） tail -f log.txt：查看文件最后是否有追加
2. 运用数组查看文件
# linux指令用单引号括起|定义数组存储文件名
c=0
for file in 'ls /usr/tmp/bigData_Homework/zx_log_md5_*.txt
do 
    filelist[$c]=$file
    c = expr `$c + 1`
done

echo "Filelist is : ***************" 
```
#### 实验报告
电子版注明学号姓名
实验目的 
实验对象 
实验方法|分哪几个模块
结果展示 
结果和代码路径
    
