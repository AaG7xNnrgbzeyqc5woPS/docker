
[关于Docker目录挂载的总结](https://www.cnblogs.com/ivictor/p/4834864.html)

---
以下是备份,请看原文

```

Docker容器启动的时候，如果要挂载宿主机的一个目录，可以用-v参数指定。

譬如我要启动一个centos容器，宿主机的/test目录挂载到容器的/soft目录，可通过以下方式指定：

# docker run -it -v /test:/soft centos /bin/bash

这样在容器启动后，容器内会自动创建/soft的目录。通过这种方式，我们可以明确一点，即-v参数中，冒号":"前面的目录是宿主机目录，后面的目录是容器内目录。

貌似简单，其实不然，下面我们来验证一下：

一、容器目录不可以为相对路径

[root@localhost ~]# docker run -it -v /test:soft centos /bin/bash
invalid value "/test:soft" for flag -v: soft is not an absolute path
See 'docker run --help'.

直接报错，提示soft不是一个绝对路径，所谓的绝对路径，必须以下斜线“/”开头。

二、宿主机目录如果不存在，则会自动生成

如果宿主机中存在/test目录，首先删除它

[root@localhost ~]# rm -rf /test
[root@localhost ~]# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

启动容器

[root@localhost ~]# docker run -it -v /test:/soft centos /bin/bash
[root@a487a3ca7997 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  soft  srv  sys  tmp  usr  var

查看宿主机，发现新增了一个/test目录

[root@localhost ~]# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  test  tmp  usr  var

三、宿主机的目录如果为相对路径呢？

这次，我们换个目录名test1试试

# docker run -it -v test1:/soft centos /bin/bash

再到宿主机上查看是否新增了一个/test1目录，结果没有，是不是因为我用的是相对路径，所以生成的test1目录在当前目录下，结果发现还是没有。那容器内的/soft目录挂载到哪里去了？通过docker inspect命令，查看容器“Mounts”那一部分，我们可以得到这个问题的答案。
复制代码

    "Mounts": [
        {
            "Name": "test1",
            "Source": "/var/lib/docker/volumes/test1/_data",
            "Destination": "/soft",
            "Driver": "local",
            "Mode": "z",
            "RW": true
        }
    ],

复制代码

可以看出，容器内的/soft目录挂载的是宿主机上的/var/lib/docker/volumes/test1/_data目录

原来，所谓的相对路径指的是/var/lib/docker/volumes/，与宿主机的当前目录无关。

四、如果只是-v指定一个目录，这个又是如何对应呢？

启动一个容器

[root@localhost ~]# docker run -it -v /test2 centos /bin/bash
[root@ea24067bc902 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  test2  tmp  usr  var

同样使用docker inspect命令查看宿主机的挂载目录
复制代码

   "Mounts": [
        {
            "Name": "96256232eb74edb139d652746f0fe426e57fbacdf73376963e3acdb411b3d73a",
            "Source": "/var/lib/docker/volumes/96256232eb74edb139d652746f0fe426e57fbacdf73376963e3acdb411b3d73a/_data",
            "Destination": "/test2",
            "Driver": "local",
            "Mode": "",
            "RW": true
        }
    ],

复制代码

可以看出，同3中的结果类似，只不过，它不是相对路径的目录名，而是随机生成的一个目录名。

五、如果在容器内修改了目录的属主和属组，那么对应的挂载点是否会修改呢？

首先开启一个容器，查看容器内/soft目录的属性

[root@localhost ~]# docker run -it -v /test:/soft centos /bin/bash
[root@b5ed8216401f /]# ll -d /soft/
drwxr-xr-x 2 root root 6 Sep 24 03:48 /soft/

查看宿主机内/test目录的属性

[root@localhost ~]# ll -d /test/
drwxr-xr-x 2 root root 6 Sep 24 11:48 /test/

在容器内新建用户，修改/soft的属主和属组

[root@b5ed8216401f /]# useradd victor
[root@b5ed8216401f /]# chown -R victor.victor /soft/
[root@b5ed8216401f /]# ll -d /soft/
drwxr-xr-x 2 victor victor 6 Sep 24 03:48 /soft/

再来看看宿主机内/test目录的属主和属组是否会发生变化？

[root@localhost ~]# ll -d /test/
drwxr-xr-x 2 mycat mycat 6 Sep 24 11:48 /test/

竟然变为mycat了。。。

原来，这个与UID有关系，UID，即“用户标识号”，是一个整数，系统内部用它来标识用户。一般情况下它与用户名是一一对应的。

首先查看容器内victor对应的UID是多少，

[root@b5ed8216401f /]# cat /etc/passwd | grep victor
victor:x:1000:1000::/home/victor:/bin/bash

victor的UID为1000，那么宿主机内1000对应的用户是谁呢？

[root@localhost ~]# cat /etc/passwd |grep 1000
mycat:x:1000:1000::/home/mycat:/bin/bash

可以看出，宿主机内UID 1000对应的用户是mycat。

六、容器销毁了，在宿主机上新建的挂载目录是否会消失？

在这里，主要验证两种情况：一、指定了宿主机目录，即 -v /test:/soft。二、没有指定宿主机目录，即-v /soft

第一种情况：
复制代码

[root@localhost ~]# rm -rf /test    --首先删除宿主机的/test目录
[root@localhost ~]# ls /    --可以看到，宿主机上无/test目录
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@localhost ~]# docker run -it --name=centos_test -v /test:/soft centos /bin/bash  --启动容器，为了删除方便，我用--name参数指定了容器的名字
[root@82ad7f3a779a /]# exit
exit
[root@localhost ~]# docker rm centos_test   --删除容器
centos_test
[root@localhost ~]# ls /   --发现 /test目录依旧存在
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  test  tmp  usr  var

复制代码

可以看出，即便容器销毁了，新建的挂载目录不会消失。进一步也可验证，如果宿主机目录的属主和属组发生了变化，容器销毁后，宿主机目录的属主和属组不会恢复到挂载之前的状态。

第二种情况，通过上面的验证知道，如果没有指定宿主机的目录，则容器会在/var/lib/docker/volumes/随机配置一个目录，那么我们看看这种情况下的容器销毁是否会导致相应目录的删除

首先启动容器

[root@localhost ~]# docker run -it --name=centos_test -v /soft centos /bin/bash
[root@6b75579ec934 /]# exit
exit

通过docker inspect命令查看容器在宿主机上生成的挂载目录
复制代码

    "Mounts": [
        {
            "Name": "b53164cb1c9f1917788638692fb22ad11994cf1fbbc2461b6c390cd3e10ea301",
            "Source": "/var/lib/docker/volumes/b53164cb1c9f1917788638692fb22ad11994cf1fbbc2461b6c390cd3e10ea301/_data",
            "Destination": "/soft",
            "Driver": "local",
            "Mode": "",
            "RW": true
        }
    ],

复制代码

对应的是/var/lib/docker/volumes/b53164cb1c9f1917788638692fb22ad11994cf1fbbc2461b6c390cd3e10ea301/_data目录

销毁容器，看目录是否存在

[root@localhost ~]# docker rm centos_test
centos_test
[root@localhost ~]# ll /var/lib/docker/volumes/b53164cb1c9f1917788638692fb22ad11994cf1fbbc2461b6c390cd3e10ea301
total 0
drwxr-xr-x 2 root root 6 Sep 24 14:25 _data

发现该目录依旧存在，即便重启了docker服务，该目录依旧存在

[root@localhost ~]# systemctl restart docker
[root@localhost ~]# ll /var/lib/docker/volumes/b53164cb1c9f1917788638692fb22ad11994cf1fbbc2461b6c390cd3e10ea301
total 0
drwxr-xr-x 2 root root 6 Sep 24 14:25 _data

七、挂载宿主机已存在目录后，在容器内对其进行操作，报“Permission denied”。

可通过两种方式解决：

1> 关闭selinux。

临时关闭：# setenforce 0

永久关闭：修改/etc/sysconfig/selinux文件，将SELINUX的值设置为disabled。

2> 以特权方式启动容器 

指定--privileged参数

如：# docker run -it --privileged=true -v /test:/soft centos /bin/bash

```
