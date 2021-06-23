安装服务端 Shadowsocks

三行命令，一次输入一行，完成之后输入下一行

```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```
最后一步输完，你应该会看到下图中内容──是要你选择需要安装的Shadowsocks版本。这里选择 4) shadowsocks-libev

1.选择完成后会让你输入密码，默认为一个随机密码，你可以回车选择默认，或者自定义一个密码后回车


2.密码设置完成后会让你输入一个端口号，默认随机一个，你也可以自定义一个端口号后回车生成



3.端口号设置完成后，选择一个加密方式，我一般采用 chacha20 来作为加密方式，手机上也是有这个模式的，比较方便，你也可以根据自己的喜好来选择不同的加密方式


4.设置完成后，会提示你是否需要安装 simple-obfs。这是 ss 的一个插件工具，可以起到混淆的作用，防止IP和端口被嗅探到，建议选 y 安装。


5.如果上一步选择了 y ，这里可以选择一种混淆方式。通常 TLS 要比 HTTP 的混淆效果更好，所以选 TLS


之后就耐心等待安装完成吧，安装完成后会给出你设置的密码、端口、服务器信息配置等，大功告成


查看是否开始运行

```
/etc/init.d/shadowsocks-libev status
```

修改相关配置

```
vim /etc/shadowsocks-libev/config.json
```

###安装客户端 Shadowsocks

###开启 BBR 加速

一键安装脚本

```
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh

```
完成后根据提示重启，如果没有则不需要，查看是否正在运行


```
uname -r
lsmod | grep bbr
```