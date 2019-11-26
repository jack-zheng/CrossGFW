# CrossGFW

[谷歌云注册](https://cloud.google.com/?hl=zh-cn)，有个免费试用的选项，戳之

Run command to install bbr and restart VPS:

```bash
sudo -i

wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh

sudo reboot
```

Check if bbr installed

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

结果包含 bbr 字段则表示安装成功

```bash
net.ipv4.tcp_available_congestion_control = bbr cubic reno

or

net.ipv4.tcp_available_congestion_control = reno cubic bbr
```

安装 SS server

```bash
sudo -i

wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh

chmod +x shadowsocks-all.sh

./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

## 后记

如果你用的是其他版本的 shadowsocks 包，比如 go, python 之类的，上面那个就不 work 了， `ps -ef | grep shadow` 检查一下你用的什么版本

检测 shadowsocks 是否启动

```bash
systemctl status shadowsocks-libev
```

输入shadowsocks 密码, 输入端口号,其他一路回车(也可自行选择混淆 协议).安装完后会有红色提示出现, 复制内容用作后面设置的参考.

## 测试实例速度

[ipip测速网址](https://www.ipip.net/)
实用工具 -> TraceRoute -> IPv4 边上的选框选一个和你近的站点， 另一边选你生成的IP，测之。ping 太高删掉重建！

## 设置防火墙

谷歌云 -> VPC网络 -> 防火墙规则 -> default-allow-https/http -> 协议和端口 选择 '允许全部'

到此服务器端设置完结，到移动PC端下载小飞机设置直接翻墙

## PC 端翻墙工具

[PC 端工具链接](https://github.com/shadowsocksrr/shadowsocksr-csharp/releases) , 安卓的apk也有，666

## Reference

* [allenking1028](https://github.com/allenking1028/ss/issues/1)
* [teddy Github](https://teddysun.com/489.html)
