# Supervisor
Supervisor install
环境准备
	* Centos 6.8 最简安装
iso文件： http://124.205.69.167/files/4192000003FB0772/mirrors.sohu.com/centos/6.8/isos/x86_64/CentOS-6.8-x86_64-minimal.iso
	* 环境准备将python升级为2.7

   #yum install -y vim
        #yum update
        #yum install centos-release-scl
        #yum install python27
        #scl enable python27 bash
        #cd /opt/rh/python27/root/usr/bin/
        # LD_LIBRARY_PATH=$LD_LIBRARY_PATH ./easy_install-2.7 pip
        #LD_LIBRARY_PATH=$LD_LIBRARY_PATH ./pip2.7 install requests
        #vim /etc/profile
                export LD_LIBRARY_PATH=/opt/rh/python27/root/usr/lib64
                alias python=/opt/rh/python27/root/usr/bin/python
                alias pip=/opt/rh/python27/root/usr/bin/pip
        #pip install -U pip
        # python -V
                Python 2.7.8

## Supervisor 发布历史

* 3.3.1 (2016-08-02) 最新版
* 3.0 (2013-07-30)
* 2.0 (2006-08-30)
* 1.0.5 (2004-07-29)

[Release History](http://supervisord.org/#release-history)

