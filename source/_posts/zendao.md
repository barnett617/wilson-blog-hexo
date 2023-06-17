---
title: 在Ubuntu 16.04安装禅道
date: 2020-01-03 09:30:00
tags:
---

# 在Ubuntu 16.04安装禅道

## 1. 在任意目录下载解压版压缩包

```
wget http://dl.cnezsoft.com/zentao/10.4/ZenTaoPMS.10.4.stable.zbox_64.tar.gz
```

[下载地址](https://www.zentao.net/download/80090.html)

## 2. 将压缩包解压到指定目录`/opt`（必须）

```
sudo tar -zxvf ZenTaoPMS.10.4.stable.zbox_64.tar.gz -C /opt
```

## 3. 指定apache和mysql端口启动

```
/opt/zbox/zbox -ap 8080 -mp 3306 start
```

查看是否提示

```
Start Apache success
Start Mysql success
```

## 4. 登录使用

初始账号
```
admin
123456
```

## 5. 机器开机自启配置

### 方法一

在`/etc/init.d/rc.local`中添加要运行的脚本（不建议使用）

### 方法二

1. 新建一个脚本zentao.sh

```
#!/bin/bash
### BEGIN INIT INFO
# Provides:          codemonkeys
# Required-Start:    $local_fs $network
# Required-Stop:     $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: zentao service
# Description:       zentao service daemon
### END INIT INFO
/opt/zbox/zbox -ap 8090 -mp 3390 start
```

需要添加中间的一段注释内容，否则会在`sudo update-rc.d zentao.sh defaults 99`时提示

```
insserv: warning: script 'zentao.sh' missing LSB tags and overrides
```

或者一劳永逸（不推荐）

```
apt-get remove insserv
```

2. 设置脚本执行权限

```
sudo chmod 755 /etc/init.d/zentao.sh
```

3. 将脚本加入启动脚本中

```
sudo update-rc.d zentao.sh defaults 99
```

最后的数字为`脚本启动顺序号`，取值范围是0-99

4. 验证是否已成功添加

在上面脚本中配置的rc等级`Default-Start:     2 3 4 5`中的任意一个rc目录中查看是否增加了`zentao.sh`

```
cd /etc/rc2.d
ls
```

查看是否有`zentao.sh`后缀的脚本

5. 移出执行脚本方法

```
sudo update-rc.d -f zentao.sh remove
```

## 6. 停止和重启

```
/opt/zbox/zbox stop
/opt/zbox/zbox restart
```

## 7. 其他(查看命令)

```
/opt/zbox/zbox -h
```