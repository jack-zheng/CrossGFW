# CrossGFW
[谷歌云注册](https://cloud.google.com/?hl=zh-cn)，有个免费试用的选项，戳之

Guid From: [allenking1028](https://github.com/allenking1028/ss/issues/1)

1-4是安装BBR 加速器部分, 不设置速度也很快，可以不跑。直接从第5步开始

1. `sudo -i`

(最前面显示root@xxxx)

1. `wget -N --no-check-certificate https://raw.githubusercontent.com/FunctionClub/YankeeBBR/master/bbr.sh && bash bbr.sh install`

蓝底窗口按TAB键选NO

选择重启 Y

这里会断开连接，大家可以关掉窗口再重新打开或几秒钟后在界面随便按几个字母 便会提示重新连接。

1. `sudo -i`

(最前面显示root@xxxx)

1. `bash bbr.sh start`

1. type command as below:

  + `wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh`
  + `chmod +x shadowsocks-all.sh`
  + `./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log`
  + `./shadowsocks-all.sh`

输入shadowsocks 密码, 输入端口号,其他一路回车(也可自行选择混淆 协议).安装完后会有红色提示出现, 复制内容用作后面设置的参考.

## 测试实例速度
[ipip测速网址](https://www.ipip.net/)
实用工具 -> TraceRoute -> IPv4 边上的选框选一个和你近的站点， 另一边选你生成的IP，测之。ping 太高删掉重建！

## 设置防火墙
谷歌云 -> VPC网络 -> 防火墙规则 -> default-allow-https/http -> 协议和端口 选择 '允许全部' 

到此服务器端设置完结，到移动PC端下载小飞机设置直接翻墙

## PC 端翻墙工具
https://github.com/shadowsocksrr/shadowsocksr-csharp/releases , 安卓的apk也有，666
