---
title: linux常用命令整理，不断更新中
date: 2019-09-14 21:57:10
tags:
---

Linux命令
Linux命令常用的命令搜集

<!--more-->

1.cd

    切换目录 cd ..（返回上一级的目录） cd ~（切换到当前用户的家目录）cd - (返回上一次的目录)

2.pwd

    显示当前目录

3.mkdir

    新建一个文件夹 mkdir xiaoqian（新建xiaoqian文件夹）mkdir -p（创建没有的目录）

4.rmdir

    删除一个空文件夹

5.ls 

    显示当前目录下的文件夹 ls -l（查看详细信息）  ls -a（显示隐藏文件）ls -all（显示隐藏文件的详细信息）

6.whoami

    查看当前用户是谁

7.who

    显示当前有那些用户

8.date

    查看当前日期   更改日期 用date + 月日时分年（date '032817062018'）hwclock -s (同步系统的时间)

9.cal

    查看日历

10.clear

    清除屏幕  Ctrl + L

11.sudo -s

    切换管理员模式

12.useradd

    创建用户 useradd xiaoqian

13.passwd

    修改密码 passwd xaioqian

14.Ctrl + C

    终止当前的命令

15.exit

    退出当前的操作  也可以退出当前普通用户

16.su 

   切换用户 su - xiaoqian 管理员切换到普通用户不需要输入密码

17.man

    帮助信息查询 man useradd（查看useradd的使用方法  输入q 可以退出查询）或者使用useradd --help查询

18.init 0

    关机 或 shutdown（需要root用户才能关闭）

19.touch

    新建一个文本 touch xiaoqian.py

20.rm

    删除目录文件包括空文件rm xiaoqian（删除有提示） rm -r xiaoqian.py (强制删除)  rm -rf xiaoqian.py(一次性强制删除目录下的所有文件)

21.cp

    复制文件或目录 cp xiaoqian.py /tmp(将xiaoqian.py复制到tmp目录下)  cp -i(提示是否覆盖以前的文件) cp -r（递归拷贝目录） 

22.mv

    剪切文件或重命名 mv xiaoqian.py /tmp(将xiaoqian.py剪切到tmp目录下) mv xiaoqian.py 123.py(将xiaoqian.py改成123.py)

23.cat

    查看文件 

24.head

   显示文件前10行 tail -1 xiaoqian.py (显示文档第一行)

25.tail

    显示后面几行 tail -1 xiaoqian.py (显示文档最后一行)

 26.echo

   将信息输出到屏幕或文件中 echo '你好'>>/tmp/xaioqian.py(将信息你好打印到xiaoqian.py文件最后一行)

27.more

    查看文件只能回车翻看文件 more xiaoqiang.py

28.less

    查看文件只能上下键翻文件 more xiaoqiang.py

29.df

    显示系统属性 df -lh(使数值更加清晰)

30.ifconfig

    获取网络接口配置信息

31.ping

    网络命令 用来测试是否与目的机相通联 是否能拼通

32.od

    非文档文本文件的打开命令

33.which

    查询完整的文件名目录which ifconfig

34.whereis

    查找文档 相当于find

35.find

    搜索文件

36.tar

    压缩文件的命令 tar -cvf test.tar xiaoqian.py（把xiaoqian.py压缩成test.tar） tar -xvf test.tar(解压text.tar文件)

37.gzip

   压缩文件命令gzip  xiaoqian.py（压缩） gzip -d xiaoqiang.py.gz(解压成xiaoqian.py文件)

38.top

    显示动态进程

39.ps

    ps -aux(静态进程)

40.kill 

    杀进程 kill -9（杀不死时用）

41.reboot

   重启计算机

42.gedit

    编辑文本

43.ln

    为文件创建链接 ln -s xiaoqian.py /home (在home下面创建了一个软链接 名字相同 如果原文件丢失则文件失效) 

44.du

    显示文件或目录所占的空间大小

45.vi

    编辑文件 i（命令模式切换到编辑模式）esc（退出编辑模式）  :（进入最后一行）:x(保存当前文件) :xq(保存并退出文件)

 简单命令

ifconfig 查看服务器ip地址

ls 查看当前文件夹下的内容

pwd 查看当前所有文件夹

cd 切换文件夹

touch 如果文件不存在创建新的文件

mkdir 创建目录

rm 删除指定的文件名

clear 清屏

.     当前目录
..    上层目录
-     前一个工作目录
~    当前所在的家目录
Linux当前路径PATH

echo $PATH

输出

复制代码
/usr/local/sbin:

/usr/local/bin:

/usr/sbin:

/usr/bin:

/root/bin:
复制代码
vim的工作流程

打开文件   vim  alex.txt   进入一个命令模式

输入字母 i 进入编辑模式

输入 esc键盘 退出编辑模式  然后输入 :wq  保持并退出

递归添加文件夹

mkdir -p /tmp/{alex,wusir}
创建普通用户

useradd alex
更改密码

passwd alex
删库跑路

