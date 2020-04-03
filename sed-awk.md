### sed

#### 简介

sed是 Stream Editor, 流式编辑器的缩写, 简称: 流编辑器, 用来处理文件

#### sed如何如何处理文件

sed会一行一行读取文件中内容, 然后按照要求进行处理 , 把处理的结果输出到屏幕上

1. sed先读取文件中的一行, 然后将其保存在一个缓冲区中(也成为模式空间)
2. 根据需求处理临时缓冲区中的行, 完成后将该行输出到屏幕上



总结:

* 默认情况下, sed是不会去改变源文件的, 修改的只是缓冲区中的文本

* 主要用sed自动的去编译一个或者多个文件, 简化对文件的反复操作, 对文件进行过滤和转换

  

#### 语法

```bash
sed [option] '处理动作' 文件名
# 常见的选项
-e: 进行多项, 多次编辑
-n: 取消默认输出(不再打印模式空间)
-r: 使用正则
-i: 原地编辑文件(修改源文件)
-f: 指定sed脚本的文件名

#常见的动作 -- 以下所有的动作, 都要在单引号里面
'p' 打印
'i' 在指定行的上一行插入内容 (vim里面的O)
'a' 在指定行的下一行插入内容 (vim里面的o)
'c' 替换指定行的所有内容 
'd' 删除指定行
```

#### CRUD举例说明

```bash
# 打印
# 对文件什么都不做
sed '' sed.txt
# 打印文件内容, 并取消默认输出, 否则算上p 会打印两遍
sed -n 'p' sed.txt 
# 打印第1行内容
sed -n '1p' sed.txt
# 打印第2行内容
sed -n '2p' sed.txt
# 打印第1-5行内容
sed -n '1,5p' sed.txt
# 打印最后一行内容
sed -n '$p' sed.txt



# 增加文件内容
# 在最后一行下面追加如下内容
sed '$a99999' sed.txt 
# 在文件每行下面追加
sed 'a999999' sed.txt
# 在文件的第五行下面追加
sed '5a999999' sed.txt
# 在文件最后一行的上一行追加
sed '$i99999' sed.txt
# 在每一行的上一行追加
sed 'i99999' sed.txt
# 在文件第6行的上一行追加
sed '6i99999' sed.txt
# 在以uucp开头行的上一行插入内容
sed '/^uucp/ihello' sed.txt



# 修改文件中的内容
# 使用hello world替换文件中的第5行内容
sed '5chello world' sed.txt
# 替换文件的所有内容
sed 'chello world' sed.txt
# 将1-5行替换成hello world
sed '1,5chello world' sed.txt
# 替换以user01开头的行
sed '/^user01/c*****' sed.txt



# 删除文件的内容
# 删除第1行
sed '1d' sed.txt
# 删除第1-5行
sed '1,5d' sed.txt
# 删除最后一行
sed '$d' sed.txt

```

#### 对文件进行搜索替换

语法如下: 

```bash
sed 选项 's/搜索的内容/替换的内容/动作' 文件

s 	   表示搜索
/ 	   表示分隔符
动作   一般为打印p, 或者全局替换g

# 例: 将第一个root替换成ROOT并打印
sed -n 's/root/ROOT/p' 1.txt
# 例: 将root全局替换成ROOT并打印
sed -n 's/root/ROOT/gp' 1.txt

# 将行首的#去掉
sed -n 's/^#//gp' 1.txt

# 注释掉文件中的1-5行
sed -n '1,5s/^/#/p' 1.txt 

# 将 /sbin/nologin 替换成hello world
# 需要搜索的关键字出现/时, 需要使用\转义
sed -n 's/\/sbin\/nologin/hello world/gp' 1.txt
# 也可以像下面这样使用@, 或者是其他自定义分隔符完成切割
sed -n 's/@/sbin@/nologin/hello world/gp' 1.txt

# 只想在某一行做替换, 下面是第10行
sed -n '10s/\/sbin\/nologin/hello world/gp' 1.txt

# 将文件中的1.1.1.0 替换成1.1.1.254
sed -n 's/1.1.1.0/1.1.1.254/g' 1.txt
# 也可以这样, 使用正则完成
sed -n 's/\(1.1.1.\)0/\1254'
```



