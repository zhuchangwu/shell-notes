### 拓展expect

**使用expect 实现在在脚本中的自动应答**

检查是否已经安装

```bash
[root@iz2zeaogswcq4v6abnhz7rz ~]# yum list | grep expect
expect.x86_64                            5.45-14.el7_1                 base     
expect-devel.i686                        5.45-14.el7_1                 base     
expect-devel.x86_64                      5.45-14.el7_1                 base     
expectk.x86_64                           5.45-14.el7_1                 base     
nodejs-expect-dot-js.noarch              0.2.0-5.el7                   epel     
pexpect.noarch                           2.3-11.el7                    base     
python2-aexpect.noarch                   1.5.1-1.el7                   epel     
python36-pexpect.noarch                  4.6-2.el7                     epel     
rubygem-rspec-expectations.noarch        2.14.5-2.el7.1                epel     
rubygem-rspec-expectations-doc.noarch    2.14.5-2.el7.1                epel
```



安装命令

```bash
yum install -y expect
```



查看刚才安装了哪些包

```bash
[root@iz2zeaogswcq4v6abnhz7rz ~]# rpm -ql expect
/usr/bin/autoexpect
/usr/bin/dislocate

# 重点使用这个包
/usr/bin/expect

/usr/bin/ftp-rfc
/usr/bin/kibitz
/usr/bin/lpunlock
/usr/bin/mkpasswd
/usr/bin/passmass
/usr/bin/rftp
/usr/bin/rlogin-cwd
/usr/bin/timed-read
/usr/bin/timed-run
/usr/bin/unbuffer
/usr/bin/weather
/usr/bin/xkibitz
/usr/lib64/libexpect.so
/usr/lib64/libexpect5.45.so
/usr/lib64/tcl8.5/expect5.45
/usr/lib64/tcl8.5/expect5.45/pkgIndex.tcl
/usr/share/doc/expect-5.45
/usr/share/doc/expect-5.45/FAQ
/usr/share/doc/expect-5.45/HISTORY
/usr/share/doc/expect-5.45/NEWS
/usr/share/doc/expect-5.45/README
/usr/share/man/man1/autoexpect.1.gz
/usr/share/man/man1/dislocate.1.gz
/usr/share/man/man1/expect.1.gz
/usr/share/man/man1/kibitz.1.gz
/usr/share/man/man1/mkpasswd.1.gz
/usr/share/man/man1/passmass.1.gz
/usr/share/man/man1/tknewsbiff.1.gz
/usr/share/man/man1/unbuffer.1.gz
/usr/share/man/man1/xkibitz.1.gz

```



#### 案例1:

A远程登录到server上后什么都不做

```bash
	#!/usr/bin/expect
	# 开启程序
	spawn ssh root@101.200.15.233
	
	# 捕获相关内容
	expect {
		# 自动响应, 遇到(yes/no)? 发送 yes ,  \r表示按下回车键
        "yes/no?" {send "yes\r";exp_continue} 
         # 自动响应, 遇到password:? 发送 123456密码
        "password:" {send "2424zw\r"}
	}
	# 交互
	interact 
```

脚本执行的方式

```bash
expect -f expect.sh
```



定义变量:

```bash
	#!/usr/bin/expect
	
	set ip 101.200.19.233
	set pass 123456
	set timeout 5
	
	# 开启程序
	spawn ssh root@$ip
	
	# 捕获相关内容
	expect {
		# 自动响应, 遇到(yes/no)? 发送 yes ,  \r表示按下回车键
        "yes/no?" {send "yes\r";exp_continue} 
         # 自动响应, 遇到password:? 发送 123456密码
        "password:" {send "$pass\r"}
	}
	# 交互
	interact 
```



#### 案例2: 

A远程登录到别的主机上面做操作

```bash
	#!/usr/bin/expect
	
	set ip 101.200.159.233
	set pass 242zc.
	set timeout 5
	
	# 开启程序
	spawn ssh root@$ip
	
	# 捕获相关内容
	expect {
		# 自动响应, 遇到(yes/no)? 发送 yes ,  \r表示按下回车键
        "yes/no?" {send "yes\r";exp_continue} 
         # 自动响应, 遇到password:? 发送 123456密码
        "password:" {send "$pass\r"}
	}
	# 开始做事情
	expect "#"
	send "touch /tmp/expect_file{1..3}\r"
	send "date +%F\r"
	send "exit\r"
	# 结束
	expect eof
```

获取出用户输入的参数

```bash
set ip [ lindex $argv 0 ] # 用户输入的第一个参数
set pass [ lindex $argv 1 ] # 用户输入的第二个参数
```



#### 案例3

shell + expect 结合使用, 在多台服务器上创建1个用户

```bash
	#!/bin/env bash
	
	set ip 101.200.159.233 
	set pass 242cw 
	while read ip pass 
	do	
		# 遇到END, 结束程序
		# 如果-END 的-省略, 那么下面的创建用户下面的END需要顶格写
		/usr/bin/expect <<-END &>/dev/null
		 spawn ssh root@$ip
		 expect{
             	# 自动响应, 遇到(yes/no)? 发送 yes ,  \r表示按下回车键
       			 "yes/no?" {send "yes\r";exp_continue} 
         		# 自动响应, 遇到password:? 发送 123456密码
        		"password:" {send "$pass\r"}
		   }
		 # 创建用户
		 expect "#" {send "useradd yy1\r"}
		 expect eof
		 END
	done < ip.txt
```



### sudo 命令

普通用户有很多权限的限制, **普通用户不能创建用户, 管理用户, 安装软件包, 重启网卡, 关机, 重启机器**

 通过sudo可以实现为普通用户进行授权

设置sudo配置, 命令;

```bash
visudo
```

添加权限

```bash
	# root用户拥有所有的权限
	root    ALL=(ALL) ALL
	# yunwei 用户可以通过 sudo 使用 root 用户的所有权限
	# 使用sudo时, 不用输入密码
	# 但是不包含, 关机, 重启, 强制删除
	yunwei  ALL=(root) NOPASSWD:ALL,!/sbin/shutdown,!/sbin/init,!/bin/rm -rf
```

















