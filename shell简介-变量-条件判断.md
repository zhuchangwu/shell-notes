### 什么是Shell脚本?

将需要执行的命令保存在文件中,按照顺序依次执行

shell脚本是解释型的语言



### 能干啥?

* 自动化部署 LAMP/LNMP/Tomcat
* 自动化管理 (系统初始化脚本, 批量更改主机密码,推送公钥)
* 自动化运维 (统计网站访问量)
* 自动化备份 (数据库的备份,日志的转储)



### 基本写法

第一部行 , 通过魔法字符`#!`指定

```bash
 #!/bin/bash 
```

如果直接将解释器写死在脚本里面, 可能因为系统找不到解释器而产生兼容问题, 所以我们可以像下面这样

```bash
 #!/bin/env bash
```

第二, 三 部分, 通过注释符`#` 对脚本进行描述

```bash
	# Name:
	# Desc:
	# Path:
	# Usage: 用法
	# Update: 更新时间
	
	# 如下是第三部分, 命令集
	commands
	...
```

### 脚本的执行

#### 方式1: 

1. 使用绝对路径执行脚本, 从 `/` 开始
2. 使用相对路径执行脚本

>  **方式1, 要求我们执行的脚本具有x权限**

#### 方式2:

通过指定解释器 如 bash 或者 sh 去执行指定的脚本, **此时脚本不要求具有x权限**

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# bash firstBash.sh 
hello shell

[root@iz2zeaogswcq4v6abnhz7rz changwu]# sh firstBash.sh 
hello shell
```



**通常有下面两个技巧**

* **我们使用方法2中的  `bash -x` 去看一下我们的脚本执行的过程中出现了什么问题**

```bash
	bash -x xxx.sh
```

* **我们使用方法2中的  `bash -n` 去看一下我们的脚本是否存在语法问题**

```bash
	bash -n xxx.sh
```

* 通过source 命令执行脚本

```bash
	source xxx.sh
```

总结就是 : shell有两种标准执行方式, 三种非标准的执行方式





### 变量的定义

语法:  `变量名 = 变量值`

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# A=hello
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo A
A
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo ${A}  只要取值就需要$
hello
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $A    只要取值就需要$
hello
[root@iz2zeaogswcq4v6abnhz7rz changwu]# A=world    重复赋值, 会覆盖旧值
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $A   
world
[root@iz2zeaogswcq4v6abnhz7rz changwu]# unset A    取消变量
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $A

[root@iz2zeaogswcq4v6abnhz7rz changwu]# 

```

小结: 

* 变量要尽量见名知意
* 变量区分大小写
* 变量名不能存在特殊字符, 诸如 * 
* `变量=值` 变量名和值之间不能存在空格
* 对于值为一个串, 且存在空格, 那么赋值时, 需要使用`""` 将值包裹起来
* 变量不能以数字开头, 但是可以有数字存在

#### 将命令的结果赋值给变量

可以通过`$() 和 ``实现

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# A=$(uname -r)
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $A
3.10.0-514.26.2.el7.x86_64

[root@iz2zeaogswcq4v6abnhz7rz changwu]# A=`uname -r`
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $A
3.10.0-514.26.2.el7.x86_64
```





#### `$A 和 ${A}`的区别

看下面的示例, 可以使用${A : 从第几位开始切 : 切几位}

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# A=123456
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $A
123456
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo ${A:2:4}
3456
```



#### 交互式为变量赋值

* 用户输入

语法: `read [选项] 变量名`

```bash
read -p "请输入姓名"  -n 5  -s -t name
-p: 描述信息
-n: 定义字符数, 超过长度的字符数无效
-s: 不显示用户的输入内容
-t: 超时时间, 默认为秒, 超时不提交, 将被清空
```



* 文件值导入变量

实例如下:  需要通过 `<` 改表输入流来实现下面的功能

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# read -p "read ip from ips" ip1 ip2 < ips
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $ip1
1.1.1.1
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $ip2
2.2.2.2
```



#### 定义指定类型的变量

如: 给变量做一些限制 (只读) , 固定变量的类型 (int)

