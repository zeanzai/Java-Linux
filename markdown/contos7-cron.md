
# Contos7中Cron的使用

# 前言

笔者在进行试验时，有以下几个操作习惯，具体[参考](/post/dev/how-to-devops/)。

Centos7默认开通了80端口和22端口。

# 查看服务相关信息

```shell
$ systemctl status crond		// crond状态
$ systemctl is-enabled crond	// 是否开机自启
```

# 基础知识

## Cron时间表达式详解

### 表达式概述

```
.---------------- minute (0 - 59)：代表分钟，取值范围00-59
|  .------------- hour (0 - 23)：代表小时，取值范围00-23
|  |  .---------- day of month (1 - 31)：代表月份中的日期，取值范围01-31
|  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...：代表月份，取值范围01-12
|  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
|  |  |  |  |
*  *  *  *  * user-name  command to be executed
```

### 特殊符号含义

| 特殊符号  | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 表示任意时间都，也是”每”的意思，举例：如00 23 * * *cmd表示每月每周每日的23:00都执行cmd任务 |
| -        | 表示分隔符，表示一个时间段范围段，如17-19点，每小时的00分执行任务，00 17-19 * * * cmd 。就是17,18,19点整点分别执行的意思 |
| ,        | 表示分隔时段的意思，30 17,18,19 * * * /bin.sh /scripts/dingjian.sh表示每天17,18和19点的半点时刻执行/scripts/dingjian.sh脚本。也可以和”-”结合使用，如：30 3-5,17-19 * * * /scripts/dingjian.sh |
| /n       | 即”每隔n单位时间”，如：每10分钟执行一次任务可以写成 */10 * * * * cmd,其中“*/10”的范围是0-59，因此也可以写成0-59/10 |

## 命令概述

### 指定语法

```shell
crontab [-u user] file
        crontab -u user
                (default operation is replace, per 1003.2)
        -e      (edit user's crontab)      编辑用户命令
        -l      (list user's crontab)       列表
        -r      (delete user's crontab)    删除用户任务
        -i      (prompt before deleting user's crontab)     在删除前确认
        -s      (selinux context)
```

| 参数 | 含义                                          | 示例              |
| ---- | --------------------------------------------- | ----------------- |
| -l   | 查看crontab文件内容，提示:l为list的缩写       | crontab -l        |
| -e   | 编辑crontab文件内容，提示：e可为edit 的缩写   | crontab -e        |
| -i   | 删除crontab文件内容，删除前会提示确认，用得少 | crontab -ri       |
| -r   | 删除crontab文件内容，用得很少                 | crontab -r        |
| -u   | 指定使用的用户执行任务                        | crontab -u boy -l |

-I –r参数在生产中很少用，没什么需求必须要用-e进去编辑即可

补充: crontab {-l|-e} 实际上就是在操作/var/spool/cron/当前用户这样的文件



## 相关文件

| 文件             |                                                              |
| ---------------- | ------------------------------------------------------------ |
| /etc/cron.deny   | 该文件中所列用户不允许使用crontab命令                        |
| /etc/cron.allow  | 该文件中所列用户允许使用crontab命令，优先于/etc/cron.deny    |
| /var/spool/cron/ | 所有用户crontab配置文件默认都存放在此目录，**文件名以用户名命名** |
| /var/log/cron    | 定时任务的执行日志                                           |



# 示例

```shell
// 1. 查看当前用户的定时任务
	$ crontab -l

// 2. 为当前用户编辑一个定时任务
	$ crontab -e

// 3. 清空当前用户的定时任务
	$ crontab -r

// 4. 每分钟打印一次自己的英文名字到 /home/test/name.txt 的文件中
方式一：
    $ mkdir /home/test // 创建文件目录
    $ crontab -e // 输入以下内容

    # print my name
    * * * * * echo "zeanzai"  >> /home/test/name.txt

    $ cat /home/test/name.txt // 查看输出
    zeanzai

方式二：
    $ mkdir /home/test // 创建文件目录
    $ vi /var/spool/cron/root // 编辑定时任务配置文件，输入以下内容
    # print my name
    * * * * * echo "zeanzai"  >> /home/test/name.txt

// 5. 查看定时任务执行的日志
	$ tail -f /var/log/cron

// 6. 查看定时任务的配置文件
方式一：
    $ ll /var/spool/cron/
    $ cat root

方式二：
    $ crontab -l

// 7. 删除定时任务
    $ crontab -ir
    yes


// 8. 每天00:01打包昨天的日志文件到tar文件，并删除昨天的日志文件
$ mkdir /home/logs/school-hydroelectricity/tar
$ vi /etc/scripts/tar.sh
cd /home/logs/school-hydroelectricity
tar zcf ./tar/$(date +'%Y-%m-%d' -d '-1 days').tar.gz ./$(date +'%Y-%m-%d' -d '-1 days')
rm -rf ./$(date +'%Y-%m-%d' -d '-1 days')

$ ./etc/scripts/tar.sh
$ crontab -e
# 每天00:01打包昨天的日志文件到tar目录中，并删除昨天的日志文件，要求打包文件以日期命名
* * * * * /bin/sh /etc/scripts/tar.sh
```



# 生产问题案例及解决过程

面试题：维护的时候，创建文件提示”No space left on device”,请问你这是什么故障：

解答：磁盘空间block满了或者inode被占满了

## 故障描述及说明

某年某月甘日某时，某人在工作中设置crontab定时任务规则保存时，提示” No space left on device”，此时用df -h检查磁盘，发现还有剩余空间，用df -I 检查则显示/var目录己占用100%的inode数量，看来是inode数量耗尽，导致系统无法在/var目录下创建文件，因为定时任务的配置在/var/spool/cron下，ext3文件系统中，每个文件需要占一个inode。

## 故障原因分析

当系统中crond定时任务执行程序有输出内容时，输出内容会以邮件形式发给crond的用户（默认是root），而sendmail等mail服务没有启动时，这些输出内容以为支在邮件队列临时目录，产生这些碎文件，导致消耗inode数量，一旦inode数量耗尽，就会导致系统无法写入文件，而报上述错误：No space left on device.

## 亡羊补牢解决方法

1) 尽量将crontab里面的命令或脚本中的命令结尾加上>/dev/null 2>&1，或在做定时执行脚本时，把屏幕输出定向到指定文件里

2) 当然也可以开启邮件服务，不过最好不做，因为邮件服务会带来安全问题

3) 优化系统，加定时清理任务，如find /var/spool/clientmqueue/ -type f -mtime +30|xargs rm -f



# 调试crontab定时任务

1. **增加执行频率调试任务**
2. **调整系统时间调试任务**
3. **通过日志输出调试定时任务**
4. **通过定时任务日志调试定时任务**


# 参考

1. http://blog.51cto.com/mrxiong2017/2084803
2. https://blog.csdn.net/andrewgb/article/details/47374963
