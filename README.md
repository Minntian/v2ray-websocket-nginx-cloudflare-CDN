# v2ray-websocket-nginx-cloudflare-CDN

一个v2ray+websocket+nginx+cloudflare CDN的简单教程,大概是目前最安全的科学上网方式吧

# 懒人福利
建议使用docker快速部署,而不是下面手动复制粘贴的办法

整了个简单的docker,两行命令跑一下即可,简单快速,适合快速部署,并且适应各种环境,详情见[这里](./DOCKER.md)


### 第一步整个机器
这个就不用多说了,买个就是了,不知道在哪买可以上[主机百科](https://www.zhujiwiki.com)随便挑一个
最好有一点点linux使用基础.建议使用xshell连接SSH,配合xftp直接本地编辑好文件然后传上去,命令行编辑文件感觉不太爽

### 第二步申请域名
可以买一个,也可以[在freenom免费申请一个](https://www.freenom.com),申请完成后应看到这样的图
![pic](./assets/freenom.png)


### 第三步申请cloudflare账号
申请的时候会给一串nameServer地址,进入freenom的管理后台填上去就行
![pic](./assets/nameserver.png)到这里就把DNS交给cloudflare管理了

### 第四步 安装配置v2ray
在ubuntu20测试通过
- 强烈建议新手使用vscode的remote SSH来编辑这些配置文件，用起来比vim简单得多,出问题的概率大大降低，[vscode 远程ssh教程](https://www.jianshu.com/p/0f2fb935a9a1)
- `apt install curl socat -y`
- `bash <(curl -L -s https://install.direct/go.sh)`   安装v2ray
- 生成证书
    - 注意证书需要占用80端口去申请,确保申请的时候未被占用，把nginx，apache之类的先关掉 `systemctl stop nginx`
    - `wget -O - https://raw.githubusercontent.com/acmesh-official/acme.sh/master/acme.sh | INSTALLONLINE=1 sh`
    - `V2RAY_DOMAIN=你的域名`
    - `~/.acme.sh/acme.sh --issue -d $V2RAY_DOMAIN --standalone -k ec-256`
    - `~/.acme.sh/acme.sh --installcert -d $V2RAY_DOMAIN --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key --ecc`
    - 生成的证书在`/etc/v2ray`目录下
并且放到了指定位置
- 安装nginx
    - `apt install nginx`
- 复制粘贴小小的修改几个配置文件即可
    - 把[v2ray服务端文件](./assets/服务端配置文件.json)内容直接替换 `/etc/v2ray`下的config.json的内容,服务端的配置文件就解决了
    - 然后处理nginx的配置文件,直接把[nginx配置文件文件](./assets/nginx配置文件.txt)的内容替换掉 `/etc/nginx/sites-available/default`这个文件的内容,此时要确定下输入https加域名能跑得起来才行.
    <!-- - [客户端的配置文件在这里](./assets/客户端配置文件.json) 客户端的配置文件稍微麻烦一点,windows可以选择用[v2rayN](https://github.com/2dust/v2rayN),去release页面下载
    - 安卓可以用[v2rayNG](https://github.com/2dust/v2rayNG),配置文件里把域名换成自己的  然后导入到软件里 -->
    - 推荐使用clash客户端，[clash配置文件示例](./assets/config.yml) 把域名改成你自己的,然后把文件放到你的nginx root目录(/var/www/html)里即可通过url访问
- `systemctl restart nginx`
- `systemctl restart v2ray` 
- 然后可以尝试连接一下,不出意外应该可以了 *没错就是这么快!*

### 在cloudflare页面把云朵打勾即可

### 安装BBR加速
bbr脚本在[这里](https://github.com/chiakge/Linux-NetSpeed),建议装BBR PLUS,用着还行,安装BBR plus内核时会弹出两次确认框，选择`no`即可

### tips
国内腾讯云香港轻量，电信cn2 gia路线，联通移动直连，目前30M的速度差不多能跑满,24/月，可以考虑一下



    