语法: 

```bash
declare 选项 变量名=变量值
有如下可选项
-i: 将变量看作
-r: 只读
-a: 普通数组
-A: 关联数组
-x: 将变量通过环境变量导出 == 将变量设置成环境变量
```



### 变量的分类

#### 本地变量

用户自定义的变量,仅仅在当前进程 (bash)中有效

在其他进程和子进程中无效

#### 环境变量

环境变量是属于某一个用户的变量, 环境话说, tom和jerry有各自的环境变量,  并且他彼此看不到对方的环境变量

在当前进程 (bash), 其他进程和子进程中都有效

* `env` 查看当前用户的环境变量

* `set` 查询当前用户所有的变量

* 添加临时环境变量:

   
####方式1:

  export变量名 变量值

#### 方式2:

  变量名=变量值
  export 变量名
   ```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# export name="tom"
[root@iz2zeaogswcq4v6abnhz7rz changwu]# env |grep name
name=tom

前面也说了通过 declare -x 也能实现添加临时环境变量
[root@iz2zeaogswcq4v6abnhz7rz changwu]# declare -x name=jerry
[root@iz2zeaogswcq4v6abnhz7rz changwu]# env | grep name
name=jerry
   ```

如果想永久改变环境变量如下:

```bash
	# 方式1: 
	vim /etc/profile   
	# 方式2
	vim ~/.bashrc
	
	添加如下值:
	name=tom
	或者
	export name=tom
```



#### 全局变量

全局变量就是我们定义在配置文件中的变量

比如我们安装的jdk, 就需要修改全局变量 == > 环境变量的全局配置, 这里面的变量是所有的用户共享的

全局配置文件的位置

```bash
/etc/profile 
```

如果我们仅仅为某一个用户安装jdk环境, 并希望永久生效的话, 只需要修改`~/.bash_prfile`就行



小总结:

| 文件名              | 说明           | 备注                  |
| ------------------- | -------------- | --------------------- |
| $HOME/.bashrc       | 用户登录时读取 | 定义别名, umask, 函数 |
| $HOME/.bash_profile | 用户登录时读取 |                       |
| $HOME/.bash_logout  | 用户退出时读取 |                       |
| /etc/bashrc         | 全局bash信息,  | 所有用户都生效        |
| /etc/profile        | 全局环境变量   | 系统和所有用户都生效  |

**上述的文件修还后, 都需要使用`source`命令让其生效**



**用户登录系统时, 配置文件的读取顺序**

1. `/etc/profile`
2. `$HOME/.bash_profile`
3. `$HOME/bash_rc`
4. `/etc/bashrc`
5. `$HOME/.bash_logout`



#### 系统变量

系统内置的bash中的变量,  指的是当前系统中bash内置的变量, 这写变量我们是不能更改的

参见如下示例理解系统变量的使用

假设我有如下的脚本:

```bash
echo "\$0 = $0"  # 当前的脚本名
echo "\$# = $#"  # 脚本名后参数的个数
echo "\$* = $*"  # 脚本后的所有参数, 参数当成一个整体输出, 每一个参数的之间用空格隔开
echo "\$@ = $@"  # 脚本后的所有参数, 参数之间独立输出
echo "\$1 = $1"  # 第一个参数
echo "\$2 = $2"
echo "\$3 = $3"
```

执行: 脚本

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# ./firstBash.sh a b c
$0 = ./firstBash.sh
$# = 3
$* = a b c
$@ = a b c
$1 = a
$2 = b
$3 = c
shell end

