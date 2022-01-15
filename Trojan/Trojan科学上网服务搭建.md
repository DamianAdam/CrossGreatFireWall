# trojan-go搭建

## 一、简介

Trojan是近两年兴起的网络工具，项目官网https://github.com/trojan-gfw。

与强调加密和混淆的SS/SSR等工具不同，trojan将通信流量伪装成互联网上最常见的https流量，从而有效防止流量被检测和干扰。在敏感时期，基本上只有trojan和V2Ray伪装能提供稳如狗的体验。

V2Ray和Trojan有如下区别及特点：

1. v2ray是一个网络框架，功能齐全；trojan只是一个绕过防火墙的工具，轻量级、功能简单；都使用TLS加密的情况下，理论上trojan比V2ray性能更好；
2. v2ray和trojan都能实现https流量伪装；
3. v2ray内核用go语言开发，trojan是c++实现；
4. v2ray名气大，使用的人多，客户端很好用；trojan关注和使用的人少，官方客户端简陋，生态完善度不高。

## 二、准备

- 国内可连接的国外VPS一台、域名一个。

## 三、Trojan搭建

#### 3.1 解析域名

首先，为了国内操作方便，我们可以将域名托管到阿里云，或者直接在阿里云购买一个域名也可以。只是解析IP地址工信部是不会管的。具体步骤如下：

- 访问阿里云官网：https://www.aliyun.com/，完成登录后点击右上角的控制台，然后按照下例图示访问域名控制面板。

![3.1-1](./img/3.1-1.png)

- 在弹出的页面中找到全部域名，其中就有已购买（或已托管）的域名，点击该域名操作部分的解析进入解析页面。

![3.1-2](./img/3.1-2.png)

- 点击添加记录。

![3.1-3](./img/3.1-3.png)

- 按照一下图示进行配置，配置完成后点击下方确认。

![3.1-4](./img/3.1-4.png)

- 添加成功的记录会出现在列表中。

![3.1-5](./img/3.1-5.jpg)

- 可以采用 `ping 域名` 的方式检测解析是否成功。

#### 3.2 配置BBR内核加速

- 使用SSH工具连接到VPS上。
- 执行一键脚本进行安装。

```bash
wget -O tcpx.sh "https://git.io/JYxKU" && chmod +x tcpx.sh && ./tcpx.sh
```

- 选择5安装 BBRplus新版内核。

```bash
 TCP加速 一键安装管理脚本 [v1.3.2.100] 不卸内核 from blog.ylx.me 母鸡慎用
 0. 升级脚本
 9. 切换到卸载内核版本          10. 切换到一键DD系统脚本
 1. 安装 BBR原版内核
 2. 安装 BBRplus版内核          5. 安装 BBRplus新版内核
 3. 安装 Lotserver(锐速)内核    6. 安装 Zen官方内核
 30. 安装 官方稳定内核          31. 安装 官方最新内核 backports/elrepo
 32. 安装 XANMOD官方内核        33. 安装 XANMOD官方高响应内核
 11. 使用BBR+FQ加速             12. 使用BBR+FQ_PIE加速
 13. 使用BBR+CAKE加速
 14. 使用BBR2+FQ加速            15. 使用BBR2+FQ_PIE加速
 16. 使用BBR2+CAKE加速
 17. 开启ECN                    18. 关闭ECN
 19. 使用BBRplus+FQ版加速       20. 使用Lotserver(锐速)加速
 21. 系统配置优化               22. 应用johnrosen1的优化方案
 23. 禁用IPv6                   24. 开启IPv6
 51. 查看排序内核               52. 删除保留指定内核
 25. 卸载全部加速               99. 退出脚本
————————————————————————————————————————————————————————————————
 系统信息: Debian GNU/Linux 10 Microsoft Hyper-V x86_64 5.10.0-0.bpo.9-cloud-amd64
 当前状态: 已安装 BBR 加速内核 , 未安装加速模块
 当前拥塞控制算法为: cubic 当前队列算法为: fq
 请输入数字 :5
```

- 然后执行./tcpx.sh，选择19使用BBRplus+FQ版加速。

