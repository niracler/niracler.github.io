---
title: Linux | 课程设计 ftp配置文件内容
date: 2018-03-08 09:06:40
tags: [linux，]
---

## 这两个地方是要改
```
# 用户限制在主目录
DefaultRoot			~

# Umask是要修改的
Umask 000 # 解决学生作业子目录无法上交的权限问题
```

<!-- more -->

# 这个很重要，一定要加
```

<Directory "~/学生作业" >
Umask 000 # 解决学生作业子目录无法上交的权限问题
<Limit RMD DELE RETR>
DenyGroup student
</Limit>
</Directory>

<Directory "~/资源共享" >
Umask 000 # 解决学生作业子目录无法上交的权限问题
<Limit RMD DELE STOR>
DenyGroup student
</Limit>
</Directory>

```

# 修改之后要重启ftp服务器!!!!
```
$ /etc/init.d/proftpd restart
```

# 下面是我加了之后的配置文件，可以不看
```#
# /etc/proftpd/proftpd.conf -- This is a basic ProFTPD configuration file.
# To really apply changes, reload proftpd after modifications, if
# it runs in daemon mode. It is not required in inetd/xinetd mode.
#

# Includes DSO modules
Include /etc/proftpd/modules.conf

# If set on you can experience a longer connection delay in many cases.
IdentLookups			off

#配置服务器名
ServerName			"niracler FTP Server"

#设置服务器运行模式，独立服务，或者被监管
ServerType			standalone

#设置为默认服务器
DefaultServer			on

# Set off to disable IPv6 support which is annoying on IPv4 only boxes.
#设置关闭IPv6支持
UseIPv6			        off

#设置服务器接受请求的端口
Port				21

#设置被动模式使用的端口范围
PassivePorts 			60000 65535

#设置用户上传文件的权限掩码
Umask				000 000

#设置用户被chroot锁定到的各自的Home目录
DefaultRoot			~

#关闭欢迎信息显示
DeferWelcome			off

#如果显示欢迎信息，则指定显示的文件
DisplayLogin			welcome.msg

#指定切换文件夹时，显示的欢迎信息
DisplayChdir               	.message true

#设置日志
TransferLog /var/log/proftpd/xferlog
SystemLog   /var/log/proftpd/proftpd.log

MultilineRFC2228		on
ShowSymlinks			on

TimeoutNoTransfer		600
TimeoutStalled			600
TimeoutIdle			1200


ListOptions                	"-l"

DenyFilter			\*.*/



<IfModule mod_dynmasq.c>
# DynMasqRefresh 28800
</IfModule>


#设置服务器进程运行使用的用户
User				proftpd

#设置服务器进程运行使用的组
Group			        nogroup

# (such as xinetd)
MaxInstances			30

# Normally, we want files to be overwriteable.
AllowOverwrite			on



<IfModule mod_quotatab.c>
QuotaEngine off
</IfModule>

<IfModule mod_ratio.c>
Ratios off
</IfModule>


# Delay engine reduces impact of the so-called Timing Attack described in
# http://www.securityfocus.com/bid/11430/discuss
# It is on by default.
<IfModule mod_delay.c>
DelayEngine on
</IfModule>

<IfModule mod_ctrls.c>
ControlsEngine        off
ControlsMaxClients    2
ControlsLog           /var/log/proftpd/controls.log
ControlsInterval      5
ControlsSocket        /var/run/proftpd/proftpd.sock
</IfModule>

<IfModule mod_ctrls_admin.c>
AdminControlsEngine off
</IfModule>

<Directory "~/学生作业" >
Umask 000 # 解决学生作业子目录无法上交的权限问题
<Limit RMD DELE RETR>
DenyGroup student
</Limit>
</Directory>

<Directory "~/资源共享" >
Umask 000 # 解决学生作业子目录无法上交的权限问题
<Limit RMD DELE STOR>
DenyGroup student
</Limit>
</Directory>

# Include other custom configuration files
Include /etc/proftpd/conf.d/

```
