# 服务器
1. 首先有一台服务器，服务器选择看自己，目前没钱，只能上vultr，利用xshell或类似ssh远程连接软件来连接自己的服务器。  
2. 连接上之后查看是否已经启用了Linux内核数据转发，尤其是Ubuntu系统，Ubuntu18.04在vultr上如果未启用就安装ssr之类的会出现无法连接的问题。  
3. 启用Linux内核数据转发:  
```sudo sysctl net.ipv4.ip_forward```  
如果输出为 ```net.ipv4.ip_forward = 0``` 表示未启用， 输出 ```1``` 表示已启用。  
在 ```/etc/sysctl.conf``` 文件底部添加一行
```net.ipv4.ip_forward = 1```  
用 ```sysctl -p``` 命令出发新的内核配置，使它生效。  

# SSR安装
登录系统后使用一件安装脚本安装，安装脚本有系统要求，安装之前确定系统以及系统版本号。
查看系统版本号命令：  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```uname -r``` 

执行下面的命令：  
```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh  
chmod +x shadowsocks-all.sh  
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```
按照提示选择需要安装的版本， 选择ShadowsocksR  
然后输入密码，接着设置端口，再后面的就是ssr的配置，选择加密方式，协议，混淆方式，选择完会提示按任意键继续。  
等待它自己安装结束，安装成功会看到直接的配置信息，之后就可以去配置pc端或手机端的ssr了。  
每个地方对不同协议的适用都不一样，多试试不同协议混淆搭配总会连通的。  

### 卸载方法：  
使用root用户登录，找到脚本下载的位置，在脚本所在的文件夹运行以下命令：
```
./shadowsocks-all.sh uninstall
```

### 启动脚本：
```
/etc/init.d/shadowsocks-r start | stop | restart | status
```
后面参数的含义分别为 启动，停止，重启，状态。

### 配置文件位置
```
/etc/shadowsocks-r/config.json
```

--------
# V2Ray
安装使用脚本安装，使用以下命令安装：  
```
bash <(curl -s -L https://git.io/v2ray.sh)
```
如果提示 curl：command not found，需要安装curl。  
Ubuntu/debian 安装方法： apt-get update -y && apt-get install curl -y  
centos 安装方法：yum update -y && yum install curl -y  
安装成功后就能安装脚本了。

安装步骤和ssr差不多，先选择安装，然后选择v2ray传输协议，协议这些可以去官网看看。
选择完协议选择端口，是否开启广告拦截，是否配置Shadowsocks，如果选择了配置Shadowsocks就要又配置一遍ss，配置完就会提示enter键继续，然后还会让你确认一遍配置，无视直接enter。  
我安装就选了v2ray的协议端口，其他都默认的过去的。  
安装完成就会弹出完成的配置信息，之后就去pc端和手机端配置就好了。  
### v2ray管理面板
直接在终端输入 v2ray 就可以，然后会弹出很多选项，找到自己想要的选项进去修改就好了。

--------
# anyconnect(ocserv.sh)
脚本安装，安装命令如下：  
```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/ocserv.sh && chmod +x ocserv.sh && bash ocserv.sh
```
选择```1```就会开始安装。  
安装结束后会让你开始配置账号，先是用户名，然后是密码，后面是tcp和udp端口，设置完后它会自动配置并展示结果。  
安装完成后运行```./ocserv.sh```可以管理脚本。  
除了运行脚本管理还可以用下面的命令查看ocserv。  
```
/etc/init.d/ocserv start
# 启动ocserv
/etc/init.d/ocserv stop
# 停止ocserv
/etc/init.d/ocserv restart
# 重启ocserv
/etc/init.d/ocserv status
# 查看ocserv的运行状态
/etc/init.d/ocserv log
# 查看ocserv运行日志
/etc/init.d/ocserv test
# 测试ocserv配置文件是否正确
```
配置文件： ```/etc/ocserv/ocserv.conf ```  
账号配置文件： ```/etc/ocserv/ocpasswd ```  
日志文件： ```/tmp/ocserv.log ```  

--------
# wireguard
## 安装
安装可以通过官网找到安装方法，以ubuntu为例：  
```
sudo add-apt-repository ppa:wireguard/wireguard
sudo apt-get update
sudo apt-get install wireguard
```
## 生成密钥对  
进入配置目录 ```cd /etc/wireguard ```,然后执行下面的命令。  
```
umask 077
wg genkey | tee privatekey | wg genkey > publickey
```
打印公钥私钥：  
``` 
cat privatekey
cat publieckey
```

## 编辑wg0配置文件wg0.conf  
```
sudo vim wg0.conf
```
wg0.conf配置文件内容：  
```
[Interface]
Address = 10.0.0.1/24   
# VPN server自己的地址，可以改成任意的内网地址
Address = fd86:ea04:1115::1/64   
# VPN server自己的ipv6的地址（可选）
SaveConfig = true   
# 设为true之后，每次重启服务（stop service时）都会自动保存config
 
# 以下是重点: 当服务启动时，通过iptables配置wg0来的流量forward到ens3
# 如果你的device不是ens3而是别的名字，把下面的ens3改成别的。有可能为eth0。可以使用ifconfig查看。
# 当服务停止的时候，删除相关的iptables规则
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE; ip6tables -A FORWARD -i wg0 -j ACCEPT; ip6tables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE; ip6tables -D FORWARD -i wg0 -j ACCEPT; ip6tables -t nat -D POSTROUTING -o ens3 -j MASQUERADE
 
ListenPort = 10443   # 随便选一个空闲的端口
PrivateKey = <private-key>   #之前生成的privatekey的内容
```

## 启动服务
### 手动开启关闭wg0网络
```
wg-quick up wg0
wg-quick down wg0
```
### 或者添加开机启动
```systemctl enable wg-quick@wg0 ```

如果有以下wg0启动报错，使用下面的解决办法。  
错误1：
``` /usr/bin/wg-quick: line 31: resolvconf: command not found ```
解决办法：
``` sudo apt install resolvconf ```  
错误2：
``` Warning: `/etc/wireguard/wg0.conf' is world accessible ```
解决办法：
``` sudo chmod 600 /etc/wireguard/wg0.conf ```

启动完后使用```wg```命令查看是否启动成功以及状态。

## 添加客户端
首先在客户端生成密钥对，复制客户端的公钥，使用以下命令在服务器添加客户端。
``` 
# client-public-key 填写客户端生成的公钥(publickey)
sudo wg set wg0 peer <client-public-key> allowed-ips 10.0.0.2/24 
```




### ps:
引用安装教程链接：  
1. [四合一安装脚本](https://teddysun.com/486.html)
2. [可能引用四合一脚本的另外一个脚本](https://segmentfault.com/a/1190000017952445)
3. [v2ray脚本安装](https://ppig.xyz/post/2/)
4. [v2ray官网](https://www.v2ray.com/)
5. [anyconnect脚本安装](https://doubibackup.com/nr2hjmg2.html)
6. [wireguard官网](https://www.wireguard.com)
7. [wireguard参考1在Ubuntu部署vpn隧道](https://steemit.com/cn/@curl/ubuntu-vpn-wireguard)
8. [wireguard参考2自己搭建wireguard给Android](https://mine260309.me/archives/1697)
9. [wireguard参考3wg0启动错误解决办法](https://www.cnblogs.com/redmondfan/p/10304322.html)