```



其他系统变量

```bash
${10} ~ ${n} # 第10 ~ n 个参数
$$ 当前进程的进程号
$! 当前终端最后一个被放在后台运行的进程号
```



### 简单的四则运算

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $((1+1))
2
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $[1+1]
2

[root@iz2zeaogswcq4v6abnhz7rz changwu]# expr 1+1
1+1
[root@iz2zeaogswcq4v6abnhz7rz changwu]# expr 1 + 1
2

[root@iz2zeaogswcq4v6abnhz7rz changwu]# expr 1 * 1
expr: syntax error

# 特殊符号需要进行转义
[root@iz2zeaogswcq4v6abnhz7rz changwu]# expr 1 \* 1
1
[root@iz2zeaogswcq4v6abnhz7rz changwu]# expr 1 \/ 1
1

# 使用let得提前定义一下变量
[root@iz2zeaogswcq4v6abnhz7rz changwu]# n=1; let n+=2
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $n
3

[root@iz2zeaogswcq4v6abnhz7rz changwu]# let n+=2
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $n
2

# 求多乘
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $n
2
[root@iz2zeaogswcq4v6abnhz7rz changwu]# let n=n**3
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $n
8

# 注意如下的技巧, 整形和浮点型直接相加会报错, 使用bc解决
[root@iz2zeaogswcq4v6abnhz7rz changwu]# $[1+1.5]
-bash: 1+1.5: syntax error: invalid arithmetic operator (error token is ".5")

[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo  1+1.5|bc
2.5


```



### 条件判断

#### 语法格式:

1. 格式1:  `test 条件表达式`
2. 格式2:  `[ 条件表达式,两边都有空格 ]`
3. 格式3: ` [[ 条件表达式,两边都有空格 ]`   支持正则



#### 判断文件类型

```bash
-e	判断文件是否存在
-s	判断文件是否存在并且是一个非空文件

 # linux中文件的七种基本类型
-f	判断文件是否存在并且是一个普通文件
-d	判断文件是否存在并且是一个目录
-L	判断文件是否存在并且是一个软链接
-b	判断文件是否存在并且是一个设备文件
-S	判断文件是否存在并且是一个套接字文件
-c	判断文件是否存在并且是一个字符设备文件
-P	判断文件是否存在并且是一个命名管道文件
```

实例

```bash
# 判断文件是否存在, 0表示真  1表示假
[root@iz2zeaogswcq4v6abnhz7rz changwu]# test -e ./ips 
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $?
0
[root@iz2zeaogswcq4v6abnhz7rz changwu]# test -e ./ips2
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $?
1


# 判断目录是否存在
```



#### 判断文件权限

```bash
-r   判断当前用户是否可以对当前文件可读
-w   判断当前用户是否可以对当前文件可写
-x   判断当前用户是否可以对当前文件可执行

-u   判断是否有suid, 高级权限冒险位
-g   判断是否sgid, 高级权限强制位
-k   判断是否有t位, 高级权限粘滞位 , (处理文件的创建者和root, 其他人不能删除这个文件)
```



#### 判断文件的新旧

```bash
	file1 -nt file2 # 比较file1是否比file2新
	file1 -ot file2 # 比较file1是否比file2旧
	file1 -et file2 # 比较file1和file2是否是同一个文件, 是否是硬连接, 指向了同一个inode
```



#### 判断整数

```bash
-eq		是否等于
-ne		不等于
-gt	 	大于
-lt		小于
-ge		大于等于
-le		小于等于
```



#### 判断字符串

```bash
-z	 判断是否是空字符串, 长度为0则成立
-n   判断是否为非空字符串, 长度为0不成立
string1 = string2  判断字符串是否相等
string1 != string2  判断字符串是否不相等
```



实例:

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# test -z "hello";echo $?
1

```

#### 逻辑运算符

```bash
&& 和 -a  # 逻辑与
-o 和 ||   # 逻辑或
```

特性和其他高级语言的特性一样, 具有短路的作用





#### 类C的风格进行数值的比较

```bash
# 其中 == 是比较, =是赋值
((1==2));echo $?
((1<2));echo $?
((1!=2));echo $?
((`id - u`<2));echo $?

#  " "括起来表示一个整体
[root@iz2zeaogswcq4v6abnhz7rz changwu]# a="hello";b="world"
[root@iz2zeaogswcq4v6abnhz7rz changwu]# [ "$a" == "$b" ];echo $?
1
[root@iz2zeaogswcq4v6abnhz7rz changwu]# [ "$a" != "$b" ];echo $?
0

