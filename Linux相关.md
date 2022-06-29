# root用户和sudo使用root权限的区别：

##### 回答一：

#### **sudo指令**

-功能： 以root的身分执行命令
-语法： sudo 其他指令
-用户： 被root加入『/etc/sudoers』文件中的用户

1.root的密码除了root本人知道外，不需被其他需要用到root权限用户知道，因为使用sudo时，要求输入的密码是『该位用户自己的密码』。

2．把所有可执行sudo指令的用户都规范在『/etc/sudoers』这个文件中，root可以很容易地掌控整个系统。

#### **执行sudo su -成root的用户，和root用户的区别：**

普通用户使用sudo 来执行只有root才能执行权限的命令，跟用root用户执行是不一样的，因为这时候他用的**还是普通用户的环境变量。**

用su -成root的用户还是有些环境变量是和root登陆是不一样的。另外，它们的uid也是不一样，只有euid是相同的。



##### 回答二：

sudo可以用用户的环境。

例如，假如我在用户pansz环境中为vim配好了一套很习惯的配置，直接用root的话，那么vim就使用root用户的环境了，我为pansz用户定制的屌炸天的插件都没有了，vim成了原始配置。而用sudo则可以保持用户的环境。

如果用切换账号登录的方式使用root跟普通用户，那么你势必需要为root跟自己的普通用户同步配置两套环境，所有的常用的软件的配置都需要两套，久而久之，你发现维护两套环境太累了，这样的同步不如直接用root方便。然后，就变成了直接用root裸奔了。因而切换用户的机制实际上鼓励用root裸奔。

但有了sudo之后，就可以只为用户配置一套环境。获得root权限时仍然使用用户环境。这样，允许用户一直保持普通用户环境。

同理，当一个主机有多个管理员时，每个人可能都不喜欢别人给root环境中指定的配置，可能导致root用户的配置文件被改来改去，直至发生系统管理员之间的恶性冲突流血事件（逃）。

有了sudo之后，每个人在root权限下都可以使用自己用户的环境配置，就可以只用定义自己用户的环境不修改root用户的配置文件，从而互相不影响了。



# Linux Crontab定时任务

https://wangchujiang.com/linux-command/c/crontab.html#!kw=

linux内置的cron进程能帮我们实现这些需求，cron搭配shell脚本，非常复杂的指令也没有问题。

### cron介绍

我们经常使用的是crontab命令是cron table的简写，它是cron的配置文件，也可以叫它作业列表，我们可以在以下文件夹内找到相关配置文件。

- /var/spool/cron/ 目录下存放的是每个用户包括root的crontab任务，每个任务以创建者的名字命名
- /etc/crontab 这个文件负责调度各种管理和维护任务。
- /etc/cron.d/ 这个目录用来存放任何要执行的crontab文件或脚本。
- 我们还可以把脚本放在/etc/cron.hourly、/etc/cron.daily、/etc/cron.weekly、/etc/cron.monthly目录中，让它每小时/天/星期、月执行一次。

### crontab的使用

我们常用的命令如下：

```
crontab [-u username]　　　　//省略用户表表示操作当前用户的crontab
    -e      (编辑工作表)
    -l      (列出工作表里的命令)
    -r      (删除工作作)
```

我们用**crontab -e**进入当前用户的工作表编辑，是常见的vim界面。每行是一条命令。

crontab的命令构成为 时间+动作，其时间有**分、时、日、月、周**五种，操作符有

- ***** 取值范围内的所有数字
- **/** 每过多少个数字
- **-** 从X到Z
- **，**散列数字

------

## 实例



##### 实例1：每1分钟执行一次myCommand

```
* * * * * myCommand
```

##### 实例2：每小时的第3和第15分钟执行

```
3,15 * * * * myCommand
```

##### 实例3：在上午8点到11点的第3和第15分钟执行

```
3,15 8-11 * * * myCommand
```



# ddos deflate 安装使用详解

https://www.jianshu.com/p/f1e44408c195

```
crontab -l 也无法查看到此定时任务
```

注意：

![image-20220219111053403](/Users/changzw/Library/Application Support/typora-user-images/image-20220219111053403.png)

# 查看 定时任务 crontab 日志记录

```
 /var/log/cron.log
```

```
tail -f /var/log/cron
```

看 /var/log/cron.log这个文件就可以，可以用 tail -f /var/log/cron 观察

https://blog.csdn.net/whatday/article/details/107109508

#### 注意：

```
如果系统没有开启（系统默认不开启）crontab 日志，则需要手动编辑 sudo vi /etc/rsyslog.d/50-default.conf 文件，取消cron的注释，然后重启 服务 sudo service rsyslog restart
```

<img src="/Users/changzw/Library/Application Support/typora-user-images/image-20220219112140852.png" alt="image-20220219112140852" style="zoom:50%;" />

## 重启服务cron：

```
service cron restart
```

### /dev/null 2>&1什么意思

```
" >/dev/null 2>&1 "常用来避免shell命令或者程序等运行中有内容输出。
```

### 查看所有用户的定时任务

```
cat /etc/passwd | cut -f 1 -d : |xargs -I {} crontab -l -u {}
```

查看文件的最后操作时间：

```
stat filename
```

