## Linux下安装Nginx

1.安装gcc gcc是用来编译下载下来的nginx源码

```shell
 # yum -y install gcc-c++
```

2、安装pcre和pcre-devel 

    PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。

nginx 的 http 模块使用 pcre 来解析正则表达式，pcre-devel 是使用 pcre 开发的一个二次开发库。

```shell
# yum install -y pcre pcre-devel
```

3、安装zlib zlib提供了很多压缩和解方式，nginx需要zlib对http进行gzip

```  shell
# yum install -y zlib zlib-devel
```

4、安装openssl openssl是一个安全套接字层密码库，nginx要支持https，需要使用openssl

  yum install -y openssl openssl-devel

 

5. 下载nginx 

```shell
# wget http://nginx.org/download/nginx-1.14.0.tar.gz
```

6.解压

```shell
# tar -zxvf nginx-1.14.0.tar.gz
```

7. cd到文件路劲 

8.编译

```shell
# ./configure --prefix=/usr --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --pid-path=/var/run/nginx/nginx.pid --lock-path=/var/lock/nginx.lock --user=nginx --group=nginx --with-http_ssl_module --with-http_flv_module --with-http_gzip_static_module --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/tem/nginx/client --http-proxy-temp-path=/var/tem/nginx/proxy --http-fastcgi-temp-path=/var/tem/nginx/fcgi --with-http_stub_status_module
```



 

9.安装

```shell
# make && make install
```

10.启动

```shell
# nginx -c /etc/nginx/nginx.conf 
```

11. 如果出现[emerg] getpwnam("nginx") failed 错误 执行

```shell
# useradd -s /sbin/nologin -M nginx
# id nginx

```

12.如果出现 [emerg] mkdir() "/var/temp/nginx/client" failed (2: No such file or directory) 错误 执行

```shell
# sudo mkdir -p /var/tem/nginx/client
```

13.如果您正在运行防火墙，请运行以下命令以允许HTTP和HTTPS通信：

```shell
# sudo firewall-cmd --permanent --zone=public --add-service=http 

# sudo firewall-cmd --permanent --zone=public --add-service=https

# sudo firewall-cmd --reload
```