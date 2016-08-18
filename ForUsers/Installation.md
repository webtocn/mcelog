
# Installation

## mcelog 安装

#### 快速开始
标准的方法应该使用mcelog发行版,但如果你的mcelog很旧的或错误配置了，您可以安装一个新的。

如果您的发行版基于旧的crontab方式的mcelog，你需要避免冲突。最简单的方法是删除mcelog在/etc/cron.\* 中的计划任务文件。

编译git版本或一个tarball 。git版本是目前推荐的并且更有特色。

获取 git 版本 :
```
git clone git://git.kernel.org/pub/scm/utils/cpu/mce/mcelog.git
```

(或者，如果你已经拥有一个分支，你可以`cd mcelog ; git pull -u`)

 

 #### 编译和安装

 ```
 cd mcelog
 make
 ... become root ...
 make install
 ```

 #### 基于Linux中init脚本安装

 设置`mcelog.init`脚本，确保能够开机启动执行。例如可以这样(在opensuse系统):

 ```
 cp mcelog.init /etc/init.d/mcelog
 chkconfig mcelog on
 ```

 其他发行版上可能需要找到相当于`chkconfig`的管理方式。`Mcelog.init` 也有一些配置可以设置,尽管默认值是合理的。

 如果你不重新启动系统， 也可以是使用`/etc/init.d/mcelog start`。如果你正确修改了配置文件，不需要重启进程。

 #### 基于systemd系统安装

 mcelog包含一个标准systemd单位文件:`mcelog.service`。安装服务文件 :
 ```
 cp mcelog.service /usr/lib/systemd/system
 systemctl enable mcelog.service
 ```

 #### 基于upstart系统安装

 这里需要使用init脚本

 #### 其他的安装方式注意问题

 如果你没有配置mcelog在syslog或其他journald日志，您可能需要添加/var/log/mcelog日志轮询设置(如logrotate)。这通常可以在/etc/logrotate .d目录中通过创建一个文件来实现(或重用已存在的版本)。

 您可以验证这个守护进程正在运行完全通过运行
 ```
 mcelog --client
 ```
 这是查询运行的守护进程中的信息。如果没有任何输出那么系统是健康的(没有错误记录)。

 #### 依赖关系

 确认你的系统中存在/dev/mcelog. 如果没有请创建一个： `mknod /dev/mcelog c 10 227` ,基于udev的系统还可以在`/usr/lib/udev/rules.d udev`规则文件 中添加信息：
 ```
 ACTION=="add", KERNEL=="mcelog", SUBSYSTEM=="misc", TAG+="systemd", ENV{SYSTEMD_WANTS}+="mcelog.service"
 ```

 如果/dev不是持久的,在许多新版本上这是需要。特殊发布的版本会在意这些。

 对于bad page offlining你需要一个2.6.33+内核或2.6.32内核与有该功能的补丁 (比如RHEL6或SLES11-SP1)

 内核必须启用`CONFIG_X86_MCE`。对于32位内核需要2.6.30+内核。

 #### Mcelog  运行模式

 Mcelog有三种运行模式： **cronjob(计划)** , **trigger(触发器)** , **daemon(守护进程)**

 推荐的方式是daemon方式,因为一些新的功能(如页面错误预测分析)需要一个连续运行的守护进程。如果你只是想运行在守护进程模式,后面的内容你就不用看了。

 在daemon模式mcelog持续的作为一个守护进程在后台运行等待错误。可以从一个 init脚本运行`mcelog --daemon &`，这是最快和最feature-ful的方式。

 cronjob方式是旧的方法。 mcelog每5分钟从cron运行和检查错误。缺点是它明显的延迟错误报告(高达10分钟),同时不允许mcelog保持扩展状态 。

 trigger方式是一种较新的方法，当内核有错误时运行mcelog。开启方式： 
 ```
 echo /usr/sbin/mcelog   >/sys/devices/system/machinecheck/machinecheck0/trigger 
 ```
 这更快 , 但仍然不允许 mcelog 保持状态 , 并且有相对较高的开销为每个错误 , 因为一个项目必须从头开始初始化。

