---
layout: post
title:  "linux ps 命令"
date:   2016-11-22 21:51:00 +0800
categories: swumao update linux ps
---

ps命令是我们在日常中使用比较多的命令，比如当我们需要查看一个程序是否正在运行中，一般情况下会使用如下的命令：

```
ps aux | grep php-fpm

root          1  0.0  0.7 129304 15208 ?        Ss   Nov19   0:03 php-fpm: master process (/usr/local/etc/php-fpm.conf)
www-data      5  0.1  0.6 129784 13716 ?        S    Nov19   2:46 php-fpm: pool www
www-data      6  0.1  0.8 133644 17592 ?        S    Nov19   2:44 php-fpm: pool www
www-data      7  0.1  0.7 132344 15904 ?        D    Nov19   2:15 php-fpm: pool www
```

那么下面的这些输出分别代表什么意思呢？下面将就ps命令平时用到最多的两个参数进行说明。

1. `ps -l`

  从左到右分别是：
  - F 进程标志，说明进程权限，其中4代表root权限，1表示仅可进行fork，但不可exec
  - S 进程状态，详细描述见后面的备注
  - UID 该进程被该uid所拥有
  - PID 该进程的pid号码
  - UUID 此进程的父进程pid号码
  - C CPU使用率，单位为百分比
  - PRI Priority 代表此进程被CPU所执行的优先级，数值越小代表此进程越快被CPU执行
  - NI Nice -20~19，PRI(new) = PRI(old)+Nice
  - ADDR 指出该进程在内存的哪个部分 如果是running的进程，一般会显示-
  - SZ 代表此进程用掉多少内存
  - WCHAN 表示目前进程是否在运行中 如果是running的进程，一般会显示-
  - TTY 登陆者的终端位置，如果为远程登录使用动态终端接口(pts/n)
  - TIME 使用CPU的时间 是此进程实际花费CPU运行的时间，而不是系统时间
  - CMD command，造成此程序的触发进程的命令

2. `ps aux`

  从左到右分别是：
  - USER 该进程数据哪个用户账号的
  - PID 该进程的进程标识号
  - %CPU 该进程使用掉的CPU资源百分比
  - %MEM 该进程所占用的物理内存百分比
  - VSZ 该进程所占用的虚拟内存量(单位KiB, 1KiB=1024byte)
  - RSS 该进程所占用的固定内存量
  - TTY 该进程在哪个终端机上面运行
  - STAT 该进程目前的状态，详细说明见后面的备注
  - START 程序开始的时间
  - TIME 该进程实际使用CPU的时间
  - COMMAND 该进程的实际命令

备注：

1. 进程状态的说明
  - R 运行中 running
  - S 睡眠状态(idle)，但可以被唤醒(signal)
  - D 不可被唤醒状态，通常可能在等待I/O
  - T 停止状态，可能是在工作控制（后台暂停）或出错（trace）状态
  - Z 僵尸状态 该进程已经被终止但却无法被删除至内存外
  - X dead
  - < 高优先级
  - N 低优先级
  - s 包含子进程
  - `+` 位于后台的进程组
  - L 有些页被锁进内存
  - l 多线程的
