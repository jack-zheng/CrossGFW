## 前述

2022年末的时候，在谷歌云买的VPS接连嗝屁，估计是我之前用的都是裸奔的SS服务，加上谷歌云的IP已经被监管了，所以就算新建一个VPS也活不过10分钟，一次访问，没多久就被强了。无奈只能再学习一下翻墙技术，改善一下翻墙姿势，发现油管博主叫[不良林](https://www.youtube.com/watch?v=s90feRmdr9A)的，在教你搭梯子的时候还科普了很多计算机网络的知识，可以跟着搞一下，顺便学学新知识，不错。

翻车打卡:
- 2023-04-02, 443 又被封了，感觉得再找找办法了
- 2023-03-09, 443 端口被封了，难道真就是两会的原因？
- 2023-03-01, 443 端口被封了，重新再搭建一个试试

## 购买VPS + 设置

1. 使用原来的主机做样本，点击复制即可创建新的 VPS，这里用的 Ubantu 18
1. 谷歌的VPS想要通过SSH访问需要额外的设置
1. 通过 UI 界面打开 SSH 先
1. `sudo -i` 获取管理员权限
1. `vim /etc/ssh/sshd_config` 修改 ssh 配置文件
1. 将 PermitRootLogin 前面的注释去掉并将值改为 yes, 同理修改 PasswordAuthentication 值为 yes, 保存退出。这里的退出按 esc 没用，得按 ctl + c
1. `service sshd restart` 重启 ssh 服务
1. `passwd` 重制密码，到此设置 ssh 开启完成

## 购买域名

访问[namesilo](https://www.namesilo.com/)购买域名用于伪装，博主有给出优惠码，大概一年10块人名币左右，还可以

1. 注册一个账号
1. 搜索你想要的域名，挑一个最便宜的即可
1. 加入购物车之后购买，买的时候可以输入博主的优惠码，有效期一年
1. 点击购物车 -> 点击页面顶部 Manage My Domains 进入管理界面，点击地球标识，进入 Manager DNS 界面
1. 删除多余 DNS 配置，只保留一条即可，点击修改，在 hostname 中填入 `*` 表示泛解析，在 ipv4 address 中填入之前创建的 VPS ip, submit 保存
1. 点击 A，新增一条记录，hostname 不填，在 ipv4 address 中填入之前创建的 VPS ip, submit 保存
1. 域名操作完成，域名解析生效需要 15-30 mins

step 4 pic:
![20230301201848](https://obsidian-pic-bucket-01.oss-cn-shanghai.aliyuncs.com/obsidian_pic/20230301201848.png)
step 5 pic:
![20230301202119](https://obsidian-pic-bucket-01.oss-cn-shanghai.aliyuncs.com/obsidian_pic/20230301202119.png)

## FinalShell 设置

1. 到官网下载软件并安装
1. 打开软件，点击文件夹，在弹出窗口点击第一个新建按钮，添加 SSH 连接信息
1. 主机为 VPS ip，用户名为 root 其他按实际情况填写即可
1. 回到主页面，双击连接

他这个工具还会实时显示VPS状态，挺好的。在本地和 VPS 中 ping 一下刚才申请的域名，如果 ip 和你的 VPS 一致，表示已经生效了

## 节点搭建

先 `apt update` 更新软件源

### 拥塞控制

启用 bbr 拥塞控制算法，参考[这篇文章](https://github.com/iMeiji/shadowsocks_install/wiki/%E5%BC%80%E5%90%AF-TCP-BBR-%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6%E7%AE%97%E6%B3%95)，GCP 上我创建的镜象符合要求，不需要额外操作。

开机后 `uname -r` 看看是不是内核 >= 4.9。

执行 `lsmod | grep bbr`，如果结果中没有 tcp_bbr 的话就先执行：

```
sudo modprobe tcp_bbr
echo "tcp_bbr" | sudo tee --append /etc/modules-load.d/modules.conf
```
执行

```
echo "net.core.default_qdisc=fq" | sudo tee --append /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee --append /etc/sysctl.conf
```

配置使能 `sudo sysctl -p`，再执行检测配置是否成功

```
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
```

如果结果都有 bbr，则证明你的内核已开启 BBR。

执行 `lsmod | grep bbr`，看到有 tcp_bbr 模块即说明 BBR 已启动。

### x-ui 安装 + nginx 安装

安装 x-ui `bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)`。按照提示，填写用户名密码，端口等信息。

安装 nginx `apt install nginx`, 遇到提问回车即可

安装 acme `curl https://get.acme.sh | sh`

添加软连接 `ln -s /root/.acme.sh/acme.sh /usr/local/bin/acme.sh`

切换CA机构 `acme.sh --set-default-ca --server letsencrypt`

申请证书 `acme.sh --issue -d 申请的域名(e.g. xxx.com) -k ec-256 --webroot /var/www/html`

安装证书，这里 `-d` 后面域名和上面的要一致

```
acme.sh --install-cert -d 申请的域名(e.g. xxx.com) --ecc \
--key-file       /etc/x-ui/server.key  \
--fullchain-file /etc/x-ui/server.crt \
--reloadcmd     "systemctl force-reload nginx"
```

寻找伪装网站，在 Google 里搜索 'login Cloudreve' 找一个登录页面即可

域名+端口号登陆 xui，选择最新版内核。点击 '入站列表' 填入信息

* 备注 - 随意
* 协议 - vmess
* 监听IP：127.0.0.1
* 端口：10000
* 传输：ws
* 路径：复制 id 即可

点击 '面板设置'

* 监听ip:  127.0.0.1
* 监听端口：不变
* url 根路径：/id-xui/, e.g. /af674cf5-800a-4a4a-e9b0-xxxxxxxx-xui/
* 点击'保存配置'，再点击'重启面板'

FinalShell 中进入 '/etc/nginx' 目录，修改 nginx 配置

```conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 1024;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    gzip on;

    server {
        listen 443 ssl;
        
        server_name mysite.com;  #你的域名
        ssl_certificate       /etc/x-ui/server.crt;  #证书位置
        ssl_certificate_key   /etc/x-ui/server.key; #私钥位置
        
        ssl_session_timeout 1d;
        ssl_session_cache shared:MozSSL:10m;
        ssl_session_tickets off;
        ssl_protocols    TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers off;

        location / {
            proxy_pass https://www.rayvioli.xyz;  #伪装网址
            proxy_redirect off;
            proxy_ssl_server_name on;
            sub_filter_once off;
            sub_filter "www.rayvioli.xyz" $server_name; #伪装网址
            proxy_set_header Host "www.rayvioli.xyz";   #伪装网址
            proxy_set_header Referer $http_referer;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header User-Agent $http_user_agent;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
            proxy_set_header Accept-Encoding "";
            proxy_set_header Accept-Language "zh-CN";
        }


        location /af674cf5-800a-4a4a-e9b0-xxxxxxx {   #分流路径
            proxy_redirect off;
            proxy_pass http://127.0.0.1:10000;        #Xray端口
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
        
        location /af674cf5-800a-4a4a-e9b0-xxxxxxxx-xui {   #xui路径
            proxy_redirect off;
            proxy_pass http://127.0.0.1:9999;              #xui监听端口
            proxy_http_version 1.1;
            proxy_set_header Host $host;
        }
    }

    server {
        listen 80;
        location /.well-known/ {
               root /var/www/html;
            }
        location / {
                rewrite ^(.*)$ https://$host$1 permanent;
            }
    }
}
```

终端输入 `systemctl reload nginx` 重新启动 nginx. 通过 域名+xui路径重新访问管理界面 e.g. xxx.lol/af674cf5-800a-4a4a-e9b0-xxxxxxxx-xui

最后通过上面的链接重新登陆 x-ui, 复制链接或者直接扫描二维码配置客户端。修改的点如下：

1. 地址改为域名
2. 端口改为 443
3. 加密方式改为 zero
4. 传输层安全改为 tls

## nginx.conf 默认配置如下

```conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
# 
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}

```