rm -rf   /*
cat 查看文件内容

ls -la 以树状显示文件夹,显示隐藏文件

更改主机名字

 hostnamectl   set-hostname  wangdachui
1.查看linux的path变量

echo $PATH
2.简述linux的文档目录结构
是一个树状图
定点是根目录 /
查看根目录  ls /
超级用户root家目录 /root
普通家目录 /home

3.递归创建文件夹/tmp/oldboy/python/{alex,wusir,nvshen,xiaofeng}

 mkdir -p /tmp/oldboy/python/{alex,wusir,nvshen,xiaofeng}
4.显示/tmp/下所有内容详细信息

ls /tmp/* -lh

5.简述 /  ~  - 的含义
/ 根目录
~ 用户家目录
- 上一次工作目录

6.请简述你如何使用vi命令
1.打开文件  vim  filename。此时进入了一个命令模式
2.输入  字母i  ，进入编辑模式
3. 输入 esc 键盘 ，退出编辑模式，此时输入  :wq  写入代码，并且保存退出 

7.查看/etc/passwd的内容并且打印行号

cat -n /etc/passwd

8.查看文本有哪些命令？

vi
vim
cat

9.linux xshell常用快捷键？

tab 自动补全
ctrl + l 清屏
ctrl + c 终止
shift + ctrl + r 快速链接对话
ctrl + d   是快速推出会话

10.如何用echo清空一个文件？

echo > xx.txt

11.复制/tmp/下所有内容到/home，在修改文件前，先拷贝一份，防止内容被破坏

cp -r /tmp/*  /home

12.重命名test.py为my.py

mv test.py my.py

13.强制删除/tmp下内容

rm -rf /tmp/*

14.找到服务器上的settings.py

find / -type f -name settings.py

15.找到/etc下的网卡配置文件，提示网卡配置文件名是ifc开头
find /etc -name ifc*

这里题目，请先cp /etc/passwd /tmp/   拷贝文件
16.过滤出/tmp/passwd下有关root的信息
cat /etc/passwd |grep '^root'

17.过滤出/tmp/passwd下除了/sbin/nologin的信息，且打印行号
cat /etc/passwd |grep -v /sbin/nologin -n

18.查看/tmp/passwd前25行
head -25 /tmp/passwd

19.查看/tm/passwd后3行
tail -3 /tmp/passwd

20.不间断打印/var/log/py.log的信息
tail -f  /var/log/py.log


23.配置rm别名为“禁止你用rm，谢谢”，然后取消别名
alias = “ echo 禁止你用rm，谢谢"
unalias rm

24.将服务器1的/tmp/my.py远程传输到服务器2的/opt/目录下
scp /tmp/my.py    root@服务器2地址ip:/opt/

25.将服务器2的/opt/test.py拷贝到服务器1的/home目录下
scp -r root@服务器2地址IP:opt/test.py   /home

26.统计/var/log/文件夹大小
du -sh /var/log/

27.简述top的常见参数
-b 批处理
-c 显示完整的治命令
-I 忽略失效过程
-s 保密模式
-S 累积模式

28.给settings.py加锁，禁止删除
lsattr settings.py  #查看是否加锁

chattr +a settings.py #加锁

chattr -a settings.py #解除

29.同步服务器时间到ntp.aliyun.com
ntpdate -u ntp.aliyun.com

30.下载http://pythonav.cn/xiaobo.jpg图片
wget http://pythonav.cn/xiaobo.jpg



在编辑文件显示显示行号

:set nu

echo追加写入文件
    >  重定向覆盖输出符
    >> 重定向追加输入符

head  -10  filename  #查看前10行
tail -10  filename  #查看后10行

实时监控 tail -f   filename

复制的命令
    cp  文件  新文件夹
    cp -r  文件夹 新文件夹
历史记录命令 
    history  历史记录命令，记录系统的命令敲过的历史 以!加数字执行

查找命令，查找机器上的文件，或者文件夹

   find      /     -name  views.py        #找到机器上所有的 views.py 
   find   /etc  -name  *.py            #找到/etc目录下 所有名字叫做 .py的文件
   find  /  -name   python*            #找到 机器上 所有的python开头的文件和文件夹
   find  /  -type  f  -name  python*    #找到机器上 所有以python开头的文件
   find  /  -type  d    -name  python*    #找到机器上所有python开头的文件夹

查看命令帮助信息，如何查看有什么参数？
    http://linux.51yip.com/

linux的系统管道符
     grep  -v   "^$"  settings.py |  grep  -i "allow"

  -v 取反     -i  忽略大小写

alias添加别名

      alias rm="echo 请不要删库"
    
      unalias  rm 取消别名

.更改主机名字
     hostnamectl   set-hostname  xxx

.xshell的快捷键
    ctrl + l  清屏 
    ctrl + d  是快速推出会话  logout  
    shift + ctrl + r  是快速连接会话

远程传输文件的命令
    
    scp  你想传输的内容     你想传输到的地方 
    
    #实例
    #把本地的test.sh 传输到远程服务器上 
    scp  test.sh     root@服务器ip:/opt/
    
    #把远端服务器的内容，传输到自己的机器上
    
    scp -r   root@123.206.16.61:/opt/pythonav    /tmp/

 


给文件加锁

  chattr +a  fildname

给文件解锁

  chattr -a fildname

查看隐藏锁

 lschattr fildname

wget  资源的url
    wget http://www.xiaohuar.com/d/file/20190227/257e9f91df2bbd45c537f9416ae3afbb.jpg

统计文件 文件夹大小的命令
    ls -lh  方式1
    du -h  文件  #统计文件大小
    du -sh  .  #统计当前文件夹大小合计
    du -sh  /var/log   #统计/var/log大小

linux的任务管理器

      top

linux的时间命令

      date  可以查看当前时间日期

进行linux时间同步的命令
    ntpdate -u  ntp.aliyun.com

下载python3，进行编译安装，运行django程序
        1.解决python编译安装所需的软件依赖
        yum install gcc patch libffi-devel python-devel  zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel -y
        
        2.下载python3源码包
            wget https://www.python.org/ftp/python/3.6.7/Python-3.6.7.tar.xz
            
            xz -d Python-3.6.7.tar.xz
            tar -xf Python-3.6.7.tar        #解压缩，生成一个python源码文件夹
        
        3.进入python源码文件夹
            cd Python-3.6.7 
        4.进行编译安装，编译三部曲
            1.第一曲
                ./configure --prefix=/opt/python36/
                        --prefix  参数解释：指定python36安装的位置 
            2.第二曲
                make 
            3.第三曲 ，此时就开始安装python3软件
                make install 


​            
        5.进入python36安装目录 ，检查bin目录


​        
        6.配置python3的环境变量  PATH 
            1.取出当前的path变量
            echo $PATH
            2.填写python3的bin目录到PATH中
            PATH="/opt/python36/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"
            3.讲环境变量，写入到全局配置文件/etc/profile 
            vim  /etc/profile 
            
            4.写入如下代码，在最底行写入如下配置
            PATH="/opt/python36/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"
            5.读取配置文件，加载变量生效
            source  /etc/profile  
            
        7.安装django，运行django
            1.安装django模块
            pip3 install -i  https://pypi.douban.com/simple django  
        
            2.升级pip
            pip3 install --upgrade pip
            
            3.通过命令创建django项目
            
            django-admin startproject crm
            django-admin startapp app01
            
            4.关闭防火墙
            iptables -F 

 


查看系统发行版本

cat /etc/redhat-release

cat /etc/os-release

看系统用户的id信息
id  root  


存放用户信息的文件 /etc/passwd 
存放用户组的文件  /etc/group

用户切换

su - 用户名

 

userdel删除用户

userdel -rf  用户名

vim /etc/sudoers #编辑文本给普通用户加权限

sudo命令，预设以root身份去执行命令 
    1.配置 /etc/sudoers，添加banzhuren用户到配置中
    找到如下行
    ## Allow root to run any commands anywhere 
    root    ALL=(ALL)       ALL
    banzhuren       ALL=(ALL)       ALL

    2.保存退出后，banzhuren用户即可使用sudo命令了
    
    3.visodu命令，提供对/etc/sudoers文件，进行配置检测的功能

 权限查看

    -  横线代表是普通文本
    d 代表文件夹
    l 代表软连接

r     read 可读
w     write 可写 
x   可执行  
-    没有权限

linux有三个用户身份
可读可写可执行 是有顺序的   依次是可读可写可执行 
user  属主          
group 属组          
others  其他人

 

加权限

chmod o+w  fildname #给其他用户加写权限

chmod 777 fildname #给所以用户加读写执行

 

更改文件属主

     chown  用户名   文件 

更改文件属组

     chgrp  用户组名  文件

linux的软连接配置

ln -s  目标文件绝对路径 软连接绝对路径

ln -s  /tmp/file1.txt  /opt/file2.txt 

#打包，解包命令   
#将/tmp下所有内容打包成一个文件  xx.tar 


#打包文件  ，不节省磁盘
tar -cvf   打包文件的名字.tar     要打包的内容

#压缩且打包文件 
tar -zcvf  压缩文件名.tar.gz     要压缩的内容 

#解压缩 
tar  -xf  解包文件名.tar  

tar -zxvf    压缩文件名.tar.gz 

 

#查看进程
ps  -ef   |grep  python  #检查python进程是否存活


#查看端口 
netstat -tunlp  |  grep  8000


netstat -tunlp |grep  

#杀死计算机的进程 
1.通过ps -ef | grep  找到进程的pid号

2.通过kill  pid   杀死进程 

#批量杀死进程 

pkill   你想杀死的任务名字

pkill  python  

#linux防火墙功能 

1.linux有俩防火墙，可能会影响咱们的 web服务部署
一个是selinux
二个是iptables  
三个，云服务器对外提供防火墙服务的 硬件防火墙  

2.关闭防火墙的步骤
iptables -F  #清空防火墙规则
systemctl  stop  firewalld  #关闭防火墙服务
systemctl  disable  firewalld  #禁止防火墙开机自启 

3.关闭selinux 步骤如下
1.打开文件vim /etc/selinux/config 

2.临时修改selinux，临时关闭
getenforce  #获取selinux状态
setenforce 0    #临时关闭 

想要永久关闭seliunux，执行3,4步骤
3.修改代码如下
SELINUX=disabled
4.重启机器后，生效

linux的定时任务
   crontab服务

vim /etc/crontab   #打开文件，查看语法规则

编辑定时任务的文件
crontab -e  

#每分钟写入一个信息到文件中
* * *  * *   /usr/bin/echo "老弟啊，学习很累啊" >> /tmp/xd.txt

#每分钟执行一次命令
分 时  日 月  周
*  *   *   *   *   命令绝对路径
*  *  *  *  *  执行命令 


#每小时的3,15分组执行命令
分 时  日 月  周
*      *   *   *   *   命令绝对路径
3,15  *  *  *  *   执行

提前下载好wget工具
yum install wget -y  

​    

2.下载lrzsz工具，方便linux和windows互相传文件
yum install lrzsz -y

3.如何手动启动网卡？
ifup eth33

4.linux的超级用户是什么？如何查看用户身份信息？
root管理员
id root

5.简单描述linux的用户管理
用户
用户组
其他用户

6.如何创建普通用户，并且修改用户密码，然后使用普通用户登录
useradd alex
passwd alex

ssh alex@服务器

7.在linux下如何切换用户
su - alex

8.如何使用root身份执行普通用户的命令?请详细说明配置步骤
visudo
   配置 /etc/sudoers  添加alex用户到配置中
    ## Allow root to run any commands anywhere 
    root    ALL=(ALL)       ALL
    alex       ALL=(ALL)       ALL

   保存退出后，alex用户即可使用sudo命令了


9.简述linux文件的权限有哪些？
r:读 
w:写
x:执行

10.linux文件权限的755，700是什么意思？
                     755        700                    
user 属主       rwx         rwx    
group 属组     r-x         ---
other 其他人   r-x         ---

11.如何修改test.py文件权限为700
chmod 700 test.py

12.如何修改test.py属组是oldboy?
chgrp oldboy  test.py

13.已知test.py文件权限是rwxr--r--，如何修改权限为rw-rw-rw
chmod 666 test.py

14.linux如何建立软连接？
ln -s 目标文件地址    快捷地址

15.linux的PS1变量是什么？如何修改
linux的命令提示符
查找echo PS1  修改PS1="[\u@\h \w \t]\$"   1.vim vi 是什么？ 文本编辑器  2.vim有哪几种模式？ 命令模式 插入模式 底行模式  3.命令模式有哪些？ :wq  vim 4.输入模式有哪些？ i a o ESC 5.底线命令模式有哪些？ : ENTER 6.centos7用什么命令管理服务 systemctl restart nginx  service nginx stop #centos7以下  7.linux解析dns的命令是什么？ nslookup     1.将/tmp/下所有内容压缩成All_log.tar.gz并且放到/home/下 tar -zcvf    /tmp/All_log.tar.gz   /tmp/*  2.解压缩Python源码包Python-3.7.0b3.tgz tar -zxvf Python-3.7.0b3.tgz  3.查看mysql端口状态 netstat -tunlp |grep mysql  4.如何查看nginx的进程 ps -ef |grep nginx  5.如何杀死nginx进程 pkill nginx  6.如何修改linux中文 vim /etc/locale.conf  7.如何统计/var/log大小 du -sh /var/log  8.tree是什么作用? 以树形结构展示给用户  9.如何给linux添加一个dns服务器记录 /etc/hosts 强制写入本地 需要测试的域名 和ip的解析 vim /etc/resolv.conf  nameserver 114.114.114.114  crontab -e 10.每月的,5,15,25天的晚上5点50重启nginx 50 17 5,15,25 * *   /opt/nginx/sbin/nginx  -s reload  11.每周3到周5的深夜11点，备份/var/log /vmtp/ * 23 * * 3-5   /var/log/cp     /var/log/   /mtp/  12.每天早上6.30清空/tmp/内容 30 6 * * * /usr/bin/rm -rf /tmp/*  13.每个星期三的下午6点到8点的第5，15分钟执行命令 command 5,15 18-20 * * 3  command  14.编译安装软件有哪些步骤？      1.下载源代码      2.解压源代码      3进入源代码目录,开始编译安装      4.配置环境变量  15.如何修改python3的环境变量，以及软连接 echoPATH
PATH="/opt/python36/bin/:$PATH"
vim /etc/profile
ln -s /opt/python36/bin/python3.6   /usr/bin/python3

16.请在linux上启动django，windows上进行访问

18.如何查看linux端口？
netstat -tunlp

19.如何杀死进程号为5888？
kill 5888

20。如何关闭iptables？
iptables -F

14.请说出 755, 740分别是什么权限?
rwxr-xr-x
rwxr-----

15.修改文件权限为只有属主可读可写可执行？
chmod  070 xx.txt


系统信息 
arch 显示机器的处理器架构(1) 
uname -m 显示机器的处理器架构(2) 
uname -r 显示正在使用的内核版本 
dmidecode -q 显示硬件系统部件 - (SMBIOS / DMI) 
hdparm -i /dev/hda 罗列一个磁盘的架构特性 
hdparm -tT /dev/sda 在磁盘上执行测试性读取操作 
cat /proc/cpuinfo 显示CPU info的信息 
cat /proc/interrupts 显示中断 
cat /proc/meminfo 校验内存使用 
cat /proc/swaps 显示哪些swap被使用 
cat /proc/version 显示内核的版本 
cat /proc/net/dev 显示网络适配器及统计 
cat /proc/mounts 显示已加载的文件系统 
lspci -tv 罗列 PCI 设备 
lsusb -tv 显示 USB 设备 
date 显示系统日期 
cal 2007 显示2007年的日历表 
date 041217002007.00 设置日期和时间 - 月日时分年.秒 
clock -w 将时间修改保存到 BIOS 



关机 (系统的关机、重启以及登出 ) 
shutdown -h now 关闭系统(1) 
init 0 关闭系统(2) 
telinit 0 关闭系统(3) 
shutdown -h hours:minutes & 按预定时间关闭系统 
shutdown -c 取消按预定时间关闭系统 
shutdown -r now 重启(1) 
reboot 重启(2) 
logout 注销 



文件和目录 
cd /home 进入 '/ home' 目录' 
cd .. 返回上一级目录 
cd ../.. 返回上两级目录 
cd 进入个人的主目录 
cd ~user1 进入个人的主目录 
cd - 返回上次所在的目录 
pwd 显示工作路径 
ls 查看目录中的文件 
ls -F 查看目录中的文件 
ls -l 显示文件和目录的详细资料 
ls -a 显示隐藏文件 
ls *[0-9]* 显示包含数字的文件名和目录名 
tree 显示文件和目录由根目录开始的树形结构(1) 
lstree 显示文件和目录由根目录开始的树形结构(2) 
mkdir dir1 创建一个叫做 'dir1' 的目录' 
mkdir dir1 dir2 同时创建两个目录 
mkdir -p /tmp/dir1/dir2 创建一个目录树 
rm -f file1 删除一个叫做 'file1' 的文件' 
rmdir dir1 删除一个叫做 'dir1' 的目录' 
rm -rf dir1 删除一个叫做 'dir1' 的目录并同时删除其内容 
rm -rf dir1 dir2 同时删除两个目录及它们的内容 
mv dir1 new_dir 重命名/移动 一个目录 
cp file1 file2 复制一个文件 
cp dir/* . 复制一个目录下的所有文件到当前工作目录 
cp -a /tmp/dir1 . 复制一个目录到当前工作目录 
cp -a dir1 dir2 复制一个目录 
ln -s file1 lnk1 创建一个指向文件或目录的软链接 
ln file1 lnk1 创建一个指向文件或目录的物理链接 
touch -t 0712250000 file1 修改一个文件或目录的时间戳 - (YYMMDDhhmm) 
file file1 outputs the mime type of the file as text 
iconv -l 列出已知的编码 
iconv -f fromEncoding -t toEncoding inputFile > outputFile creates a new from the given input file by assuming it is encoded in fromEncoding and converting it to toEncoding. 
find . -maxdepth 1 -name *.jpg -print -exec convert "{}" -resize 80x60 "thumbs/{}" \; batch resize files in the current directory and send them to a thumbnails directory (requires convert from Imagemagick) 



文件搜索 
find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
find / -user user1 搜索属于用户 'user1' 的文件和目录 
find /home/user1 -name \*.bin 在目录 '/ home/user1' 中搜索带有'.bin' 结尾的文件 
find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件 
find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件 
find / -name \*.rpm -exec chmod 755 '{}' \; 搜索以 '.rpm' 结尾的文件并定义其权限 
find / -xdev -name \*.rpm 搜索以 '.rpm' 结尾的文件，忽略光驱、捷盘等可移动设备 
locate \*.ps 寻找以 '.ps' 结尾的文件 - 先运行 'updatedb' 命令 
whereis halt 显示一个二进制文件、源码或man的位置 
which halt 显示一个二进制文件或可执行文件的完整路径 



挂载一个文件系统 
mount /dev/hda2 /mnt/hda2 挂载一个叫做hda2的盘 - 确定目录 '/ mnt/hda2' 已经存在 
umount /dev/hda2 卸载一个叫做hda2的盘 - 先从挂载点 '/ mnt/hda2' 退出 
fuser -km /mnt/hda2 当设备繁忙时强制卸载 
umount -n /mnt/hda2 运行卸载操作而不写入 /etc/mtab 文件- 当文件为只读或当磁盘写满时非常有用 
mount /dev/fd0 /mnt/floppy 挂载一个软盘 
mount /dev/cdrom /mnt/cdrom 挂载一个cdrom或dvdrom 
mount /dev/hdc /mnt/cdrecorder 挂载一个cdrw或dvdrom 
mount /dev/hdb /mnt/cdrecorder 挂载一个cdrw或dvdrom 
mount -o loop file.iso /mnt/cdrom 挂载一个文件或ISO镜像文件 
mount -t vfat /dev/hda5 /mnt/hda5 挂载一个Windows FAT32文件系统 
mount /dev/sda1 /mnt/usbdisk 挂载一个usb 捷盘或闪存设备 
mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share 挂载一个windows网络共享 



磁盘空间 
df -h 显示已经挂载的分区列表 
ls -lSr |more 以尺寸大小排列文件和目录 
du -sh dir1 估算目录 'dir1' 已经使用的磁盘空间' 
du -sk * | sort -rn 以容量大小为依据依次显示文件和目录的大小 
rpm -q -a --qf '%10{SIZE}t%{NAME}n' | sort -k1,1n 以大小为依据依次显示已安装的rpm包所使用的空间 (fedora, redhat类系统) 
dpkg-query -W -f='${Installed-Size;10}t${Package}n' | sort -k1,1n 以大小为依据显示已安装的deb包所使用的空间 (ubuntu, debian类系统) 



用户和群组 
groupadd group_name 创建一个新用户组 
groupdel group_name 删除一个用户组 
groupmod -n new_group_name old_group_name 重命名一个用户组 
useradd -c "Name Surname " -g admin -d /home/user1 -s /bin/bash user1 创建一个属于 "admin" 用户组的用户 
useradd user1 创建一个新用户 
userdel -r user1 删除一个用户 ( '-r' 排除主目录) 
usermod -c "User FTP" -g system -d /ftp/user1 -s /bin/nologin user1 修改用户属性 
passwd 修改口令 
passwd user1 修改一个用户的口令 (只允许root执行) 
chage -E 2005-12-31 user1 设置用户口令的失效期限 
pwck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的用户 
grpck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的群组 
newgrp group_name 登陆进一个新的群组以改变新创建文件的预设群组 



文件的权限 - 使用 "+" 设置权限，使用 "-" 用于取消 
ls -lh 显示权限 
ls /tmp | pr -T5 -W$COLUMNS 将终端划分成5栏显示 
chmod ugo+rwx directory1 设置目录的所有人(u)、群组(g)以及其他人(o)以读（r ）、写(w)和执行(x)的权限 
chmod go-rwx directory1 删除群组(g)与其他人(o)对目录的读写执行权限 
chown user1 file1 改变一个文件的所有人属性 
chown -R user1 directory1 改变一个目录的所有人属性并同时改变改目录下所有文件的属性 
chgrp group1 file1 改变文件的群组 
chown user1:group1 file1 改变一个文件的所有人和群组属性 
find / -perm -u+s 罗列一个系统中所有使用了SUID控制的文件 
chmod u+s /bin/file1 设置一个二进制文件的 SUID 位 - 运行该文件的用户也被赋予和所有者同样的权限 
chmod u-s /bin/file1 禁用一个二进制文件的 SUID位 
chmod g+s /home/public 设置一个目录的SGID 位 - 类似SUID ，不过这是针对目录的 
chmod g-s /home/public 禁用一个目录的 SGID 位 
chmod o+t /home/public 设置一个文件的 STIKY 位 - 只允许合法所有人删除文件 
chmod o-t /home/public 禁用一个目录的 STIKY 位 



文件的特殊属性 - 使用 "+" 设置权限，使用 "-" 用于取消 
chattr +a file1 只允许以追加方式读写文件 
chattr +c file1 允许这个文件能被内核自动压缩/解压 
chattr +d file1 在进行文件系统备份时，dump程序将忽略这个文件 
chattr +i file1 设置成不可变的文件，不能被删除、修改、重命名或者链接 
chattr +s file1 允许一个文件被安全地删除 
chattr +S file1 一旦应用程序对这个文件执行了写操作，使系统立刻把修改的结果写到磁盘 
chattr +u file1 若文件被删除，系统会允许你在以后恢复这个被删除的文件 
lsattr 显示特殊的属性 



打包和压缩文件 
bunzip2 file1.bz2 解压一个叫做 'file1.bz2'的文件 
bzip2 file1 压缩一个叫做 'file1' 的文件 
gunzip file1.gz 解压一个叫做 'file1.gz'的文件 
gzip file1 压缩一个叫做 'file1'的文件 
gzip -9 file1 最大程度压缩 
rar a file1.rar test_file 创建一个叫做 'file1.rar' 的包 
rar a file1.rar file1 file2 dir1 同时压缩 'file1', 'file2' 以及目录 'dir1' 
rar x file1.rar 解压rar包 
unrar x file1.rar 解压rar包 
tar -cvf archive.tar file1 创建一个非压缩的 tarball 
tar -cvf archive.tar file1 file2 dir1 创建一个包含了 'file1', 'file2' 以及 'dir1'的档案文件 
tar -tf archive.tar 显示一个包中的内容 
tar -xvf archive.tar 释放一个包 
tar -xvf archive.tar -C /tmp 将压缩包释放到 /tmp目录下 
tar -cvfj archive.tar.bz2 dir1 创建一个bzip2格式的压缩包 
tar -jxvf archive.tar.bz2 解压一个bzip2格式的压缩包 
tar -cvfz archive.tar.gz dir1 创建一个gzip格式的压缩包 
tar -zxvf archive.tar.gz 解压一个gzip格式的压缩包 
zip file1.zip file1 创建一个zip格式的压缩包 
zip -r file1.zip file1 file2 dir1 将几个文件和目录同时压缩成一个zip格式的压缩包 
unzip file1.zip 解压一个zip格式压缩包 



RPM 包 - （Fedora, Redhat及类似系统） 
rpm -ivh package.rpm 安装一个rpm包 
rpm -ivh --nodeeps package.rpm 安装一个rpm包而忽略依赖关系警告 
rpm -U package.rpm 更新一个rpm包但不改变其配置文件 
rpm -F package.rpm 更新一个确定已经安装的rpm包 
rpm -e package_name.rpm 删除一个rpm包 
rpm -qa 显示系统中所有已经安装的rpm包 
rpm -qa | grep httpd 显示所有名称中包含 "httpd" 字样的rpm包 
rpm -qi package_name 获取一个已安装包的特殊信息 
rpm -qg "System Environment/Daemons" 显示一个组件的rpm包 
rpm -ql package_name 显示一个已经安装的rpm包提供的文件列表 
rpm -qc package_name 显示一个已经安装的rpm包提供的配置文件列表 
rpm -q package_name --whatrequires 显示与一个rpm包存在依赖关系的列表 
rpm -q package_name --whatprovides 显示一个rpm包所占的体积 
rpm -q package_name --scripts 显示在安装/删除期间所执行的脚本l 
rpm -q package_name --changelog 显示一个rpm包的修改历史 
rpm -qf /etc/httpd/conf/httpd.conf 确认所给的文件由哪个rpm包所提供 
rpm -qp package.rpm -l 显示由一个尚未安装的rpm包提供的文件列表 
rpm --import /media/cdrom/RPM-GPG-KEY 导入公钥数字证书 
rpm --checksig package.rpm 确认一个rpm包的完整性 
rpm -qa gpg-pubkey 确认已安装的所有rpm包的完整性 
rpm -V package_name 检查文件尺寸、 许可、类型、所有者、群组、MD5检查以及最后修改时间 
rpm -Va 检查系统中所有已安装的rpm包- 小心使用 
rpm -Vp package.rpm 确认一个rpm包还未安装 
rpm2cpio package.rpm | cpio --extract --make-directories *bin* 从一个rpm包运行可执行文件 
rpm -ivh /usr/src/redhat/RPMS/`arch`/package.rpm 从一个rpm源码安装一个构建好的包 
rpmbuild --rebuild package_name.src.rpm 从一个rpm源码构建一个 rpm 包 



YUM 软件包升级器 - （Fedora, RedHat及类似系统） 
yum install package_name 下载并安装一个rpm包 
yum localinstall package_name.rpm 将安装一个rpm包，使用你自己的软件仓库为你解决所有依赖关系 
yum update package_name.rpm 更新当前系统中所有安装的rpm包 
yum update package_name 更新一个rpm包 
yum remove package_name 删除一个rpm包 
yum list 列出当前系统中安装的所有包 
yum search package_name 在rpm仓库中搜寻软件包 
yum clean packages 清理rpm缓存删除下载的包 
yum clean headers 删除所有头文件 
yum clean all 删除所有缓存的包和头文件 



DEB 包 (Debian, Ubuntu 以及类似系统) 
dpkg -i package.deb 安装/更新一个 deb 包 
dpkg -r package_name 从系统删除一个 deb 包 
dpkg -l 显示系统中所有已经安装的 deb 包 
dpkg -l | grep httpd 显示所有名称中包含 "httpd" 字样的deb包 
dpkg -s package_name 获得已经安装在系统中一个特殊包的信息 
dpkg -L package_name 显示系统中已经安装的一个deb包所提供的文件列表 
dpkg --contents package.deb 显示尚未安装的一个包所提供的文件列表 
dpkg -S /bin/ping 确认所给的文件由哪个deb包提供 



APT 软件工具 (Debian, Ubuntu 以及类似系统) 
apt-get install package_name 安装/更新一个 deb 包 
apt-cdrom install package_name 从光盘安装/更新一个 deb 包 
apt-get update 升级列表中的软件包 
apt-get upgrade 升级所有已安装的软件 
apt-get remove package_name 从系统删除一个deb包 
apt-get check 确认依赖的软件仓库正确 
apt-get clean 从下载的软件包中清理缓存 
apt-cache search searched-package 返回包含所要搜索字符串的软件包名称 



查看文件内容 
cat file1 从第一个字节开始正向查看文件的内容 
tac file1 从最后一行开始反向查看一个文件的内容 
more file1 查看一个长文件的内容 
less file1 类似于 'more' 命令，但是它允许在文件中和正向操作一样的反向操作 
head -2 file1 查看一个文件的前两行 
tail -2 file1 查看一个文件的最后两行 
tail -f /var/log/messages 实时查看被添加到一个文件中的内容 



文本处理 
cat file1 file2 ... | command <> file1_in.txt_or_file1_out.txt general syntax for text manipulation using PIPE, STDIN and STDOUT 
cat file1 | command( sed, grep, awk, grep, etc...) > result.txt 合并一个文件的详细说明文本，并将简介写入一个新文件中 
cat file1 | command( sed, grep, awk, grep, etc...) >> result.txt 合并一个文件的详细说明文本，并将简介写入一个已有的文件中 
grep Aug /var/log/messages 在文件 '/var/log/messages'中查找关键词"Aug" 
grep ^Aug /var/log/messages 在文件 '/var/log/messages'中查找以"Aug"开始的词汇 
grep [0-9] /var/log/messages 选择 '/var/log/messages' 文件中所有包含数字的行 
grep Aug -R /var/log/* 在目录 '/var/log' 及随后的目录中搜索字符串"Aug" 
sed 's/stringa1/stringa2/g' example.txt 将example.txt文件中的 "string1" 替换成 "string2" 
sed '/^$/d' example.txt 从example.txt文件中删除所有空白行 
sed '/ *#/d; /^$/d' example.txt 从example.txt文件中删除所有注释和空白行 
echo 'esempio' | tr '[:lower:]' '[:upper:]' 合并上下单元格内容 
sed -e '1d' result.txt 从文件example.txt 中排除第一行 
sed -n '/stringa1/p' 查看只包含词汇 "string1"的行 
sed -e 's/ *$//' example.txt 删除每一行最后的空白字符 
sed -e 's/stringa1//g' example.txt 从文档中只删除词汇 "string1" 并保留剩余全部 
sed -n '1,5p;5q' example.txt 查看从第一行到第5行内容 
sed -n '5p;5q' example.txt 查看第5行 
sed -e 's/00*/0/g' example.txt 用单个零替换多个零 
cat -n file1 标示文件的行数 
cat example.txt | awk 'NR%2==1' 删除example.txt文件中的所有偶数行 
echo a b c | awk '{print $1}' 查看一行第一栏 
echo a b c | awk '{print $1,$3}' 查看一行的第一和第三栏 
paste file1 file2 合并两个文件或两栏的内容 
paste -d '+' file1 file2 合并两个文件或两栏的内容，中间用"+"区分 
sort file1 file2 排序两个文件的内容 
sort file1 file2 | uniq 取出两个文件的并集(重复的行只保留一份) 
sort file1 file2 | uniq -u 删除交集，留下其他的行 
sort file1 file2 | uniq -d 取出两个文件的交集(只留下同时存在于两个文件中的文件) 
comm -1 file1 file2 比较两个文件的内容只删除 'file1' 所包含的内容 
comm -2 file1 file2 比较两个文件的内容只删除 'file2' 所包含的内容 
comm -3 file1 file2 比较两个文件的内容只删除两个文件共有的部分 




