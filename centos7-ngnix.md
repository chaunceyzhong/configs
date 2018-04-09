
### linux version : CENTOS 7
### ngnix version : 1.2




## 一、检查安装依赖环境
1. gcc:nginx编译依赖gcc环境.

　　　安装命令：`yum install gcc-c++`

2. pcre:(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式.

　　　安装命令：`yum install -y pcre pcre-devel`

3. zlib：该库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip.

　　　安装命令：`yum install -y zlib zlib-devel`

4. openssl:一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。nginx不仅支持http协议，还支持https（即在ssl协议上传输http）.

　　　安装命令：`yum install -y openssl openssl-devel`
   
## 二、下载nginx源码包并配置编译路径
1. 下载源码包.
      命令:`wget http://nginx.org/download/nginx-1.12.0.tar.gz`
2. 解压源码包.
      命令: `tar -zxvf nginx-1.12.0.tar.gz`
3. 配置编译参数.
      进入解压后文件夹 `cd nginx-1.12.0`
  `
 ./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
`

注：安装之前需要手动创建上面指定的nginx文件夹，即/var/temp、/var/temp/nginx、/var/run/nginx/文件夹，否则启动时报错

4. 编译安装
      命令 `make && make install`
      
      
## 启动nginx

1. 进入安装目录

　　　`cd /usr/local/nginx/sbin/`

2. 启动

　　　`./nginx`

3. 若报错：[emerg] open() "/var/run/nginx/nginx.pid" failed (2: No such file or directory)

　　　需要查看下是不是在/var/run文件夹下不存在nginx文件夹，不存在则新建

4. 查看是否启动：`ps -ef | grep nginx`

　　　如果有master和worker两个进程证明启动成功　　

　　注意：执行./nginx启动nginx，这里可以-c指定加载的nginx配置文件，如下：

　　　`./nginx -c /usr/local/nginx/conf/nginx.conf`

　　　如果不指定-c，nginx在启动时默认加载conf/nginx.conf文件，此文件的地址也可以在编译安装nginx时指定./configure的参数(--conf-path= 指向配置文件（nginx.conf）)

5. 停止

　5.1. 暴利kill(不推荐使用)

　　　`kill -9 processId`
   
　5.2. 快速停止

　　　`cd /usr/local/nginx/sbin && ./nginx -s stop`

　　　此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程

　5.3 完整停止(建议使用)

　　　`cd /usr/local/nginx/sbin && ./nginx -s quit`

　　　此方式停止步骤是待nginx进程处理任务完毕进行停止

6. 重启及重新加载配置

　　6.1. 先停止再启动（建议使用）

　　　　`./nginx -s quit && ./nginx`

　　6.2. 重新加载配置文件
  
　　　　`./nginx -s reload`

7. 测试
　　nginx安装成功，启动nginx,即可通过ip地址来访问nginx
  
 ## 三、防火墙设置
 
 1. centos7 默认使用firewalld命令来设置防火墙,取代iptables.
 
      `systemctl stop firewalld.service`             #停止firewall
   
      `systemctl disable firewalld.service `       #禁止firewall开机启动
 
 2.开启端口
 
     `firewall-cmd --zone=public --add-port=80/tcp --permanent`
  
     命令含义：
   --zone #作用域
   --add-port=80/tcp #添加端口，格式为：端口/通讯协议
   --permanent #永久生效，没有此参数重启后失效

3. 重启防火墙

    `firewall-cmd --reload`
  
4. 其他命令

  参考 
  https://www.zhaokeli.com/Article/6321.html
  https://blog.csdn.net/achang21/article/details/52538049
  
  
  
  
