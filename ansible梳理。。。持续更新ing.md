---
title: ansible梳理。。。持续更新ing
date: 2019-11-15 22:41:52
tags:
---

## 1.1 ansible基本架构

ansible系统由控制主机和被管理主机组成,控制主机不支持windows平台

<!--more-->

 

![img](https://upload-images.jianshu.io/upload_images/6078939-1799907d732a3e87.png)

ansible基础架构

- 核心: ansible
- Core Modules: ansible自带的模块
- Custom Modules: 核心模块功能不足时,用户可以添加扩展模块
- Plugins: 通过插件来实现记录日志,发送邮件或其他功能
- Playbooks: 剧本,YAML格式文件，多个任务定义在一个文件中，定义主机需要调用哪些模块来完成的功能
- Connectior Plugins: ansible基于连接插件连接到各个主机上,默认是使用ssh
- Host Inventory: 记录由Ansible管理的主机信息，包括端口、密码、ip等

## 1.2 ansible特点

部署简单, 只需要在控制主机上部署ansible环境,被控制端上只要求安装ssh和python 2.5以上版本,这个对于类unix系统来说相当与无需配置.

1. no angents: 被管控节点无需安装agent
2. no server: 无服务端,使用是直接调用命名
3. modules in any languages: 基于模块工作, 可以使用任意语言开发模块
4. 易读的语法: 基于yaml语法编写playbook
5. 基于推送模式: 不同于puppet的拉取模式,直接由调用者控制变更在服务器上发生的时间
6. 模块是幂等性的:定义的任务已存在则不会做任何事情,意味着在同一台服务器上多次执行同一个playbook是安全的

## 1.3 ansible程序目录结构:

- 配置文件: `/etc/ansible/`
- 执行文件目录: `/usr/bin/`
- lib依赖库: `/usr/lib/python2.7/site-packages/ansible/`
- help文件: `/usr/lib/python2.7/site-packages/ansible`

# 2. ansible任务执行

## 2.1 ansible任务执行模式

Ansible任务执行模式分为以下两种:

- ad-hoc模式(点对点模块)
  使用单个模块,支持批量执行单条命令,相当与在bash中执行一句shell命令
- playbook模式(剧本模式)
  ansible主要的管理方式,通过多个task的集合完成一类功能,可以理解为多个ad-hoc的配置文件

## 2.2 ansible执行流程:

 

![img](https://upload-images.jianshu.io/upload_images/6078939-2d6e5a817186480d.png)

image.png

# 3. ansible使用实例

## 3.1 安装

1. 编译安装
   ansible依赖于Python 2.6或更高的版本、paramiko、PyYAML及Jinja2。

```
# yum -y install python-jinja2 PyYAML python-paramiko python-babel python-crypto
# tar xf ansible-1.5.4.tar.gz
# cd ansible-1.5.4
# python setup.py build
# python setup.py install
# mkdir /etc/ansible
# cp -r examples/* /etc/ansible
```

1. rpm包安装

```
# yum install ansible
```

## 3.2 修改配置文件

ansible配置文件查找顺序

1. 检查环境变量`ANSIBLE_CONFIG`指向的路径文件(`export ANSIBLE_CONFIG=/etc/ansible.cfg`)；
2. `~/.ansible.cfg`，检查当前目录下的ansible.cfg配置文件；
3. `/etc/ansible.cfg`检查etc目录的配置文件。

ansible配置文件
ansible 有许多参数，下面我们列出一些常见的参数：

```
    inventory = /etc/ansible/hosts      #这个参数表示资源清单inventory文件的位置
    library = /usr/share/ansible        #指向存放Ansible模块的目录，支持多个目录方式，只要用冒号（：）隔开就可以
    forks = 5       #并发连接数，默认为5
    sudo_user = root        #设置默认执行命令的用户
    remote_port = 22        #指定连接被管节点的管理端口，默认为22端口，建议修改，能够更加安全
    host_key_checking = False       #设置是否检查SSH主机的密钥，值为True/False。关闭后第一次连接不会提示配置实例
    timeout = 60        #设置SSH连接的超时时间，单位为秒
    log_path = /var/log/ansible.log     #指定一个存储ansible日志的文件（默认不记录日志）
```

## 3.3 定义Inventory(主机列表)

ansible的主要功用在于批量主机操作，为了便捷地使用其中的部分主机，可以在inventory file中将其分组命名。默认的inventory file为/etc/ansible/hosts。
inventory file可以有多个，且也可以通过Dynamic Inventory来动态生成。

Inventory文件格式:

- inventory文件遵循INI文件风格，中括号中的字符为组名。可以将同一个主机同时归并到多个不同的组中；此外，当如若目标主机使用了非默认的SSH端口，还可以在主机名称之后使用冒号加端口号来标明。

```
ntp.com

[webservers]
www1.com:2222
www2.com

[dbservers]
db1.com
db2.com
db3.com
```

- 如果主机名称遵循相似的命名模式，还可以使用列表的方式标识各主机，例如：

```
[webservers]
www[01:50].example.com

[databases]
db-[a:f].example.com
```

- 主机变量: 可以在inventory中定义主机时为其添加主机变量以便于在playbook中使用。例如：

```
[webservers]
www1.com http_port=80 maxRequestsPerChild=808
www2.com http_port=8080 maxRequestsPerChild=909
```

- 组变量

```
[webservers]
www1.com
www2.com

[webservers:vars]
ntp_server=ntp.com
nfs_server=nfs.com
```

inventory其他的参数

- ansible基于ssh连接inventory中指定的远程主机时，还可以通过参数指定其交互方式；这些参数如下所示：

```
ansible_ssh_host # 远程主机
ansible_ssh_port # 指定远程主机ssh端口
ansible_ssh_user # ssh连接远程主机的用户,默认root
ansible_ssh_pass # 连接远程主机使用的密码,在文件中明文,建议使用--ask-pass或者使用SSH keys
ansible_sudo_pass # sudo密码, 建议使用--ask-sudo-pass
ansible_connection # 指定连接类型: local, ssh, paramiko
ansible_ssh_private_key_file # ssh 连接使用的私钥
ansible_shell_type # 指定连接对端的shell类型, 默认sh,支持csh,fish
ansible_python_interpreter # 指定对端使用的python编译器的路径
```

## 3.4 基于ad-hoc模式运行

ansible通过ssh实现配置管理、应用部署、任务执行等功能，因此，需要事先配置ansible端能基于密钥认证的方式联系各被管理节点。

ansible命令使用语法:

```
ansible <host-pattern> [-f forks] [-m module_name] [-a args]
    -m module：默认为command
```

例如:

1. 定义好inventory后可以调用`ping`模块来检测网络是否可达

```
# ansible all -m ping
192.168.57.22 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
192.168.57.11 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

1. 使用`command`模块远程执行命令:

```
# 如果/etc/passwd文件存在就执行grep命令
# ansible all -m command -a 'removes=/etc/passwd grep root /etc/passwd' 
192.168.57.22 | SUCCESS | rc=0 >>
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

192.168.57.11 | SUCCESS | rc=0 >>
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

可以通过`ansible-doc -l`列出所有可用的module,常用的module有:

```
ping # 主机连通性测试
command # 在远程主机上执行命令,不支持管道
shell # 在远程主机上调用shell解析器,支持管道命令个
copy # 用于将文件复制到远程主机,支持设定内容和修改权限.
file # 创建文件,创建连接文件,删除文件等
fetch # 从远程复制文件到本地
cron # 管理cron计划任务
yum # 用于模块的安装
service # 管理服务
user # 管理用户账号
group # 用户组管理
script # 将本地的脚本在远端服务器运行
setup # 该模块主要用于收集信息，是通过调用facts组件来实现的,以变量形式存储主机上的信息
```

`ansible -s `可以查看指定module的用法,或者参看[官方帮助文档](http://docs.ansible.com/ansible/latest/modules/modules_by_category.html):

```
# ansible-doc -s service
- name: Manage services
  service:
      arguments:             # Additional arguments provided on the command line
      enabled:               # Whether the service should start on boot. *At least one of state and enabled are required.*
      name:                  # (required) Name of the service.
      pattern:               # If the service does not respond to the status command, name a substring to look for as would be
                               found in the output of the `ps' command as a stand-in for a
                               status result.  If the string is found, the service will be
                               assumed to be running.
      runlevel:              # For OpenRC init scripts (ex: Gentoo) only.  The runlevel that this service belongs to.
      sleep:                 # If the service is being `restarted' then sleep this many seconds between the stop and start
                               command. This helps to workaround badly behaving init scripts
                               that exit immediately after signaling a process to stop.
      state:                 # `started'/`stopped' are idempotent actions that will not run commands unless necessary.
                               `restarted' will always bounce the service.  `reloaded' will
                               always reload. *At least one of state and enabled are required.*
                               Note that reloaded will start the service if it is not already
                               started, even if your chosen init system wouldn't normally.
      use:                   # The service module actually uses system specific modules, normally through auto detection, this
                               setting can force a specific module. Normally it uses the value
                               of the 'ansible_service_mgr' fact and falls back to the old
                               'service' module when none matching is found.
```

## 3.5 基于playbook执行

playbook是由一个或多个“play”组成的列表。play的主要功能在于将事先归并为一组的主机装扮成事先通过ansible中的task定义好的角色。从根本上来讲，所谓task无非是调用ansible的一个module。将多个play组织在一个playbook中，即可以让它们联同起来按事先编排的机制同唱一台大戏。

下面是一个简单示例:

```
- hosts: master
  user: root
  vars:
    - motd_warning: 'WARNING: Use by master ONLY'
  tasks:
    - name: setup a MOTD
      copy: dest=/etc/motd content="{{ motd_warning }}"
      notify: say something
  handlers:
    - name: say something
      command: echo "copy OK"
```

playbooks的组成部分

- Target section: 定义要运行playbook的远程主机组
  - hosts: hosts用于指定要执行指定任务的主机，其可以是一个或多个由冒号分隔主机组
  - user: 指定远程主机上的执行任务的用户,还可以指定sudo用户等
- Variable section: 定义playbook运行时使用的变量
- Task section: 定义要在远程主机上运行的任务列表
  - name: 每个任务都有name,建议描述任务执行步骤,未通过name会用执行结果作为name
  - 'module:options': 调用的module和传入的参数args
- Handler section: 定义task完成后需要调用的任务
  - notify: 在Task Section在每个play的最后触发,调用在hendler中定义的操作
  - handler: 也是task的列表

执行过程:

```
[root@localhost ansible]# ansible-playbook set_motd.yaml 

PLAY [master] ******************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [192.168.57.11]

TASK [setup a MOTD] ************************************************************************************************************
changed: [192.168.57.11]

RUNNING HANDLER [say something] ************************************************************************************************
changed: [192.168.57.11]

PLAY RECAP *********************************************************************************************************************
192.168.57.11              : ok=3    changed=2    unreachable=0    failed=0   

# cat /etc/motd 
WARNING: Use by master ONLY
```

playbook安装配置apache实战

1. 编写playbook:`install_httpd.yaml`

```
---
- hosts: slave
  vars:
    http_port: 8080
  user: root
  tasks:
  - name: ensure apache is at the latest version
    yum: name=httpd state=latest
  - name: write the apache config file
    template:
      src: template/httpd.j2
      dest: /etc/httpd/conf/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running
    service: name=httpd state=started
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

1. 生成apache配置文件jinja2模板,将服务端口配置为变量`Listen {{ http_port }}`
2. 执行playbook并检查远端服务端口

```
[root@localhost ansible]# ansible-playbook install_httpd.yaml 

PLAY [slave] *******************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [192.168.57.22]

TASK [ensure apache is at the latest version] **********************************************************************************
changed: [192.168.57.22]

TASK [write the apache config file] ********************************************************************************************
changed: [192.168.57.22]

TASK [ensure apache is running] ************************************************************************************************
changed: [192.168.57.22]

RUNNING HANDLER [restart apache] ***********************************************************************************************
changed: [192.168.57.22]

PLAY RECAP *********************************************************************************************************************
192.168.57.22              : ok=5    changed=4    unreachable=0    failed=0   

[root@localhost ansible]# ansible slave -m shell -a 'ss -lntp | grep 8080'
192.168.57.22 | SUCCESS | rc=0 >>
LISTEN     0      128         :::8080                    :::*                   users:(("httpd",pid=29187,fd=4),("httpd",pid=29185,fd=4),("httpd",pid=29184,fd=4),("httpd",pid=29183,fd=4),("httpd",pid=29182,fd=4),("httpd",pid=29181,fd=4))
```

## 3.6 roles

ansilbe自1.2版本引入的新特性，用于层次性、结构化地组织playbook。roles能够根据层次型结构自动装载变量文件、tasks以及handlers等。要使用roles只需要在playbook中使用include指令即可。简单来讲，roles就是通过分别将变量、文件、任务、模块及处理器放置于单独的目录中，并可以便捷地include它们的一种机制。角色一般用于基于主机构建服务的场景中，但也可以是用于构建守护进程等场景中。

一个roles的案例如下所示：

```
site.yml
webservers.yml
fooservers.yml
roles/
   common/
     files/
     templates/
     tasks/
     handlers/
     vars/
     meta/
   webservers/
     files/
     templates/
     tasks/
     handlers/
     vars/
     meta/
```

在playbook中,可以这样使用roles:

```
---
- hosts: webservers
  roles:
    - common
    - webservers
```

也可以向roles传递参数:

```
---
  - hosts: webservers
  - common
  - { role: foo_app_instance, dir: '/opt/a', port: 5000 }
  - { role: foo_app_instance, dir: '/opt/b', port: 5001 }
```

也可以用条件来使用roles:

```
---
- hosts: webservers
  - { role: some_role, when: "ansible_os_family == 'RedHat'"  }
```

### 3.6.1 创建roles步骤:

1. 创建以roles命名的目录；
2. 在roles目录中分别创建以各角色名称命名的目录，如webservers等；
3. 在每个角色命名的目录中分别创建files、handlers、meta、tasks、templates和vars目录；用不到的目录可以创建为空目录，也可以不创建；
4. 在playbook文件中，调用各角色；

# 4. ansible速度优化

## 4.1 SSH Multiplexing

ansible运行playbook时会启动很多ssh连接来执行复制文件,运行命令这样的操作.openssh支持这样一个优化,叫做ssh Multiplexing,当使用这个ssh Multiplexing的时候,多个连接到相同主机的ssh回话会共享相同的TCP连接,这样就只有第一次连接的时候需要进行TCP三次握手.

ansible会默认使用ssh Multiplexing特性,一般不需要更改配置,相关的配置项为:

```
[ssh_connection]
control_master = auto # 套接字不存在的情况下自动创建
control_path = $HOME/.ansible/cp/ansible-ssh-%h-%p-%r # 连接套接字存放的位置
control_persist = 60s  # 60s没有ssh连接就关闭主连接
```

## 4.2 pipelining

ansible执行过程中,他会基于调用的模块生成一个python脚本,然后将python脚本复制到主机上,最后执行脚本.ansible支持一个优化,叫做pipelining,在这个模式下ansible执行脚本时并不会去复制它,而是通过管道传递给ssh会话,这会让ansible的ssh会话从2个减少到1个,从而节省时间.

pipelining默认是关闭的, 因为他需要确认被管理主机上的/etc/sudoers文件中的`requiretty`没有启用, 格式如下:

```
Defaults: <username> !requiretty
```

ansible开启pipelining方法, 修改ansible.cfg配置文件:

```
[defaults]
pipelining = True
```

## 4.3 fact缓存

ansible playbook会默认先收集fact信息,如果不需要fact数据可以在playbook中禁用fact采集:

```
- name: not need facts
  hosts: myhosts
  gather_facts: False
  tasks:
    ...
```

也可以全局禁用fact采集:

```
[defaults]
gathering = explicit
```

另一种解决方案就是使用fact缓存,目前ansible支持下面几种fact缓存:

- JSON文件
- Redis
- memcached

JSON文件做fact缓存示例
ansible把采集到的fact写入控制主机的json文件中,如果文件已经存在,那么ansible不会再去主机上采集fact

启用JSON文件缓存,修改ansible.cfg文件:

```
[defaults]
gathering = smart

# 设置超时时间
face_caching_timeout = 86400

# 使用JSON文件做为缓存后端
fact_caching = jsonfile
fact_caching_connection = /tmp/ansible_fact_cache
```

## 4.4 并发数

ansible默认并发数是5,可以用下面两种方法修改并发数:

- 环境变量方式

```
export ANSIBLE_FORKS=20
```

- 设置ansible.cfg

```
[defaults]
forks = 20
```

# 5. ansible 与 docker

ansible内置多种云计算相关模块,如aws,openstack,docker等,下图是ansible与docker相关的模块:



 

![img](https://upload-images.jianshu.io/upload_images/6078939-dd7555be152cfb66.png)

image

## 5.1 使用ansible创建docker镜像

通过playbook和dockerfile相结合的方式生成镜像, 示例如下:

```
FROM ansible/ubuntu14.04-ansible:stable
MAINTAINER xxx

ADD ansible /srv/ansible
WORKDIR /srv/ansible

RUN ansible-playbook web-app.yaml -c local

VOLUME /srv/project/static

WORKDIR /srv/project

EXPOSE 8000

CMD ["gunicorn_django", "-c", "gunicorn.conf.py"]
```

## 5.2 启动容器

ansible可以通过docker模块来操作容器,示例如下:

```
- name: start the postgres container
  docker:
    image: postgres:9.4
    name: postgres
    public_all_ports: True
    env:
        POSTGRES_USER: "{{ database_user }}"
        POSTFRES_PASSWORD: "{{ database_password }}"
```