字符设置和文件格式转换 
dos2unix filedos.txt fileunix.txt 将一个文本文件的格式从MSDOS转换成UNIX 
unix2dos fileunix.txt filedos.txt 将一个文本文件的格式从UNIX转换成MSDOS 
recode ..HTML < page.txt > page.html 将一个文本文件转换成html 
recode -l | more 显示所有允许的转换格式 



文件系统分析 
badblocks -v /dev/hda1 检查磁盘hda1上的坏磁块 
fsck /dev/hda1 修复/检查hda1磁盘上linux文件系统的完整性 
fsck.ext2 /dev/hda1 修复/检查hda1磁盘上ext2文件系统的完整性 
e2fsck /dev/hda1 修复/检查hda1磁盘上ext2文件系统的完整性 
e2fsck -j /dev/hda1 修复/检查hda1磁盘上ext3文件系统的完整性 
fsck.ext3 /dev/hda1 修复/检查hda1磁盘上ext3文件系统的完整性 
fsck.vfat /dev/hda1 修复/检查hda1磁盘上fat文件系统的完整性 
fsck.msdos /dev/hda1 修复/检查hda1磁盘上dos文件系统的完整性 
dosfsck /dev/hda1 修复/检查hda1磁盘上dos文件系统的完整性 



初始化一个文件系统 
mkfs /dev/hda1 在hda1分区创建一个文件系统 
mke2fs /dev/hda1 在hda1分区创建一个linux ext2的文件系统 
mke2fs -j /dev/hda1 在hda1分区创建一个linux ext3(日志型)的文件系统 
mkfs -t vfat 32 -F /dev/hda1 创建一个 FAT32 文件系统 
fdformat -n /dev/fd0 格式化一个软盘 
mkswap /dev/hda3 创建一个swap文件系统 