[root@iz2zeaogswcq4v6abnhz7rz changwu]# word=$a
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $word
hello
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo "$word"
hello
```



### 流程控制语句



#### if 语法: 

```bash
# 方式1
if [ condition ];then
	command
	command
fi # 结束符

# 方式2
if [[ condition ]];then
	command
	command
fi # 结束符

# 方式3
if test condition;then
	command
	command
fi # 结束符


# 可以这样使用
 [ 条件 ] && command # 如果满足条件我们就执行command
```



#### if-else

```bash
if [ condition ];then
		command1
	else
		command2
fi

等同于
[ 条件 ] && command1 || command2
```

#### if-elif-else

```bash
	if [ condition1 ];then
			command 
		elif [ condition2 ];then
			command
		else
			command
	fi
```

#### 应用案例1 :

**判断当前主机和远程主机是否ping通**

```bash
	#!/bin/env bash
	# DESC: 判断当前主机和远程主机时候相通 211.159.173.151
	# Date: 2020-04-01
	# Auth: changwu
	 
     read  -p "请输入您要ping的主机ip" ip
     ping -c 3 $ip &> /dev/null # ping命令的输出扔掉
     if [ $? -eq 0 ];then
     	echo "$ip 链路通"
     else
     	echo "$ip 链路不通"
     fi

```



#### 应用案例2:

**判断一个进程是否存在**

可以通过下面的两个命令实现去判断进程是否存在, ps 和 pgrep

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# ps -ef | grep nginx
root      2194  2102  0 08:59 pts/0    00:00:00 grep --color=auto nginx
root      3343     1  0 Mar13 ?        00:00:00 nginx: master process nginx
nginx     3622  3343  0 Mar13 ?        00:00:00 nginx: worker process
[root@iz2zeaogswcq4v6abnhz7rz changwu]# pgrep nginx
3343
3622
[root@iz2zeaogswcq4v6abnhz7rz changwu]# echo $?
0

```

落地实现:

```bash	
	#!/bin/env bash
	#Auth: changwu
	#Desc: 判断进程是否存在
	
	read -p "请输入您想检查的进程名" name
	pgrep $name &> /dev/null
	if [ $? -eq 1];then
			echo "$name 进程不存在"
		else
			echo "$name 进程存在"
	fi
```



#### 应用案例3:

**判断一个服务器是否正常**

在linux系统中可以使用下面的三种命令: `wget` 和 `curl` 和 `elinks`

优先选择 `wget` 因为它返回的内容最少, 最快