#### 其他命令

| 命令 | 解释                                       | 备注                 |
| ---- | ------------------------------------------ | -------------------- |
| r    | 从另外文件中读取内容                       |                      |
| w    | 内容另存为                                 |                      |
| &    | 保存查找串以便在替换串中引用               | 和`\(\)` 相同        |
| =    | 打印行号                                   |                      |
| !    | 对所选行以外的所有行应用命令, 放到行数之后 | '1,5!' 表示除了1-5行 |
| q    | 退出                                       |                      |

```bash

# 输出1.txt, 输出到第三行时, 再输出/etc/hosts文件中的内容
sed '3r /etc/hosts' 1.txt

# 输出1.txt, 输出到最后一行时, 再输出/etc/hosts文件中的内容
sed '3r /etc/hosts' 1.txt

# 将1.txt中 以sync开头的行, 的行首添加#
sed -n 's/^sync/#&/gp' 1.txt
# 将1.txt中 以sync开头的行, 在行首的sync后面添加#
sed -n 's/^sync/&#/gp' 1.txt
```



#### 其他选项

```bash
-e  多项编辑
-r  扩展正则
-i  修改源文件

# 打印包含home的行, 再打印行号
sed -ne '/home/p' 1.txt -ne '/home/='
# 文件放在最后也行
sed -ne '/home/p'  -ne '/home/=' 1.txt
[root@iz2zeaogswcq4v6abnhz7rz changwu]# sed -ne '/home/p' sed.txt -ne '/home/='
u:x:1000:1001::/home/u:/bin/bash
6
u5:x:1001:1002::/home/u5:/bin/bash
7
u1:x:1002:1003::/home/u1:/bin/bash
9

# 在第五行的上面插入helloworld , 再把第八行的下面插入hahahahha, 再打印行号
sed -e '5ihelloworld' -e '8ihahaha' -e  '5=;8=' 1.txt



-i 选项可以直接修改源文件, 慎用
# 搜索小写的root,替换成大写的ROOT, 搜索小写的stu, 替换成大写的STU
sed -i 's/root/ROOT/;s/stu/STU' 1.txt
```

#### sed 和 正则 配合使用

| 正则          | 说明                                  | 备注                              |
| ------------- | ------------------------------------- | --------------------------------- |
| /key/         | 查询包含关键字的行                    | sed -n '/root/p' 1.txt            |
| /key1/,/key2/ | 匹配两个关键字之间的行                | sed -n '/^admin/,/^mysql/p' 1.txt |
| /key/,x       | 查询从关键字开始, 到第x行之间的行     | sed -n '/^ftp/,7p'                |
| x,/key/       | 从第x行开始到与关键字匹配的行会见的行 |                                   |
| x,y!          | 不包含x-y的行                         |                                   |
| /key/!        | 不包含关键字的行                      | sed -n '/bash$/!p' 1.txt          |

#### sed脚本

sed 脚本是一个命令清单

每一行的末尾都不能有任何空格, 制表符, 或者其他文本

一行轴如果需要写多个命令, 使用分号隔开 

不需要, 也不能使用`` 保护命令

```bash
#!/bin/sed -f
1,5d
s/root/hello/g
3i777
# 每一行的下面加内容, 没有的话就不加
a111
p
```



### awk

#### 简介

* awk是一种编程语言,  linux/unix中 使用它进行数据的处理, 数据可以来自标准输入, 或者是一个多个文件, 或者说其他的命令行

* 处理数据的方式:  逐行扫描文件, 默认是从第一行扫描到最后一行, 寻找匹配的特定模式的行, 并在行上进行你想要的操作