SWAP文件系统 
mkswap /dev/hda3 创建一个swap文件系统 
swapon /dev/hda3 启用一个新的swap文件系统 
swapon /dev/hda2 /dev/hdb3 启用两个swap分区 



备份 
dump -0aj -f /tmp/home0.bak /home 制作一个 '/home' 目录的完整备份 
dump -1aj -f /tmp/home0.bak /home 制作一个 '/home' 目录的交互式备份 
restore -if /tmp/home0.bak 还原一个交互式备份 
rsync -rogpav --delete /home /tmp 同步两边的目录 
rsync -rogpav -e ssh --delete /home ip_address:/tmp 通过SSH通道rsync 
rsync -az -e ssh --delete ip_addr:/home/public /home/local 通过ssh和压缩将一个远程目录同步到本地目录 
rsync -az -e ssh --delete /home/local ip_addr:/home/public 通过ssh和压缩将本地目录同步到远程目录 
dd bs=1M if=/dev/hda | gzip | ssh user@ip_addr 'dd of=hda.gz' 通过ssh在远程主机上执行一次备份本地磁盘的操作 
dd if=/dev/sda of=/tmp/file1 备份磁盘内容到一个文件 
tar -Puf backup.tar /home/user 执行一次对 '/home/user' 目录的交互式备份操作 
( cd /tmp/local/ && tar c . ) | ssh -C user@ip_addr 'cd /home/share/ && tar x -p' 通过ssh在远程目录中复制一个目录内容 
( tar c /home ) | ssh -C user@ip_addr 'cd /home/backup-home && tar x -p' 通过ssh在远程目录中复制一个本地目录 
tar cf - . | (cd /tmp/backup ; tar xf - ) 本地将一个目录复制到另一个地方，保留原有权限及链接 
find /home/user1 -name '*.txt' | xargs cp -av --target-directory=/home/backup/ --parents 从一个目录查找并复制所有以 '.txt' 结尾的文件到另一个目录 
find /var/log -name '*.log' | tar cv --files-from=- | bzip2 > log.tar.bz2 查找所有以 '.log' 结尾的文件并做成一个bzip包 
dd if=/dev/hda of=/dev/fd0 bs=512 count=1 做一个将 MBR (Master Boot Record)内容复制到软盘的动作 
dd if=/dev/fd0 of=/dev/hda bs=512 count=1 从已经保存到软盘的备份中恢复MBR内容 



