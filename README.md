# CrossGFW

[谷歌云注册](https://cloud.google.com/?hl=zh-cn)，有个免费试用的选项，戳之

安装BBR 加速器部分, 不设置速度也很快，可以不跑。直接从第5步开始

1. run command to install bbr and restart VPS:
    + `sudo -i`
    + `wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh`
1. reboot VPS to let bbr works, `sudo reboot`
1. `sudo -i` and `sysctl net.ipv4.tcp_available_congestion_control` to check if bbr installed
    + normally value should be `net.ipv4.tcp_available_congestion_control = bbr cubic reno` or `net.ipv4.tcp_available_congestion_control = reno cubic bbr`
1. type command as below:
    + `sudo -i`
    + `wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh`
    + `chmod +x shadowsocks-all.sh`
    + `./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log`

输入shadowsocks 密码, 输入端口号,其他一路回车(也可自行选择混淆 协议).安装完后会有红色提示出现, 复制内容用作后面设置的参考.

## 测试实例速度
[ipip测速网址](https://www.ipip.net/)
实用工具 -> TraceRoute -> IPv4 边上的选框选一个和你近的站点， 另一边选你生成的IP，测之。ping 太高删掉重建！

## 设置防火墙
谷歌云 -> VPC网络 -> 防火墙规则 -> default-allow-https/http -> 协议和端口 选择 '允许全部' 

到此服务器端设置完结，到移动PC端下载小飞机设置直接翻墙

## PC 端翻墙工具
https://github.com/shadowsocksrr/shadowsocksr-csharp/releases , 安卓的apk也有，666

## Reference

* [allenking1028](https://github.com/allenking1028/ss/issues/1)
* [teddy Github](https://teddysun.com/489.html)
