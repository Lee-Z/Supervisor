# Supervisor

---

## Supervisor 

一个运行在类Unix系统下的进程管理系统工具

* 由Python进行编写
* 采用C/S结构：

	* supervisord是服务进程
	* supervisorctl是客户端管理工具

* 使用ini格式的配置文件

---

## Supervisor 发布历史

* 3.3.1 (2016-08-02) 最新版
* 3.0 (2013-07-30)
* 2.0 (2006-08-30)
* 1.0.5 (2004-07-29)

[Release History](http://supervisord.org/#release-history)

---
## 安装

### 推荐环境
* CentOS 6+ 不建议在V6以下的版本上进行安装
* python 2.7.x [Installing Python 2.7 on Centos 6](../centos6_x/python27_on_centos65.md)

### 安装步骤

	!bash
	# 确认环境
	[root@localhost ~]# cat /etc/redhat-release 
	CentOS release 6.8 (Final)
	[root@localhost ~]# python -V
	Python 2.7.8
	# 使用pip进行安装
	[root@localhost ~]# pip install supervisor
	Collecting supervisor
	  Downloading supervisor-3.3.1.tar.gz (415kB)
	    100% |████████████████████████████████| 419kB 659kB/s 
	Collecting meld3>=0.6.5 (from supervisor)
	  Downloading meld3-1.0.2-py2.py3-none-any.whl
	...
	Successfully installed meld3-1.0.2 supervisor-3.3.1

---

## 配置服务

supervisor 配置文件: supervisord.conf 。`supervisord` 和 `supervisorctl` 都会使用这个文件。

	!bash
	echo_supervisord_conf > /etc/supervisord.conf
	mkdir /etc/supervisord/

### include 分段

	# 支持多级文件配置
	vi /etc/supervisord.conf
	...
	; last line
	[include]
	files = /etc/supervisord/*.ini

参考 [include Section Settings](http://supervisord.org/configuration.html#include-section-settings)

---

## 配置 supervisord 服务自动运行脚本

	!bash
	[root@localhost ~]# wget https://raw.githubusercontent.com/Supervisor/initscripts/master/redhat-init-mingalevme -O /etc/init.d/supervisord
	[root@localhost ~]# chmod +x /etc/init.d/supervisord
	[root@localhost ~]# chkconfig --add supervisord
	[root@localhost ~]# chkconfig --level 2345 supervisord on


	#supervisord脚本配置
	#PREFIX=/usr
	PREFIX=/opt/rh/python27/root/usr

---

## 运行服务

### 启动
	[root@localhost ~]# service supervisord start
###service 报错
```
  # service supervisord status
/opt/rh/python27/root/usr/bin/python2: error while loading shared libraries: libpython2.7.so.1.0: cannot open shared object file: No such file or directory

```
解决方法：
  在/etc/ld.so.conf.d/目录下新建一个conf配置文件，起名为python2.7.conf
将python2.7的库目录放入到python2.7.conf中
/opt/python2.7/lib
保存后，运行ldconfig，就可以了
### 管理

	!bash
	[root@localhost ~]# supervisorctl status
	confluence                       RUNNING   pid 3690, uptime 6 days, 17:40:51
	crowd                            RUNNING   pid 3686, uptime 6 days, 17:40:51
	gerrit-crowd                     RUNNING   pid 3683, uptime 6 days, 17:40:52
	gerrit2                          RUNNING   pid 3729, uptime 6 days, 17:40:51
	jira                             RUNNING   pid 3682, uptime 6 days, 17:40:52
	nginx                            RUNNING   pid 3705, uptime 6 days, 17:40:51

参考 [Running Supervisor](http://supervisord.org/running.html)

---

## 管理 Nginx 服务

要点：

	1. command 关闭daemon模式
	2. user 一定要用 root ， 因为在配置中写了运行用户了

配置：

	[root@localhost /]# vi /etc/supervisord/nginx.ini

	[program:nginx]
	command=/usr/local/nginx/sbin/nginx -g "daemon off;"
	process_name=%(program_name)s
	directory=/usr/local/nginx
	stopsignal=QUIT
	user=root
	stderr_logfile=/usr/local/nginx/logs/%(program_name)s.err
	stdout_logfile=/usr/local/nginx/logs/%(program_name)s.log

---

## 管理 Java 进程

直接在shell中run java会出现stop失败的情况，在启动前添加 exec即可。

### [Gerrit]crowd-ldap-server

	vi [root@localhost /]# vi /etc/supervisord/gerrit-crowd.ini

	[program:gerrit-crowd]
	command=sh /home/gerrit2/crowd-ldap-server/run.sh	; the program (relative uses PATH, can take args)
	process_name=%(program_name)s ; process_name expr (default %(program_name)s)
	numprocs=1                    ; number of processes copies to start (def 1)
	directory=/home/gerrit2/crowd-ldap-server	; directory to cwd to before exec (def no cwd)
	autostart=true                ; start at supervisord start (default: true)
	autorestart=true              ; retstart at unexpected quit (default: true)
	startsecs=3					;wait for 3 seconds
	stopsignal=QUIT               ; signal used to kill process (default TERM)
	stopwaitsecs=5               ; max num secs to wait b4 SIGKILL (default 10)
	user=gerrit2
	stdout_logfile  = /home/gerrit2/crowd-ldap-server/log/%(program_name)s.log
	redirect_stderr = true

---

## 管理 Java 进程(2)
### [Gerrit]GerritCodeReview

	[root@localhost /]# vi /etc/supervisord/gerrit2.ini

	[program:gerrit2]
	command=/var/lib/gerrit2/bin/gerrit.sh run
	process_name=%(program_name)s
	directory=/var/lib/gerrit2
	startsecs=5
	stopsignal=INT
	user=gerrit2
	redirect_stderr=true
	stdout_logfile=/var/lib/gerrit2/logs/%(program_name)s.log

