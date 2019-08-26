# git下载和安装
&emsp;&emsp;从git官网下载git：``` https://git-scm.com ```。安装双击exe文件，如果下的是安装版的，就直接装在系统里了，下的压缩包的以```7z.exe```结尾的，双击完会解压出一个文件夹，双击```git-bash.exe```就可以打开git了。 

--------
# 创建本地仓库
&emsp;&emsp;首先新建一个空文件夹```mkdir ./learn```，```./```表示当前目录，也可以使用绝对路径```/home/xx/tmp```或者Windows下为```/c/tmp```，如果不在C盘可以把```/c```改成```/d```或者电脑上其他可用的盘号。 

&emsp;&emsp;创建完文件夹后进入文件夹，```cd /c/tmp/learn```。接下来初始化版本库，使用命令```git init```。此时文件夹下会自动生成一个```.git```的隐藏文件夹，这个目录是git用来跟踪管理版本库的，里面的文件一般不动。 

### 添加文件到版本库
&emsp;&emsp;随便是用什么办法创建文件```readme.md```，可以电脑上直接新建文件夹或&emsp;&emsp;使用命令```touch readme.md```新建文件，当然直接使用vim编辑然后保存也可以。  
&emsp;&emsp;在文件里输入说明，如果连接github，那这个文件就是仓库的说明。 
&emsp;&emsp;把文件添加到仓库使用命令：```git add readme.md``` 
&emsp;&emsp;然后把修改提交到仓库：```git commit -m '本次提交说明'``` 

### 添加远程仓库以github为例
&emsp;&emsp;第一步创建本地SSH Key，首先看看本地目录下有没有```.ssh```文件夹，文件夹里有没有```id_ras id_rsa.pub```文件或者其他秘钥文件，如果有直接跳到第二步，没有就需要创建，创建命令: 
``` ssh-keygen -t rsa -C "youremail@example.com"```

&emsp;&emsp;一路确定选择默认设置或者在第一个需要输入的时候选择创建```id_rsa```文件的位置，默认在```/c/Users/用户名/.ssh/id_rsa```，更改到自己想要的位置，之后设置密码什么的为空就好了。但更改秘钥位置会引发一些问题，之后在写。现在找到```id_rsa```文件，使用```cat ./.ssh/id_rsa.pub```查看或记事本什么的查看。一般以```ssh-rsa```开头，以之前输入的```youreamil@example.com```结尾，复制留着有用。 

&emsp;&emsp;第二步登陆github，创建SSH Key秘钥。点击头像下拉菜单，找到settings，进入后在左侧菜单栏里找到```SSH and GPG keys```选项，点进去，点击右上角```New SSH key```按钮，在title里填写ssh秘钥名称，这个随便写，方便知道是那台设备就行。下面的key栏里需要填写之前复制的公钥，点击```add SSH key```就完成秘钥的保存。 

&emsp;&emsp;点击```+▼```按钮，找到```new repository```，输入Repository name，其他设置保持默认，然后点击```create repository```创建仓库。 

&emsp;&emsp;创建完成后是一个空仓库，github会有引导页面，根据Github的提示，我们需要在本地仓库里运行以下命令：
``` git remote add origin git@github.com:Icecarry/learnsome.git ``` ,```git@github.com:Icecarry/learnsome.git```这是我的仓库，你的仓库，直接复制引导页面上的即可。添加完，远程仓库的名字就是```origin```了，这是git默认的叫法，可以改成别的。

&emsp;&emsp;接着我们就可以把本地内动推送到远程库上：
```git push -u origin master```
这实际上是把本地```master```分支推送到远程。由于文件夹是空的，加上```-u```参数会使本地```master```分支和远程```master```分支关联起来，在之后推送就可以简化命令直接使用```git push orign master```了。

&emsp;&emsp;第一次使用```push```命令连接github时会有一个警告，输入```yes```回车就可以了。Git会出一个警告，告诉你已经把guthub的key添加到本机的一个信任列表里了：
```Warning: Permanently added 'github.com' (RSA) to the list of known hosts.```

&emsp;&emsp;警告只出现一回，之后不会再出现。

### 可能会出现的问题
1. ```git pull或push```报```Permission denied (publickey)```，```Access denied```错，检查```git config --list```配置是否出错，使用
```git config --global user.name "youruser" //修改用户名```
```git config --global user.email "youremail" //修改你的邮箱```
，然后检查```.ssh```文件夹下的```id_rsa&id_rsa.pub```是否正确，还报错，就进入仓库文件夹下的```.git```隐藏文件夹，编辑config文件```vim config```，查看```["remote "origin"]```下的```url```是否正确。
2.  之前更改的id_rsa保存位置会导致git找不到文件，这时，就需要手动添加秘钥的位置了。使用命令```ssh-add /c/Users/用户名/.ssh/id_rsa```，如果不报错就添加成功，如果出现```Could not open a connection to your authentication agent```错误，需要先执行下面的命令```ssh-agent bash```。
如果还是报```Permission denied (publickey)```，可能需要配置config文件，在```.ssh```文件夹下查看config文件，里面的内容：
```Host git
    HostName github.com
    user xxx(这里是用户名)
    IdentityFile ~/.ssh/id_rsa
```
&emsp;&emsp;这里主要看```IdentityFile```这一栏，如果是上面这样的，而你又重新指定```id_rsa```存放位置的，改成你存放的路径。