光盘 
cdrecord -v gracetime=2 dev=/dev/cdrom -eject blank=fast -force 清空一个可复写的光盘内容 
mkisofs /dev/cdrom > cd.iso 在磁盘上创建一个光盘的iso镜像文件 
mkisofs /dev/cdrom | gzip > cd_iso.gz 在磁盘上创建一个压缩了的光盘iso镜像文件 
mkisofs -J -allow-leading-dots -R -V "Label CD" -iso-level 4 -o ./cd.iso data_cd 创建一个目录的iso镜像文件 
cdrecord -v dev=/dev/cdrom cd.iso 刻录一个ISO镜像文件 
gzip -dc cd_iso.gz | cdrecord dev=/dev/cdrom - 刻录一个压缩了的ISO镜像文件 
mount -o loop cd.iso /mnt/iso 挂载一个ISO镜像文件 
cd-paranoia -B 从一个CD光盘转录音轨到 wav 文件中 
cd-paranoia -- "-3" 从一个CD光盘转录音轨到 wav 文件中（参数-3） 
cdrecord --scanbus 扫描总线以识别scsi通道 
dd if=/dev/hdc | md5sum 校验一个设备的md5sum编码，例如一张 CD 



网络 - （以太网和WIFI无线） 
ifconfig eth0 显示一个以太网卡的配置 
ifup eth0 启用一个 'eth0' 网络设备 
ifdown eth0 禁用一个 'eth0' 网络设备 
ifconfig eth0 192.168.1.1 netmask 255.255.255.0 控制IP地址 
ifconfig eth0 promisc 设置 'eth0' 成混杂模式以嗅探数据包 (sniffing) 
dhclient eth0 以dhcp模式启用 'eth0' 
route -n show routing table 
route add -net 0/0 gw IP_Gateway configura default gateway 
route add -net 192.168.0.0 netmask 255.255.0.0 gw 192.168.1.1 configure static route to reach network '192.168.0.0/16' 
route del 0/0 gw IP_gateway remove static route 
echo "1" > /proc/sys/net/ipv4/ip_forward activate ip routing 
hostname show hostname of system 
host www.example.com lookup hostname to resolve name to ip address and viceversa(1) 
nslookup www.example.com lookup hostname to resolve name to ip address and viceversa(2) 
ip link show show link status of all interfaces 
mii-tool eth0 show link status of 'eth0' 
ethtool eth0 show statistics of network card 'eth0' 
netstat -tup show all active network connections and their PID 
netstat -tupl show all network services listening on the system and their PID 
tcpdump tcp port 80 show all HTTP traffic 
iwlist scan show wireless networks 
iwconfig eth1 show configuration of a wireless network card 
hostname show hostname 
host www.example.com lookup hostname to resolve name to ip address and viceversa 
nslookup www.example.com lookup hostname to resolve name to ip address and viceversa 
whois www.example.com lookup on Whois database 

 