* 命名的由来:  因为它有三个作者, 分别是  Alfred Aho, Brian Kernighan , Pater Weinberger

* gawk是awk的GUN版本, 它提供了贝尔实验室和GUN的一些扩展

* 下面介绍一下awk是以GUN的gawk为例, 在linux中, 已经把awk连接到gawk上了, 所以下面全部直接介绍awk

  ```bash
  [root@iz2zeaogswcq4v6abnhz7rz changwu]# ll /bin/awk 
  lrwxrwxrwx 1 root root 4 Aug 18  2017 /bin/awk -> gawk
  
  ```

  



#### awk能干啥?

* 处理文件和数据, 是类unix下的一个工具, 也是一种编程语言
* 可以用来统计数据, 比如网站的访问量, 访问的ip量等等
* 支持条件判断, 支持for和while循环



#### 命令模式语法

```bash
awk 选项 '命令部分' 文件名

# awk 命令部分引用shell变量需要将单引号换成双引号,其他情况都是单引号

# 选项
-F 定义字段分隔符, 默认使用的分隔符是空格
-v 定义变量并赋值

# 命令部分
# 1. 使用正则表达式,地址定位, 强制使用{}将awk语句抱住
'/root/{awk语句}'  		# 相当于sed中的  '/root/p'
'NR==1,NR==5{awk语句}' 	 # 相当于sed中的  '1,5p'
'/^root/,/^ftp/{awk语句}'   # 相当于sed中的  '/^root/,/^ftp/p'

# awk语句之间使用; 分隔
# {awk语句1;awk语句2;...} 
'{print $0;print$1}'  # 相当于sed中的  'p'
'NR==5{print$0}'  # 相当于sed中的  '5p'

# BEGIN...END...
'BEGIN{awk语句};{处理中};END{awk语句}'
'BEGIN{awk语句};{处理中}'
'{处理中};END{awk语句}'
```



#### 脚本模式语法

```bash
#!/bin/awk -f
# 以下是awk引号里面的命令清单, 不要使用引号保护命令, 多个命令使用分号隔开
BEGIN{FS=":"}
NR==1,NR==3{print $1"\t"$NF}
```

#### 脚本的执行

```bash
# 方式1
awk 选项 -f awk的脚本文件  需要处理的文本文件

awk -f awk.sh filename
sed -f sed.sh -i filename

# 方式2
./awk脚本的绝对路径  文件
./awk.sh filename
./sed.sh filename

```



#### awk内部相关变量

```bash
$0				# 当前处理行的所有记录

# 文件中,每行以间隔符号分隔的不同字段    awk -F:'{print $1,$3}' , 打印以:分隔的第1和第三列
$1,$2,$3...$n  

NF				# 当前记录的字段数 (列数)  awk -F:'{print NF}'
$NF				# 最后一列, $(NF-1) 表示倒数第二列
FNR/NR 			# 行号, 用于定位	
FS				# 定义间隔符	'BEGIN{FS=":"};{print $1,$3}'
OFS				# 定义输出字段的分隔符, 默认是空格, 'BEGIN(OFS="\t");print $1 $3'

# 输入记录的分隔符, 默认也是换行 'BEGIN{RS="\t"};{print $0}'
# 如果像这样把它改成 \t ,awk读取文件的时候, 碰到制表符才认为自己碰到了一个新的行
# awk 'BENGIN{RS="\t"};{print $0}' file
RS				

ORS		# 输出记录的分隔符,	默认换行, 'BEGIN{ORS="\n\n"};{print $1,$3}'
```



#### 示例:

