<div class="lab-edi-doc">

# 搭建 Discuz 论坛

## 准备 LAMP 环境

> 任务时间：15min ~ 30min

LAMP 是 Linux、Apache、MySQL 和 PHP 的缩写，是 Discuz 论坛系统依赖的基础运行环境。我们先来准备 LAMP 环境

### 安装 MySQL

使用 `yum` 安装 MySQL：

```
yum install mysql-server -y

```

安装完成后，启动 MySQL 服务：

```
service mysqld restart

```

此实验使用 mysql 默认账户名和密码，您也可以设置自己的 MySQL 账户名和密码：<sup>[[?](#stage-1-step-1-password)]</sup>，参考下面的内容：

```
/usr/bin/mysqladmin -u root password 'Password'

```

将 MySQL 设置为开机自动启动：

```
chkconfig mysqld on

```

<a id="stage-1-step-1-password"></a>

> 下面命令中的密码是教程为您自动生成的，为了方便实验的进行，不建议使用其它密码。如果设置其它密码，请把密码记住，在后续的步骤会使用到。

### 安装 Apache 组件

使用 `yum` 安装 Apache 组件：

```
yum install httpd -y

```

安装之后，启动 httpd 进程：

```
service httpd start

```

把 httpd 也设置成开机自动启动：

```
chkconfig httpd on

```

### 安装 PHP

使用 `yum` 安装 PHP：<sup>[[?](#stage-1-step-3-php)]</sup>

```
yum install php php-fpm php-mysql -y

```

安装之后，启动 PHP-FPM 进程：

```
service php-fpm start

```

启动之后，可以使用下面的命令查看 PHP-FPM 进程监听哪个端口 <sup>[[?](#stage-1-step-3-port)]</sup>

```
netstat -nlpt | grep php-fpm

```

把 PHP-FPM 也设置成开机自动启动：

```
chkconfig php-fpm on

```

<a id="stage-1-step-3-php"></a>

> CentOS 6 默认已经安装了 PHP-FPM 及 PHP-MYSQL，下面命令执行的可能会提示已经安装。

<a id="stage-1-step-3-port"></a>

> PHP-FPM 默认监听 9000 端口

## 安装并配置 Discuz

> 任务时间：15min ~ 30min

### 安装 Discuz

CentOS 6 没有Discuz 的 `yum` 源，所以我们需要下载一个Discuz 压缩包：<sup>[[?](#stage-2-step-1-discuz)]</sup>

```
wget http://download.comsenz.com/DiscuzX/3.2/Discuz_X3.2_SC_UTF8.zip

```

下载完成后，解压这个压缩包

```
unzip Discuz_X3.2_SC_UTF8.zip

```

解压完后，就能在 _upload_ 文件夹里看到discuz的源码了

<a id="stage-2-step-1-discuz"></a>

> 到Discuz官网找一个安装包并复制安装包下载路径，这里我们用 Discuz_X3.2_SC_UTF8.zip

### 配置 Discuz

由于PHP默认访问 `/var/www/html/` 文件夹，所以我们需要把upload文件夹里的文件都复制到 `/var/www/html/` 文件夹

```
cp -r upload/* /var/www/html/

```

给 /var/www/html 目录及其子目录赋予权限

```
chmod -R 777 /var/www/html

```

重启 Apache

```
service httpd restart

```

## 准备域名和证书

> 任务时间：15min ~ 30min

### 域名注册

如果您还没有域名，可以[在腾讯云上选购](https://dnspod.qcloud.com/?fromSource=lab "null")，过程可以参考下面的视频。

*   _视频 - 在腾讯云上购买域名_

### 域名解析

域名购买完成后, 需要将域名解析到实验云主机上，实验云主机的 IP 为：

```
<您的 CVM IP 地址>

```

在腾讯云购买的域名，可以_到控制台添加解析记录_，过程可参考下面的视频：

*   _视频 - 如何在腾讯云上解析域名_

域名设置解析后需要过一段时间才会生效，通过 `ping` 命令检查域名是否生效 <sup>[[?](#stage-3-step-2-replace)]</sup>，如：

```
ping www.yourdomain.com

```

如果 ping 命令返回的信息中含有你设置的解析的 IP 地址，说明解析成功。

<a id="stage-3-step-2-replace"></a>

> 注意替换下面命令中的 `www.yourmpdomain.com` 为您自己的注册的域名

### 大功告成！

恭喜，您的 Discuz 论坛已经部署完成，您可以通过浏览器访问论坛查看效果。

通过IP地址查看：[http://<您的 CVM IP 地址>/install](http://<您的 CVM IP 地址>/install "null")

通过域名查看：[http://www.yourdomain.com/install](http://www.yourdomain.com/install "null")，其中替换 `www.yourdomain.com` 为之前申请的域名。

</div>