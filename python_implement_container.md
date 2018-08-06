# 理解容器技术的部分技术本质之用python实现一个简单容器
## 什么是容器
   随着docker这门容器技术的开源，容器这门技术快速的进入了技术人员的视野，凭借容器技术对程序运行环境的打包封装的特性，并且相比较传统虚拟机而言要轻很多的优点，解决了一直让开发人员和运维人员，交付人员在开发环境和生产环境中部署的痛点，获得了开发，运维，交付人员的青睐。时下，微服务架构概念的火热，更是为docker容器技术的大热，添了一把助力。那么什么是容器呢，容器的本质是什么？docker是怎么实现的呢？笔者今天就会通过用几十行python代码来实现一个最简单的容器，来帮助大家理解容器技术的部分本质技术特性。
### 获取一个容器
   让我们首先获取一个容器，通过执行一条简单的docker命令，拉取我们需要的容器，比如:
```console
$ docker pull ubuntu
```
### 运行一个容器
   在我获取到一个ubuntu容器后，让我们来启动运行它，执行一条简单的命令，比如:
```console
$ docker run ubuntu echo "Hello World"
Hello World
```
   我们也可以在容器中启动一个shell，进入交互模式:
```console
$ docker run -it ubuntu
root@860192c939c8:/#
```
   进入交互模式后,我们可以执行列表命令，查看容器下的文件目录:
```console
root@860192c939c8:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@860192c939c8:/# ls -la
total 76
drwxr-xr-x   1 root root 4096 Jun 21 12:19 .
drwxr-xr-x   1 root root 4096 Jun 21 12:19 ..
-rwxr-xr-x   1 root root    0 Jun 21 12:19 .dockerenv
drwxr-xr-x   2 root root 4096 May 26 00:45 bin
drwxr-xr-x   2 root root 4096 Apr 24 08:34 boot
drwxr-xr-x   5 root root  360 Jun 21 12:19 dev
drwxr-xr-x   1 root root 4096 Jun 21 12:19 etc
drwxr-xr-x   2 root root 4096 Apr 24 08:34 home
drwxr-xr-x   8 root root 4096 May 26 00:44 lib
drwxr-xr-x   2 root root 4096 May 26 00:44 lib64
drwxr-xr-x   2 root root 4096 May 26 00:44 media
drwxr-xr-x   2 root root 4096 May 26 00:44 mnt
drwxr-xr-x   2 root root 4096 May 26 00:44 opt
dr-xr-xr-x 365 root root    0 Jun 21 12:19 proc
drwx------   2 root root 4096 May 26 00:45 root
drwxr-xr-x   1 root root 4096 Jun  5 21:20 run
drwxr-xr-x   1 root root 4096 Jun  5 21:20 sbin
drwxr-xr-x   2 root root 4096 May 26 00:44 srv
dr-xr-xr-x  13 root root    0 Jun 21 11:59 sys
drwxrwxrwt   2 root root 4096 May 26 00:45 tmp
drwxr-xr-x   1 root root 4096 May 26 00:44 usr
drwxr-xr-x   1 root root 4096 May 26 00:45 var
```
我们还可以执行ps查看进程:
```console
root@860192c939c8:/# ps
  PID TTY          TIME CMD
    1 pts/0    00:00:00 bash
   46 pts/0    00:00:00 ps
```
我们还可以执行hostname获得容器的hostname
```console
root@860192c939c8:/# hostname
860192c939c8
```
这里笔者向大家主要执行了，查看文件目录，ps, hostname命令，通过这三个命令，我们发现容器内的这些命令的返回结果和宿主主机是完全不同的，很多读者想必会说，这不是废话吗，但是大家有没有想过，为什么执行这几个命令会和宿主主机命令返回结果不同，这是为什么呢？接下来就由笔者带着大家用python代码来一步步实现一个容器，探秘docker的秘密。
## 用Python实现一个容器
  通过笔者使用docker的命令来看，我们可以发现docker命令执行的基本格式是, docker run <容器> <命令> <参数>，我们首先就来模拟docker run命令这个过程。