```bash
# 打印每一行内容
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk '{print $0}' awk.txt 
rony:x:997:995::/var/lib/chrony:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
nscd:x:28:28:NSCD Daemon:/:/sbin/nologin

# 打印文件的1-5行
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk 'NR==1,NR==5{print $0}' awk.txt 
rony:x:997:995::/var/lib/chrony:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
nscd:x:28:28:NSCD Daemon:/:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
nginx:x:996:992:nginx user:/var/cache/nginx:/sbin/nologin

# 打印第一行, 或者第五行
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk 'NR==1 || NR==5{print $0}' awk.txt 
rony:x:997:995::/var/lib/chrony:/sbin/nologin
nginx:x:996:992:nginx user:/var/cache/nginx:/sbin/nologin

# 使用逻辑运算符, 打印3-5行
[root@iz2zeaogswcq4v6abnhz7rz changwu]#  awk 'NR>=1 && NR<=5{print $0}' awk.txt 
rony:x:997:995::/var/lib/chrony:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
nscd:x:28:28:NSCD Daemon:/:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
nginx:x:996:992:nginx user:/var/cache/nginx:/sbin/nologin


# 打印文件中以:分隔, 第一列($1) 和最后一列($NF)
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk -F: '{print $1,$NF}' awk.txt 
rony /sbin/nologin
ntp /sbin/nologin
nscd /sbin/nologin
tcpdump /sbin/nologin
nginx /sbin/nologin


# 打印每一行的列数
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk -F: '{print NF}' awk.txt 
7
7
7
7
7
7
7
0
0
1
0
1


# 打印包含关键字的行
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk '/home/{print $0}' awk.txt 
u:x:1000:1001::/home/u:/bin/bash
u5:x:1001:1002::/home/u5:/bin/bash
u1:x:1002:1003::/home/u1:/bin/bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk '/home/' awk.txt 
u:x:1000:1001::/home/u:/bin/bash
u5:x:1001:1002::/home/u5:/bin/bash
u1:x:1002:1003::/home/u1:/bin/bash


# 使用OFS指定输出变量的分隔符
awk -F: 'BEGIN{OFS="@---@"};{print $1,$NF}' awk.txt
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk -F: 'BEGIN{OFS="@"};{print $1,$NF}' awk.txt
rony@/sbin/nologin
ntp@/sbin/nologin
nscd@/sbin/nologin
tcpdump@/sbin/nologin
nginx@/sbin/nologin
u@/bin/bash
u5@/bin/bash
@
u1@/bin/bash
@
@
@
10.1.1.1@10.1.1.1


# 使用FS定义输入间隔符, 使用OFS定义输出间隔符
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk 'BEGIN{FS=":";OFS="@@@@"};{print $1,$NF}' awk.txt 
rony@@@@/sbin/nologin
ntp@@@@/sbin/nologin
nscd@@@@/sbin/nologin
tcpdump@@@@/sbin/nologin
nginx@@@@/sbin/nologin
u@@@@/bin/bash
u5@@@@/bin/bash

# 自定义输出间隔符
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk 'BEGIN{FS=":"};{print $1"----------"$NF}' awk.txt 
rony----------/sbin/nologin
ntp----------/sbin/nologin
nscd----------/sbin/nologin
tcpdump----------/sbin/nologin
nginx----------/sbin/nologin
u----------/bin/bash
u5----------/bin/bash
----------
u1----------/bin/bash
----------

[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk 'BEGIN{FS=":"};{print "path1:"$1," path2:"$NF}' awk.txt 
path1:rony  path2:/sbin/nologin
path1:ntp  path2:/sbin/nologin

```





#### awk工作原理

* 对于awk来说, 它会按行遍历整个文件,  使用一行作为输入,  并将这一行赋值给内部变量$0,   同时, 一行也可以叫做一个记录,  awk通过RS判断使用何种符号当作结束符( 默认的结束符的 `\n`  换行符)
* awk是可以识别出每行中列的间隔 (通过内部变量FS控制 ), , 默认的FS初始化间隔符号是' ' 空格,   当然我们也能通过修改FS, 改变awk使用的间隔符
* awk, 它的print函数可以实现打印,  打印时默认使用空格当作间隔符号,   同样的, 我们可以通过设置OFS改变awk输出的间隔符号
* awk运行的流程就是, 处理完一行后, 将从文件中取出下一行, 同样的赋值给$0中, 覆盖掉原来的内容, 进一步处理



