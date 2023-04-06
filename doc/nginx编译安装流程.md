

# 1.安装依赖包

https://blog.csdn.net/t8116189520/article/details/81909574

```
//一键安装上面四个依赖
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```
# 2.下载并解压安装包
```
//创建一个文件夹
cd /usr/local
mkdir nginx
cd nginx
//下载tar包
wget http://nginx.org/download/nginx-1.13.7.tar.gz
tar -xvf nginx-1.13.7.tar.gz
```
# 3.安装nginx

```
//进入nginx目录
cd /usr/local/nginx
//进入目录
cd nginx-1.13.7
//执行命令 考虑到后续安装ssl证书 添加两个模块
./configure --with-http_stub_status_module --with-http_ssl_module
//执行make命令
make
//执行make install命令
make install
```
# 4.启动nginx服务

```
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

/usr/local/nginx/sbin/nginx -s reload
/usr/local/nginx/sbin/nginx -s stop

# 添加链接
cd /usr/sbin
ln -s /usr/local/nginx/sbin/nginx ./nginx

nginx -s reload
```
# 5.配置nginx.conf


```
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

更改配置添加后端代理的内容：

```
 location ^~/api/ {
            proxy_pass http://localhost:8888/api/v1/;
        }
```

# 6. 默认文件路径

```
/usr/local/nginx/html
```

   vue run build 生成的文件拷贝至此

# 7. 开机启动设置

添加nginx.service
```
[Unit]
Description=nginx - high performance web server 
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss -lookup.target

[Service] 
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID 
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install] 
WantedBy=multi-user.target

```
将nginx.service移动到/usr/lib/systemd/system/目录下

```
cp ./nginx.service /usr/lib/systemd/system/
```
4、重启配置服务
```
systemctl daemon-reload
```
这时因为nginx在启动状态，最好重启主机
```
lsof -i:80
reboot
```
编辑
/etc/sysconfig/iptables
添加
```
-A INPUT -p tcp --dport 80 -j ACCEPT
```
重启防火墙
```
systemctl restart iptables
```

5、查看nginx服务状态
```
systemctl status nginx
```