### 执行最简单的命令
  我们首先来模拟最初的docker的容器一个回显命令，我们希望 python docker.py run <命令> <参数>能达到之前运行docker命令一样的效果。以下是代码实现
```python
## our docker.py script
import sys
import subprocess
from multiprocessing import Process


def target(cmd):
    print 'execute [{}]'.format(' '.join(cmd))
    subprocess.call(cmd)


def run():
    cmd = sys.argv[2:]
    p = Process(target=target, args=(cmd,))
    p.start()
    p.join()

def main():
    if sys.argv[1] == 'run':
        run()
    else:
        print 'unsupported command'

if  __name__ == '__main__':
    main()

```
  接着我们来执行它，看下是不是如docker run一样,
```console
$ python docker.py run echo "Hello, python docker"
execute [echo Hello, python docker]
Hello, python docker
```
  同样，我可以也可以以交互模式来执行它，看看效果，
```console
$ python docker.py run /bin/bash
execute [/bin/bash]
root@roxas:~/mock/wjy$ ls
docker.py  hello.py  jj.py
root@roxas:~/mock/wjy$ ps
  PID TTY          TIME CMD
  745 pts/2    00:00:00 python
  746 pts/2    00:00:00 bash
  784 pts/2    00:00:00 ps
 7508 pts/2    00:00:03
root@roxas:~/mock/wjy$ whoami
root
root@roxas:~/mock/wjy$ echo $$
17564
root@roxas:~/mock/wjy$ hostname
roxas
root@roxas:~/mock/wjy$ hostname test
root@roxas:~/mock/wjy$ hostname
test
root@roxas:~/mock/wjy$ exit
$ hostname
test
```
  我们分别执行了echo,并且在交互模式(/bin/bash)下执行了ls, whoami, echo $$, ps, hostname命令并且得到了正确的返回结果，感觉和执行docker run -it ubuntu似乎一样，然而实际并不是。我们仅仅是执行命令而已，并没有真的进入隔离的容器环境，我们依旧处于宿主的文件系统，以及宿主的进程空间中。
### 隔离 UTS 的命名空间
  现在我尝试来隔离容器的 UTS 命名空间，这里比较遗憾的是，python并没有直接提供各种隔离级别的常量标识符，以及核心的系统调用函数，我们必须使用C语言的libc.clone函数，并且把<sched.h>头文件中的部分常量，在py代码中定义一次，从而实现 UTS 的命名空间隔离，并且需要继承multiprocessing.Process，对它做一些修改，从而实现 UTS 命名空间的隔离:
```python
## our docker.py script
from ctypes import *            # import ctypes so that could call libc.* function
from signal import SIGCHLD      # subprocess siganl
import sys
import os
import subprocess
from multiprocessing import Process
from multiprocessing.forking import Popen

libc = CDLL(None)
STACK_SIZE = 1024*1024      # stack size
flags = 0
CLONE_NEWUTS = 0x04000000   # redefine CLONE_NEWUTS in our py script from <sched.h> header file
flags |= CLONE_NEWUTS       # set flags

class Clone(Popen):
    def __init__(self, process_obj):
        self.process_obj = process_obj
        self.returncode = None
        child = CFUNCTYPE(c_int)(self.child)                                              # subprocess func
        child_stack = create_string_buffer(STACK_SIZE)                                    # set func stack
        child_stack_pointer = c_void_p(cast(child_stack, c_void_p).value + STACK_SIZE)    # set func stack pointer

        self.pid = libc.clone(child, child_stack_pointer, flags | SIGCHLD)    # core system call libc.clone and set FLAGS

    def child(self):
        code = self.process_obj._bootstrap()

class Container(Process):
    _Popen = Clone


def target(cmd):
    print 'execute [{}]'.format(' '.join(cmd))
    subprocess.call(cmd)


def run():
    cmd = sys.argv[2:]
    p = Container(target=target, args=(cmd,))
    p.start()
    p.join()


def main():
    if sys.argv[1] == 'run':
        run()
    else:
        print 'unsupported command'


if  __name__ == '__main__':
    main()
```
  通过读docker.py代码，可以看到笔者对docker.py文件的修改，其中最核心的部分，就是引入了ctypes模块，从而可以通过libc来使用系统调用，而Clone类就是最核心的类，它的初始化函数就是如何使用libc.clone这个系统调用的，另外，笔者还定义了CLONE_NEWUTS命名空间标识符，这是直接从linux/include/uapi/linux/sched.h 头文件的定义而来。然后让我们来执行它:
```console
$ python docker2.py run /bin/bash
execute [/bin/bash]
root@roxas:~/mock/wjy# hostname
roxas
root@roxas:~/mock/wjy# hostname test
root@roxas:~/mock/wjy# hostname
test
root@roxas:~/mock/wjy# exit
exit
$ hostname
roxas
```
  可以看到，我们执行hostname test修改了容器内的hostname，但是退出容器后，宿主主机的hostname没有变化，两个命名空间是独立的，容器与宿主的hostname命名空间已经被隔离。
### 隔离 PID 的命名空间
  在上面隔离hostname的命名空间中，我们引入了 CLONE_NEWUTS 标识符，同样我们要隔离PID的命名空间，需要引入 CLONE_NEWPID 标识符。因为其他python代码基本没有变化，我这里只把新增加的部分代码贴出来:
```python
## I just show different codes
## ....
libc = CDLL(None)
STACK_SIZE = 1024*1024      # stack size
flags = 0
CLONE_NEWUTS = 0x04000000   # redefine CLONE_NEWUTS in our py script from <sched.h> header file
CLONE_NEWPID = 0x20000000
flags |= CLONE_NEWUTS       # set flags
flags |= CLONE_NEWPID
# ....
```
  然后让我们来执行它:
```console
$ python docker2.py run /bin/bash
execute [/bin/bash]
root@roxas:~/mock/wjy#
root@roxas:~/mock/wjy#
root@roxas:~/mock/wjy# echo $$
2
root@roxas:~/mock/wjy# exit
exit
```
  可以看到，笔者通过执行echo $$显示当前进程号，发现容器的进程号，已经是2了,进程号不再是4位数，甚至是5位数。但是这里进程号为什么不是1呢？因为这里笔者使用的subprocess.call函数，该函数启动一个子进程执行调用，所以容器进程号是1，随后启动的子进程的进程号是2。在容器中，我们可以看到进程号被重置了，从而说明容器的PID命名空间和宿主主机的PID命名空间已经隔离出来。
### 隔离 USER 的命名空间
  看到这里，读者应该猜到了后续的操作，我们需要继续引入隔离USER命名空间的标识符然后设置标志位，从而做到USER命名空间的隔离，还是熟悉的配方，熟悉的味道:
```python
## I just show different codes
## ....
libc = CDLL(None)
STACK_SIZE = 1024*1024      # stack size
flags = 0
CLONE_NEWUTS = 0x04000000   # redefine CLONE_NEWUTS in our py script from <sched.h> header file
CLONE_NEWPID = 0x20000000
CLONE_NEWUSER = 0x10000000
flags |= CLONE_NEWUTS       # set flags
flags |= CLONE_NEWPID
flags |= CLONE_NEWUSER
# ....
```
  接着来执行它
```console
$ python docker2.py run /bin/bash
execute [/bin/bash]
nobody@roxas:~/mock/wjy$ whoami
nobody
nobody@roxas:~/mock/wjy$
```
  可以看到通过执行whami，这里返回结果已经是nobody，USER命名空间也隔离出来了。