JPS工具

jps(Java Virtual Machine Process Status Tool)是JDK 1.5提供的一个显示当前所有java进程pid的命令，简单实用，非常适合在linux/unix平台上简单察看当前java进程的一些简单情况。

    我想很多人都是用过unix系统里的ps命令，这个命令主要是用来显示当前系统的进程情况，有哪些进程，及其 id。 jps 也是一样，它的作用是显示当前系统的java进程情况，及其id号。我们可以通过它来查看我们到底启动了几个java进程（因为每一个java程序都会独占一个java虚拟机实例），和他们的进程号（为下面几个程序做准备），并可通过opt来查看这些进程的详细启动参数。
    
     使用方法：在当前命令行下打 jps(需要JAVA_HOME，没有的话，到改程序的目录下打) 。

jps存放在JAVA_HOME/bin/jps，使用时为了方便请将JAVA_HOME/bin/加入到Path.

$> jps
23991 Jps
23789 BossMain
23651 Resin

 


比较常用的参数：

-q 只显示pid，不显示class名称,jar文件名和传递给main 方法的参数
$>  jps -q
28680
23789
23651

-m 输出传递给main 方法的参数，在嵌入式jvm上可能是null

$> jps -m
28715 Jps -m
23789 BossMain
23651 Resin -socketwait 32768 -stdout /data/aoxj/resin/log/stdout.log -stderr /data/aoxj/resin/log/stderr.log

