# Centos下Yum安装PHP5.5,5.6,7.0

## 1.检查当前安装的PHP包
```
 yum list installed | grep php
```
如果有安装的PHP包，先删除他们

```xshell
yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-gd.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mcrypt.x86_64 php-mysql.x86_64 php-pdo.x86_64

yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-gd.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mcrypt.x86_64 php-mysql.x86_64 php-pdo.x86_64
```

## 2.修改Centos 5.X源
```
rpm -Uvh http://mirror.webtatic.com/yum/el5/latest.rpm
CentOs 6.x
rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
CentOs 7.X
rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

如果想删除上面安装的包，重新安装
```
rpm -qa | grep webstatic
rpm -e  上面搜索到的包即可
```
## 3.运行yum install

```xshell
yum install php55w.x86_64 php55w-cli.x86_64 php55w-common.x86_64 php55w-gd.x86_64 php55w-ldap.x86_64 php55w-mbstring.x86_64 php55w-mcrypt.x86_64 php55w-mysql.x86_64 php55w-pdo.x86_64 php55w-xml.x86_64

yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mcrypt.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64 php56w-xml.x86_64

```

```xshell
yum install php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64 php70w-mysql.x86_64 php70w-pdo.x86_64 php70w-xml.x86_64
```

## 4.安装PHP FPM
```xshell
yum install php55w-fpm
yum install php56w-fpm
yum install php70w-fpm
```

## 5.配置nginx 重启php-fpm
```xshell
/etc/init.d/php-fpm start
```

查看php-fpm错误日志
```xshell
cd /var/log/php-fpm/

tail -f 日志名

```