#### 格式化输出

```bash
# 例1:
awk -F '{print "username is :" $1 "\t pass is: "$3 }' /etc/passwd

# 例2:
awk -F '{printf "%-15s %-15s \n",$1,$2}'
%s: 表示字符串 strings
%d: 数值类型
- 表示左对齐, 默认是右对齐
printf 不会自动在行尾加换行, 需要手动添加上
```



#### 定义变量

```bash
# 通过-v 后面自定义变量, 然后不需要使用$就可以实现
[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk -v NUM=3 -F: '{print NUM}' /etc/passwd

```



#### EBGIN和END

* BEGIN在程序开始前执行
* END在程序处理完成后执行

```bash
'BEGIN{开始处理之前的设置};{处理中};END{处理完成之后}'
```



#### awk和正则综合使用

```bash
==		等于
!=		不等于
>
<
>=
<=
~		匹配
!~		不匹配
!		逻辑非
&&		逻辑与
||		逻辑或
```

示例:

```bash
# 从第一行开始匹配以 1p开头的行
awk -F: 'NR==1,/^1p/{print $0}' file

# 从第一行到第五行
awk -F: 'NR==1,NR==5{print $0}' file

# 以1p开头的行, 匹配到第十行
awk -F: '/^1p/,NP==10{print $0}' file

# 以root开头的行, 匹配到以1p开头的行
awk -F: '/^root/,^1p/{print $0}' file

# 以root开头的行, 或者以1p开头的行
awk -F: '/^root/||/^1p/{print $0}' file
awk -F: '/^root/;/^1p/{print $0}' file

# 显示5-10行
awk -F: 'NR==5,NR==10{print $0}' file
awk -F: 'NR>=5 && NR<=10 {print $0}' file

# 打印30-39. 以bash结尾的内容
awk 'NR>=30 && NR<=39 && $0 ~ /bash$/{print $0}'

# 使用awk打印ip地址
[root@iz2zeaogswcq4v6abnhz7rz changwu]# ifconfig eth0|awk 'NR>1 {print$2}'|awk 'NR==1'
172.17.111.171

```

#### awk 的if结构

```bash
# shell中的if结构语法如下
if [ condition ];then
	xxx
fi

# awk中, 如下
awk 选项 '正则, 地址定位{awk}' 文件名
{ if(表达式) {语句1;语句2;...} }

# 例如
awk -F: '{if ($3==0) {print $1"是管理员"} }' file

# 注意原来的``和$()作用一样, 但是在awk中使用 $() 包裹命令
awk 'BEGIN{if($(id-u)==0){print "admin"} }'


```

#### awk 的if-else结构

```bash
# shell中的if-else
if [ condition ];then
	xxx
else
	xxx
fi

# 格式
{if(表达式){语句;语句;...}else{语句;语句;...}}

# 举例
awk -F: '{ if($3>=500 && $3 !=65534) {print $1"是普通用户"}else{print $1"不是普通用户"} }' passwd
awk 'BEGIN{ if($(id -u) -ge 500 && $(id -u) -ne 65534){print "是普通用户"}else{print"不是普通用户"}}' passwd
```

#### awk 的if-elif-else结构