-l 输出应用程序main class的完整package名 或者 应用程序的jar文件完整路径名

$> jps -l
28729 sun.tools.jps.Jps
23789 com.asiainfo.aimc.bossbi.BossMain
23651 com.caucho.server.resin.Resin

-v 输出传递给JVM的参数

$> jps -v
23789 BossMain
28802 Jps -Denv.class.path=/data/aoxj/bossbi/twsecurity/java/trustwork140.jar:/data/aoxj/bossbi/twsecurity/java/:/data/aoxj/bossbi/twsecurity/java/twcmcc.jar:/data/aoxj/jdk15/lib/rt.jar:/data/aoxj/jd

k15/lib/tools.jar -Dapplication.home=/data/aoxj/jdk15 -Xms8m
23651 Resin -Xss1m -Dresin.home=/data/aoxj/resin -Dserver.root=/data/aoxj/resin -Djava.util.logging.manager=com.caucho.log.LogManagerImpl -

Djavax.management.builder.initial=com.caucho.jmx.MBeanServerBuilderImpl

sudo jps看到的进程数量最全

jps 192.168.0.77

列出远程服务器192.168.0.77机器所有的jvm实例，采用rmi协议，默认连接端口为1099

（前提是远程服务器提供jstatd服务）

注：jps命令有个地方很不好，似乎只能显示当前用户的java进程，要显示其他用户的还是只能用unix/linux的ps命令。


Vi命令:如何删除全部内容？


在命令模式下，输入:.,$d 一回车就全没了。

表示从当前行到末行全部删除掉。

用gg表示移动到首行。

超哥方法：

g 进入文档开头 G文档结尾  
按下dG 删除当前光标到行尾的数据