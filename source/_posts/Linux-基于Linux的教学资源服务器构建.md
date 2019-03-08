---
title: Linux | 基于Linux的教学资源服务器构建
date: 2018-03-08 09:05:55
tags:
  - linux
---

### 先是关于几点注意

* 本人是在阿里云服务器上搭建的教学资源服务器，若有不适合的地方，请酌情修改，或直接来问我。
* 我这里的所有命令都是没有sudo的，因为我就是root用户。有需要自己加。
* [zsh 和 fish 安装与使用](https://www.jianshu.com/p/43b7e6f069f9)，工欲善其事,必先利其器

<!-- more -->

### 这里开始是ftp搭建过程

**1. Proftpd安装**
```bash
$ apt-get update    # 更新包信息
$ apt-get upgrade  # 升级
$ apt-get install proftpd # 安装proftp
```
![](http://upload-images.jianshu.io/upload_images/4781155-89214869b66827aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择“standalone”
查看proftpd状态
```shell
$ /etc/init.d/proftpd status
```
**2. 编辑配置文件**

```shell
$ vim /etc/proftpd/proftpd.conf
```

**[文件内容](https://www.jianshu.com/p/e1546998c254)**

**3. 重启服务器**

```shell
$ /etc/init.d/proftpd restart
```

**4. Ubuntu系统配置nologin**

这步是为了防止ftp用户用终端登录
```shell
$ vim /etc/shells
```

最后一行增加/usr/sbin/nologin

**5. 创建用户组**

```shell
$ groupadd student
$ groupadd teacher
```

**6. 创建账号**

```shell
# 创建用户
$ useradd -m fuckhu -g teacher -s /usr/sbin/nologin
$ useradd fuckhus -g student -s /usr/sbin/nologin -d /home/fuckhu/

#设置账户密码
$ echo -e "fuckhu:123" | sudo chpasswd
$ echo -e "fuckhus:123" | sudo chpasswd
```

**7. 创建目录**

```shell
$ cd /home/fuckhu/
$ mkdir 学生作业
$ mkdir 资源共享

# 设置文件所属组和所属用户
$ chown fuckhu 学生作业/
$ chown :teacher 学生作业/
$ chown fuckhu 资源共享/
$ chown :teacher 资源共享/

# 给学生加个写作业的权限
$ chmod o+w 学生作业/
```
教师私人账号功能测试

![](http://upload-images.jianshu.io/upload_images/4781155-261b0b46cf6fd1ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

教师公开账号功能测试

![](http://upload-images.jianshu.io/upload_images/4781155-7266590cb2c88b49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

大家可以自己[试试](ftp://niracler.com)
[win的用户注意](https://bbs.aliyun.com/read/323533.html?page=e)

### HTTP服务器安装与配置

**1. 软件安装**
```shell
$ apt-get update
$ apt-get install apache2
$ apt-get install apache2-doc
```

**2. 服务器配置**
```shell
$ cd /var/www/html/
$ rm index.html  # 删除原有的index文件
$ ln -s /home/fuckhu/资源共享/ fuckfu # 建立软连接
```
![](http://upload-images.jianshu.io/upload_images/4781155-a551097a9b53090f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后直接浏览器用你们的ip去访问吧。

### Samba服务器安装与配置

**1. 软件安装**
```shell
$ apt-get install libtalloc2
$ apt-get install samba
```

**2. 系统用户设置 Samba 独立的登录密码:**
```shell
$ echo -e "123\n123" | smbpasswd -a -s fuckhu
```
添加samba用户帐号，smbpasswd -a -s fuckhu 是静默方式添加samba帐号，echo -e "123\n123" 是两次输出密码 123，中间输出回车符 \n

**抱歉，外网无法测试。**

### shell管理程序的设计与实现

让程序员从一些工作中解脱出来，让你[不必重复你自己](https://link.jianshu.com/?t=https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

文件namefile.txt(请不要有多余的换行与空格)
```
zengjinping
sundawei
lishaoming
liuxixian
jiajihong
fengfuye
yujinchang
gaoyanqun
```
文件creat.sh
参考代码

```shell
#!/bin/bash
cat namefile.txt |while read name
do useradd -m $name -g teacher -s /usr/sbin/nologin
useradd ${name}s -g student -s /usr/sbin/nologin -d /home/$name/
echo -e "$name:123" | sudo chpasswd
echo -e "${name}s:123" | sudo chpasswd
cd /home/$name/
mkdir 学生作业
mkdir 资源共享
chown $name 学生作业/
chown :teacher 学生作业/
chown $name 资源共享/
chown :teacher 资源共享/
chmod o+w 学生作业/
cd /var/www/html/
ln -s /home/$name/资源共享/ $name
echo -e "123\n123" | smbpasswd -a -s $name
done
```

```shell
$ sudo bash creat.sh ##执行
```

![](http://upload-images.jianshu.io/upload_images/4781155-e1132b897ec96c89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 在此过程我所参考过的内容
* [黄培灿老师的方法](https://wenku.baidu.com/view/0546c4215901020207409c8c.html)
* [Proftpd快速搭建FTP服务器](http://blog.fens.me/linux-ftp-proftpd/)
* [Centos6配置samba服务器并批量添加用户和文件夹](https://www.cnblogs.com/panblack/p/samba_batch_adding_users.html)
* [Linux 下批量创建用户(shell 命令)](http://bbs.51cto.com/thread-1081649-1-1.html)
* [Linux ln命令 - 建立文件/目录链接](http://www.linuxidc.com/Linux/2014-12/111056.htm)
* 老师的讲义。