```bash
 TCP加速 一键安装管理脚本 [v1.3.2.100] 不卸内核 from blog.ylx.me 母鸡慎用
 0. 升级脚本
 9. 切换到卸载内核版本          10. 切换到一键DD系统脚本
 1. 安装 BBR原版内核
 2. 安装 BBRplus版内核          5. 安装 BBRplus新版内核
 3. 安装 Lotserver(锐速)内核    6. 安装 Zen官方内核
 30. 安装 官方稳定内核          31. 安装 官方最新内核 backports/elrepo
 32. 安装 XANMOD官方内核        33. 安装 XANMOD官方高响应内核
 11. 使用BBR+FQ加速             12. 使用BBR+FQ_PIE加速
 13. 使用BBR+CAKE加速
 14. 使用BBR2+FQ加速            15. 使用BBR2+FQ_PIE加速
 16. 使用BBR2+CAKE加速
 17. 开启ECN                    18. 关闭ECN
 19. 使用BBRplus+FQ版加速       20. 使用Lotserver(锐速)加速
 21. 系统配置优化               22. 应用johnrosen1的优化方案
 23. 禁用IPv6                   24. 开启IPv6
 51. 查看排序内核               52. 删除保留指定内核
 25. 卸载全部加速               99. 退出脚本
————————————————————————————————————————————————————————————————
 系统信息: Debian GNU/Linux 10 Microsoft Hyper-V x86_64 5.10.0-0.bpo.9-cloud-amd64
 当前状态: 已安装 BBR 加速内核 , 未安装加速模块
 当前拥塞控制算法为: cubic 当前队列算法为: fq
 请输入数字 :19
```

- 重启一次。

```bash
init 6
```

- 重启完成后再执行./tcpx.sh，当前状态: 已安装 BBRplus 加速内核 , BBRplus启动成功则说明内核安装成功。

#### 3.3 安装Trojan

- 执行Trojan一键安装脚本。

```shell
source <(curl -sL https://git.io/trojan-install)
```

- 选择1使用Let's Encrypt证书。

```bash
1.Let's Encrypt 证书

2.自定义证书路径

请选择使用证书方式: 1
```

- 检查本机IP，并输入刚刚解析好的域名。

```bas
本机ip: 123.123.123.123
请输入申请证书的域名: www.abc.def
```

- 使用docker版的mysql存储数据。

```bash
1.安装docker版mysql(mariadb)

2.输入自定义mysql连接

请选择: 1
```

- 默认用户名和密码都使用随机即可。

```bash
生成随机用户名: VUAj, 使用直接回车, 否则输入自定义用户名: 
生成随机密码: qVMdYJwN, 使用直接回车, 否则输入自定义密码: 
新增用户成功!
```

- 在浏览器上访问解析好的域名地址，这里为https://www.abc.def/，首次访问会要求设置admin管理员密码。

![3.3-1](./img/3.3-1.png)

- 然后使用配置好的admin用户即可以管理员的身份登录Trojan面板。

![3.3-2](./img/3.3-2.png)

## 四、配置trojan-go与使用

#### 4.1 配置trojan-go

- 访问trojan面板，点击左侧的trojan管理。

![4.1-1](./img/4.1-1.png)

- 在类型处从默认的trojan改为trojan-go。

![4.1-2](./img/4.1-2.png)

#### 4.2 添加普通用户

- 依旧在面板左侧，点击用户管理。添加上方的添加按钮，

![4.2-1](./img/4.2-1.jpg)

- 输入用户名和密码，点击确定。

![4.2-2](./img/4.2-2.jpg)

- 此时用户列表中会出现新增用户，点击右侧分享。

![4.2-3](./img/4.2-3.png)

- 此时在弹出的窗口中有一个二维码和一个链接地址。

![4.2-4](./img/4.2-4.png)

#### 4.3 在Netch中导入节点。

- 复制刚才弹窗下方的链接地址，打开Netch客户端。
- 点击服务器 >> 从剪切板导入，此时就会有一条节点导入成功。
- 服务器选择你导入成功的节点，模式建议使用"\[3] [TUN/TAP] 绕过局域网和中国大陆"。
- 点击右下角的启动，即可访问https://www.google.com/以及GitHub等你需要的网站。