### 隔离 NETWORK 的命名空间
  轮到隔离NETWORK命名空间。我们继续，还是熟悉的配方，熟悉的味道，不废话，直接贴代码:
```python
## I just show different codes
## ....
libc = CDLL(None)
STACK_SIZE = 1024*1024      # stack size
flags = 0
CLONE_NEWUTS = 0x04000000   # redefine CLONE_NEWUTS in our py script from <sched.h> header file
CLONE_NEWPID = 0x20000000
CLONE_NEWUSER = 0x10000000
CLONE_NEWNET = 0x40000000
flags |= CLONE_NEWUTS       # set flags
flags |= CLONE_NEWPID
flags |= CLONE_NEWUSER
flags |= CLONE_NEWNET
# ....
```
  接着来执行它
```console
$ python docker2.py run /bin/bash
execute [/bin/bash]
nobody@roxas:~/mock/wjy$ ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
nobody@roxas:~/mock/wjy$ exit
exit
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp4s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 3c:97:0e:80:9a:d5 brd ff:ff:ff:ff:ff:ff
3: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 60:6c:66:00:a8:fc brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.11/16 brd 192.168.255.255 scope global dynamic noprefixroute wlp3s0
       valid_lft 2358sec preferred_lft 2358sec
    inet6 fe80::42a8:bd8b:f164:e2a1/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:1f:a0:59:2a brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:1fff:fea0:592a/64 scope link
       valid_lft forever preferred_lft forever
```
  可以看到，在容器中和宿主主机中分别执行 ip addr命令，返回不同的结果。网络设备已经做到容器和宿主主机的隔离。
### 信号量，消息队列，共享内存，文件系统等命名空间的隔离
  以上，笔者带着大家通过代码实现的UTS, PID, USER, NETWORK的命名空间隔离，还有IPC, MOUNT的命名空间的隔离，其中涉及到标志符CLONE_NEWIPC，CLONE_NEWNS等，但是这里已经不能通过简单设置这2个标志位，就做到容器与宿主主机的隔离，还需要执行其他步骤才能做到，比如需要把容器挂载到一个文件系统中，才能做到MOUNT的命名空间的隔离，实现代码会比较复杂，并且需要在操作系统内挂载一个新的文件系统，脱离笔者希望通过几十行python代码让大家了解容器技术部分本质的初衷。如果读者有兴趣，可以尝试实现这部分代码。
## 命名空间的隔离总结
 namespace | 系统调用参数 | 隔离内容
-------- | -----| ----- | --------
UTS    | CLONE_NEWUTS | 主机与域名 |
PID | CLONE_NEWPID | 进程编号 |
NETWORK | CLONE_NEWNET | 网络设备、网络栈、端口等 |
USER | CLONE_NEWUSER | 用户和用户组 |
IPC | CLONE_NEWIPC  | 信号量、消息队列和共享内存  |
MOUNT | CLONE_NEWNS       | 挂载点（文件系统） |
  这个表清晰的展示出了容器技术命名空间的6个namespace隔离级别，其中笔者带着大家实现了其中的4项隔离。
## 总结
  希望坚持阅读到本处的读者，能理解容器技术命名空间的隔离机制，并且理解了笔者如何通过python代码实现了一个最简单的容器。namespace是容器技术实现细节中一个比较重要的部分，另外容器技术的实现，还涉及到CGROUP等资源控制的部分。而笔者也还处于摸索CGROUP资源控制的学习过程中。有兴趣的同学，可以通过google或者学习docker的代码，来了解这部分内容。
  最后，笔者在准备此文章时间仓促，难免会有错误，欢迎大家指正。
## 附录
  笔者在写此文章，难免会去做一些项目或者文章的参考与引用，以下是参考文章或者项目的目录
  https://conf.top/how-docker-works.html
  https://github.com/docker/docker-ce
  https://pypi.org/project/pyspaces
  https://github.com/Friz-zy/pyspaces