&emsp;&emsp;使用命令测试是否连通：
```
ssh -T git@github.com
# 如果成功输出下面内容
Hi jarvan4dev! You've successfully authenticated, but GitHub does not provide shell access.
# 如果未连通
Permission denied (publickey).
# 查看当前使用的key
ssh-add -l
# 如果当前没有key，则：The agent has no identities. 此时需要添加key
ssh-add ~/.ssh/id_rsa
# ssh-add 这个命令不是用来永久性的记住你所使用的私钥的。实际上，它的作用只是把你指定的私钥添加到 ssh-agent 所管理的一个session 当中。而 ssh-agent 是一个用于存储私钥的临时性的 session 服务，也就是说当你重启之后，ssh-agent服务也就重置了。
可以使用 :
ssh-add -K ~/.ssh/id_rsa
输出如下：
Passphrase stored in keychain: /Users/用户名/.ssh/id_rsa
Identity added: /Users/用户名/.ssh/id_rsa(/Users/用户名/.ssh/id_rsa)

```

--------
# Linux基础用户管理
使用，```useradd```或```adduser```添加用户，```adduser name1```可以更方便的添加用户，现在的系统一般输入之后会让你输入密码确保安全。如果使用```useradd name1```那么只会创建这样一个用户名，它在```/home```目录下没有自己的文件夹，也没有密码。不过使用```useradd```可以跟参数自己指定想要的东西，参数有：
```
-c<备注>：加上备注文字。备注文字会保存在passwd的备注栏位中； 
-d<登入目录>：指定用户登入时的启始目录； 
-D：变更预设值； 
-e<有效期限>：指定帐号的有效期限； 
-f<缓冲天数>：指定在密码过期后多少天即关闭该帐号； 
-g<群组>：指定用户所属的群组； 
-G<群组>：指定用户所属的附加群组； 
-m：自动建立用户的登入目录； 
-M：不要自动建立用户的登入目录； 
-n：取消建立以用户名称为名的群组； 
-r：建立系统帐号； 
-s：指定用户登入后所使用的shell； 
-u：指定用户id。
```
例如：
```
useradd web -g admin -n -m  #新建web用户并指定用户组为 admin用户组,并自动建立登录目录
passwd web                  #给web 用户设置密码
useradd(选项)(参数)

                #创建admin用户，并指定属组为admin 用户id为10000
useradd -r -m -s  /bin/bash -g admin -u 10000 admin 
```
### 删除用户
```
userdel web			 # 删除用户
groupdel web		 # 删除用户组
usermod –G web web   #（强制删除该用户的主目录和主目录下的所有文件和子目录）
```


# vultr的VPS修改root账户密码
### Ubuntu&Debian
&emsp;&emsp;在服务器管理界面点击```view Console```按钮进入管理界面，点击管理界面上的```send CtrlAltDel```按钮，在服务器重启是按```Esc```键进入```GRUN boot prompt```的选择界面。将光标移动到```Ubuntu```上，按```e```键进入编辑模式，找到```linux      /boot/...```那一行，在最后加上```init = "/bin/bash"```然后按```Ctrl+x或者F10```保存重启。
&emsp;&emsp;进入命令界面后（有可能历史太多命令行看不见，多按几下Enter就好了）输入```mount -rw -o remount /```然后输入```passwd```接着输入新密码，确认新密码就完成root密码的修改了。
### Centos6
&emsp;&emsp;开始步骤差不多，点击```send CtrlAltDel```之后会出现引导界面，提示按任意键继续，停留时间很短，需要及时按键。
&emsp;&emsp;进入```grub```启动菜单后，按```a```键进入grub命令行，在命令行里输入``` 空格+dingle```再回车，vps重启。
&emsp;&emsp;出现```#```提示符，输入```passwd root```，然后根据提示输入新```root```密码。
### Centos7
&emsp;&emsp;前面几步和Ubuntu的一样，按```e```键进入编辑第一启动项，找到```linux16```这一行，将```ro```改成```rw init=/sysroot/bin/sh```，好了之后按```Ctrl+x```，输入```chroot /sysroot```回车，最后输入```passwd```修改root密码，修改完成后重启```reboot -f```。




ps 参考资料：  
1. [廖雪峰git教程](https://www.liaoxuefeng.com/wiki/896043488029600)
2. [git ssh-add 问题解决办法参考](https://www.cnblogs.com/sheldonxu/archive/2012/09/17/2688281.html)
3. [git多用户管理](https://www.jianshu.com/p/8e0d54ee3d20)
4. [git报错](https://blog.csdn.net/belalds/article/details/80485160)
5. [linux用户管理(Ubuntu centos)](https://www.jianshu.com/p/6bc98fd1c4a5)