```bash
# shell中的if-elif-else 结果如下
if [ condition ];then
	xxx
elif [ xxx ];then
	xxx
else	
	xxx
fi

# 在awk中入戏
{if(condition1){do1;do2...}else if(condition2){do3;do4...}else{do5}}

# 例判断用户的身份
awk -F: '{ if($3==0){print $1"是管理员"}else if($3>=1 && $3<=499 || $3==65534){print $1"是系统用户"} else {print $1":是普通用户"}}' /etc/passwd

[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk -F: '{ if($3==0){print $1"是管理员"}else if($3>=1 && $3<=499 || $3==65534){print $1"是系统用户"} else {print $1":是普通用户"}}' /etc/passwd
root是管理员
bin是系统用户
daemon是系统用户
adm是系统用户
lp是系统用户
sync是系统用户
shutdown是系统用户
halt是系统用户
mail是系统用户
operator是系统用户
games是系统用户


#  判断 系统中,管理员的个数, 系统用户数, 普通用户数
#  注意, END在''里面
awk -F: '{ if($3==0){i++} else if ($3>=1 && $3<=499 || $3==65534){j++} else {k++}};END {print "管理员数:"i "\n系统用户数:"j "\n普通用户数:"k} ' /etc/passwd

[root@iz2zeaogswcq4v6abnhz7rz changwu]# awk -F: '{ if($3==0){i++} else if ($3>=1 && $3<=499 || $3==65534){j++} else {k++}};END {print " 管理员数:"i "\n系统用户数:"j "\n普通用户数:"k} ' /etc/passwd
管理员数:1
系统用户数:20

```

#### 循环语句 - for

```bash
# shell 中的循环语句如下:
for ((i=1;i<=5;i++))
do
	xxx
done

# awk中的循环如下, 注意它的单()
for ((i=1;i<=10;i+=2));do echo $i; done | awk '{sum+=$0};END{print sum}'
awk 'BEGIN{ for(i=1;i<=10;i+=2) {print i}}'
awk 'BEGIN{ for(i=1;i<=10;i+=2) print i}'

# 计算1-5的和
awk 'BEGIN{sum=0;for(i=1;i<=5;i++) sum+=i; print sum}'
awk 'BEGIN{for(i=1;i<=5;i++) (sum+=i); {print sum}}'
awk 'BEGIN{for(i=1;i<=5;i++) (sum+=i); print sum}'
'
```

#### 循环语句 - while

```bash
awk 'BEGIN{i=1;while(i<=10){print i;i+=2}}'

# 打印1-5之和
awk 'BEGIN{i=1;sum=0;while(i<=5){sum+=i;i++};print sum }'
awk 'BEGIN{i=1;while(i<=5){(sum+=i) i++};print sum }'
```

#### awk 运算

```bash
+ - * / % ^
awk 'BEGIN{print 1+1}'
```



#### awk 统计案例

* **统计系统中各类型的shell**

```bash
awk -F: '{shells[$NF]++};END{for(i in shells){print i,shells[i]}}' /etc/passwd
```



* 统计网站的访问状态

```bash
ss -antp|grep 80|awk '{states[$1]++};END{for(i in states){print i,states[i]}}'

[root@iz2zeaogswcq4v6abnhz7rz changwu]# ss -antp|grep 80|awk '{states[$1]++};END{for(i in states){print i,states[i]}}'
LISTEN 2
ESTAB 1

```



* 统计访问网站的ip数量

```bash
netstat -ant | grep :80 | awk -F: '{ip_count[$8]++};END{for(i in ip_count){print i,ip_count[i]}}' | sort
```

* 统计网站日志中的PV量

```bash
# 统计PV量, 统计日志
grep '3/April/2020' mysqladmin.cc-access_log | wc -l

# 统计Apache/nginx日志中某一天不同ip的访问量
grep '3/April/2020' mysqladmin.cc-access_log | awk '{ips[$1]++} ; END{for(i in ips){print i,ips[i]}}'|sory -k2 -rn |head

```



* PV: PageView , 网站的浏览量
  * 用户打开一次网页, 计数+1, 多次打开一个网页也PV也会增加
* VV: Visit View (访问次数)
  * 用户来到网站到最终关闭网站的所有页面, 记作1次, 若30分钟内没有打开, 或者刷新页面, 或者关闭了浏览器, 认为本次访问结束
* UV: Unique Visitor (独立访问数)
  * 1天内, 相同的访客, 多次访问您的网站, 只算作一个UV
* 独立IP (IP)
  * 指的是, 一天内, 使用不同的ip地址访问网站的数量,  同一个IP无论访问多少次, 独立IP数都仅仅记作1















