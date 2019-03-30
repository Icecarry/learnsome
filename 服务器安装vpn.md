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

### ps:
安装教程链接：  
1. [四合一安装脚本](https://segmentfault.com/a/1190000017952445)
2. [可能引用四合一脚本的另外一个脚本](https://segmentfault.com/a/1190000017952445)

