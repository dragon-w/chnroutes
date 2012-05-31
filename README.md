# chnroutes

利用来自[APNIC](http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest)的数据生成路由命令脚本，让VPN客户端在连接时自动执行。通过这些路由脚本，可以让用户在使用VPN作为默认网关时，不使用VPN访问中国国内IP，从而减轻VPN负担，并提高访问国内网站的速度。

## 基本约定

在使用这些脚本之前，请确保你在自己的电脑上已经成功配置好一个VPN（PPTP或OpenVPN），并且让之以默认网关的方式运行（通常是默认配置），即VPN连接之后所有网络流量都通过VPN。

### 注意事项

* 因为这些IP数据不是固定不变的，建议每隔一个月更新一次；
* 使用此法之后，可能导致Google Music等服务无法访问，因为连上VPN之后，使用的DNS也是国外的，因此google.cn解析出的是国外的IP。

## OpenVPN

### `iproute2`

1. 执行`python chnroutes.py`，这将生成`vpn-up.sh`和`vpn-down.sh`两个文件；
2. 将上述两个文件移入`/etc/openvpn/`中，并加上执行权限；
3. 在OpenVPN配置文件中加入：
    script-security 2
    up vpn-up.sh
    down vpn-down.sh
4. 重新连接VPN，观察日志测试。

## PPTP
### Mac OSX

1. 从终端进入下载目录，执行`python chnroutes.py -p mac`，执行完毕之后同一目录下将生成两个新文件`ip-up`和`ip-down`；
2. 把这两个文件复制到`/etc/ppp/`目录，并使用`sudo chmod a+x ip-up ip-down`命令把它们设置为可执行；
3. 设置完毕，重新连接VPN。

### Linux

1.从终端进入下载目录，执行`python chnroutes.py -p linux`，执行完毕之后同一目录下将生成两个新文件`ip-pre-up`和`ip-down`.
2. 把`ip-pre-up`拷贝到`/etc/ppp/`目录，`ip-down`拷贝到`/etc/ppp/ip-down.d/`目录；
3. 设置完毕，重新连接VPN。

### Windows

1. 从终端进入下载目录，执行`python chnroutes.py -p win`，执行之后会生成`vpnup.bat`和`vpndown.bat`两个文件。
2. 由于Windows上的PPTP不支持拨号脚本，所以也只能在进行拨号之前手动执行`vpnup.bat`文件以设置路由表。而在断开VPN之后，如果你觉得有必要，可以运行`vpndown.bat`把这些路由信息给清理掉.

## 基于Linux的第三方系统的路由器

一些基于Linux系统的第三方路由器系统如: OpenWRT，DD-WRT，Tomato 都带有VPN(PPTP/Openvpn)客户端的， 也就是说，我们只需要在路由器进行VPN拨号，并利用本项目提供的路由表脚本就可以把VPN针对性翻墙扩展到整个局域网。当然，使用这个方式也是会带来副作用，即局域网的任何机器都不适合使用Emule或者BT等P2P下载软件。但对于那些不使用P2P，希望在路由器上设置针对性翻墙的用户，这方法十分有用，因为只需要一个VPN帐号，局域网内的所有机器，包括使用wifi的手机都能自动翻墙。相信配置方式请参考[Autoddvpn](http://code.google.com/p/autoddvpn/)项目。

# 信息反馈

本项目的脚本都是在使用路由器进行拨号的情况下测试通过的，如果在其它拨号方式下，脚本不能运作，请添加新的Issue。另外，在配合OpenVPN使用的时候，可能会出现因为网络质量不好，OpenVPN非主动断开，这时候`vpn-down.sh`脚本也会被自动调用，但重新连上之后，可能会找不到默认路由而添加失败，这时候你可以通过停止OpenVPN，并手动设置好原来的默认路由再重新进行OpenVPN拨号。
