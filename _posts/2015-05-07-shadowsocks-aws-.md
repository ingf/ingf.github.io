---
layout: post
title: "利用Shadowsocks和 AWS 免费翻墙"
description: "VPN Shadowsocks  greenVPN 免费 账号 翻墙 AWS 云主机"
category: 
tags: [ VPN]
---

世界那么大，我想去看看，但是被墙了...

以前用的池老师推荐的 greenVPN，总体来说，还不错，但是最近老断，于是就搞了个新的法子。申请一台云主机，在上面搭一个Shadowsocks，然后就可以在所有客户端看 YouTube 了，当然，这所有的一切都是免费的。

## 搭建Shadowsocks 服务器

首先需要一个云主机，其实[青云](https://www.qingcloud.com/)的很好嘛，但是还有更好的，[http://aws.amazon.com/cn/free/](http://aws.amazon.com/cn/free/) AWS 有一年的免费使用期限，对于翻墙和自己搞点啥东西还是很方便的，强烈推荐，这中间需要绑定信用卡号，会扣掉一美元，注意不要超过期限了哦，超过了可能会要扣钱的。

申请下来了以后，创建一个Amazon EC2实例(edhat 默认用户名为ec2-user ubuntu默认用户名为ubuntu)，然后就可以在上面倒腾各种服务了。先开启防火墙吧，在网络与安全里面进行设置，如下图所示，在操作那里点开，编辑入站规则
![shadowsocks-safe](/img/shadowsocks-safe.png)

然后我们来安装 Shadowsocks 吧，[http://shadowsocks.org/en/download/servers.html](http://shadowsocks.org/en/download/servers.html)，这里列出了所有的服务器版本，各种支持啊，我很高兴的选择了 Node.js 版，先安装 Node.js，可以点[这里下载](https://nodejs.org/download/)，然后 `npm install -g shadowsocks` 

搞定！接下来进行配置，我的配置文件在这里`/usr/local/lib/node_modules/shadowsocks/config.json`

    {
        "server":"0.0.0.0",
        "server_port":8388,
        "local_address":"127.0.0.1",
        "local_port":1080,
        "password":"barfoo!",
        "timeout":600,
        "method":"aes-256-cfb"
    }

启动服务，因为平常都用 pm2管理，所以用 pm2启动服务 `pm2 start /usr/bin/ssserver`

## 客户端

搞完了服务器，搞客户端，Shadowsocks 客户端那个多呀，[http://shadowsocks.org/en/download/clients.html](http://shadowsocks.org/en/download/clients.html) 都支持。下面说一下 Mac 的配置，其他的一样样的，从上面的链接下载ShadowsocksX，然后启动ShadowsocksX，客户端配置参照下图，IP 填你的云主机的 IP 就 OK 了。

![shadowsocks1](/img/shadowsocks1.png)
![shadowsocks2](/img/shadowsocks2.png)

恩，好，打开[https://www.youtube.com/](https://www.youtube.com/)试试吧！
其他端，Android 和 iOS 自己弄把，很好弄的。