```bash
[root@iz2zeaogswcq4v6abnhz7rz changwu]# wget http://www.baidu.com
--2020-04-01 09:14:33--  http://www.baidu.com/
Resolving www.baidu.com (www.baidu.com)... 220.181.38.150, 220.181.38.149, 240e:83:205:59:0:ff:b09b:159e, ...
Connecting to www.baidu.com (www.baidu.com)|220.181.38.150|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2381 (2.3K) [text/html]
Saving to: ‘index.html.4’

100%[=====================================================================================>] 2,381       --.-K/s   in 0s      

2020-04-01 09:14:33 (248 MB/s) - ‘index.html.4’ saved [2381/2381]


# curl 可以访问到html回来
[root@iz2zeaogswcq4v6abnhz7rz changwu]# curl http://www.baidu.com
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>


# elinks 默认会进入交互的状态, 添加-dump命令, 仅打印,而不进入交互状态
[root@iz2zeaogswcq4v6abnhz7rz changwu]# elinks -dump http://www.baidu.com
   Refresh: [1]/baidu.html?from=noscript
   Link: [2]百度搜索 (search)
   Link: [3]dns-prefetch
   Link: [4]dns-prefetch
   Link: [5]dns-prefetch
   Link: [6]dns-prefetch
   Link: [7]dns-prefetch
   Link: [8]dns-prefetch
                          [9]百度首页[10]设置[11]登录
   [12]抗击肺炎[13]新闻[14]hao123[15]地图[16]视频[17]贴吧[18]学术[19]登录[20]
                                设置[21]更多产品
                            [22][USEMAP][23][USEMAP]
                          [24]到百度首页[25]到百度首页
                   [26]_____________________ [27][ 百度一下 ]
                                     输入法

     * [28]手写
     * [29]拼音
     * 
     * [30]关闭

   [31]设为首页

   [32]关于百度

   [33]About Baidu

   [34]百度推广

   [35]使用百度前必读

   [36]意见反馈

   [37]帮助中心
   ©2020 Baidu (京)-经营性-2017-0020[38]京公网安备11000002000001号京ICP
   证030173号
   网页[39]资讯[40]贴吧[41]知道[42]音乐[43]图片[44]视频[45]地图[46]文库[47]更
   多»

                                  下载百度APP

                             有事搜一搜  没事看一看

References

   Visible links
   1. http://www.baidu.com/baidu.html?from=noscript
   2. http://www.baidu.com/content-search.xml
   3. http://dss0.bdstatic.com/
   4. http://dss1.bdstatic.com/
   5. http://ss1.bdstatic.com/
   6. http://sp0.baidu.com/
   7. http://sp1.baidu.com/
   8. http://sp2.baidu.com/
   9. http://www.baidu.com/
  10. javascript:;
  11. https://passport.baidu.com/v2/?login&tpl=mn&u=http%3A%2F%2Fwww.baidu.com%2F&sms=5
  12. https://voice.baidu.com/act/newpneumonia/newpneumonia/?from=osari_pc_1
  13. http://news.baidu.com/
  14. https://www.hao123.com/
  15. http://map.baidu.com/
  16. http://v.baidu.com/
  17. http://tieba.baidu.com/
  18. http://xueshu.baidu.com/
  19. https://passport.baidu.com/v2/?login&tpl=mn&u=http%3A%2F%2Fwww.baidu.com%2F&sms=5
  20. http://www.baidu.com/gaoji/preferences.html
  21. http://www.baidu.com/more/
  22. http://www.baidu.com/#mp
  23. http://www.baidu.com/#mp
  24. 到百度首页
	http://www.baidu.com/
  25. 到百度首页
	http://www.baidu.com/
  28. javascript:;
  29. javascript:;
  30. javascript:;
  31. http://www.baidu.com/cache/sethelp/index.html
  32. http://home.baidu.com/
  33. http://ir.baidu.com/
  34. http://e.baidu.com/?refer=888
  35. http://www.baidu.com/duty
  36. http://help.baidu.com/newadd?prod_id=1&category=4
  37. http://help.baidu.com/
  38. http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=11000002000001
  39. http://www.baidu.com/s?rtt=1&bsst=1&cl=2&tn=news&word=
  40. http://tieba.baidu.com/f?kw=&fr=wwwt
  41. http://zhidao.baidu.com/q?ct=17&pn=0&tn=ikaslist&rn=10&word=&fr=wwwt
  42. http://music.taihe.com/search?fr=ps&ie=utf-8&key=
  43. http://image.baidu.com/search/index?tn=baiduimage&ps=1&ct=201326592&lm=-1&cl=2&nc=1&ie=utf-8&word=
  44. http://v.baidu.com/v?ct=301989888&rn=20&pn=0&db=0&s=25&ie=utf-8&word=
  45. http://map.baidu.com/m?word=&fr=ps01000
  46. http://wenku.baidu.com/search?word=&lm=0&od=0&ie=utf-8
  47. http://www.baidu.com/more/

```

访问成功 `$?`为0 , 访问失败则为非0



实现: 

```bash
#!/bin/env bash
#Desc:检查网站是否可以正常访问
	
	web_server=www.baidu.com
	wget -P /home  $web_server &> /dev/null  # 指定下载位置, 并输出扔掉
	if [ $? -eq 0 ];then
		  rm -rf /home/index.* # 删除下载的文件
	  	  echo "服务器可正常访问"
	     else
		  echo "访问异常"
	fi
```



#### 案例4

给定的目录不存在就创建

```bash
	# 方式1
	[ ! -d /home/xxx ] && mkdir /home/xxx -p
	# 方式2
	if [ ! -d /home/xxx ];then mkdir /home/xxx -p fi
```







