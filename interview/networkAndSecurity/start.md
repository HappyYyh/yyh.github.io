---
title: Start
permalink: /interview/networkAndSecurity/start
key: networkAndSecurity-start
---

### 【OSI七层架构与TCP/IP四层架构】


<table>
    <thead>
        <tr>
            <th style="text-align: center">OSI七层网络模型</th>
            <th style="text-align: center">TCP/IP四层概念模型</th>
            <th style="text-align: center">对应网络协议</th>
            <th style="text-align: center">功能</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="text-align: center">应用层（Application）</td>
            <td style="text-align: center" rowspan="3">应用层</td>
            <td style="text-align: center">TFTP，HTTP，SNMP，FTP，SMTP，DNS，Telnet</td>
            <td style="text-align: center">文件传输，电子邮件，文件服务，虚拟终端</td>
        </tr>
        <tr>
            <td style="text-align: center">表示层（Presentation）</td>
            <td style="text-align: center">没有协议</td>
            <td style="text-align: center">数据格式化，代码转换，数据加密</td>
        </tr>
        <tr>
            <td style="text-align: center">会话层（Session）</td>
            <td style="text-align: center">没有协议</td>
            <td style="text-align: center">解除或建立与别的接点的联系</td>
        </tr>
        <tr>
            <td style="text-align: center">传输层（Transport）</td>
            <td style="text-align: center">传输层</td>
            <td style="text-align: center">TCP，UDP</td>
            <td style="text-align: center">提供端对端的接口</td>
        </tr>
        <tr>
            <td style="text-align: center">网络层（Network）</td>
            <td style="text-align: center">网络层</td>
            <td style="text-align: center">IP，ICMP，RIP，OSPF，BGP，IGMP</td>
            <td style="text-align: center">为数据包选择路由</td>
        </tr>
        <tr>
            <td style="text-align: center">数据链路层（Data link）</td>
            <td style="text-align: center" rowspan="2">网络接口层</td>
            <td style="text-align: center">SLIP，CSLIP，PPP，ARP，RARP，MTU</td>
            <td style="text-align: center">传输有地址的帧以及错误检测功能</td>
        </tr>
        <tr>
            <td style="text-align: center">物理层（Physical）</td>
            <td style="text-align: center">ISO2110，IEEE802，IEEE802.2
</td>
            <td style="text-align: center">以二进制数据形式在物理媒体上传输数据</td>
        </tr>
    </tbody>
</table>



### 【Wi-Fi属于哪一层】

​	Wi-Fi是物理层和数据链路层的东西。

​	Wi-Fi取代的是以太网的网线和交换机上的口，通过无线电波来收发信息。



### 【安全攻击的方式和解决方案】

1. **流量攻击**

   这种攻击就是我们常听说的**DDoS**攻击，它分为两种方式**带宽攻击**和**应用攻击**。

   流量攻击中的常见的宽带攻击，是竞争对手惯用的一种方法，一般是使用大量数据包淹没一个或多个路由器、服务器和防火墙，使你的网站处于瘫痪状态无法正常打开。对于这种攻击竞争对手都会花费人力和财力的，如果是真正意义上的DDoS攻击更是花费不少，个人认为当遇到流量攻击时不要惊慌，这种方式的花费竞争对手也是吃不消的，所以不会持续太久，如果是真正意义上的 DDoS攻击，你的空间商也会做相应措施的。

   在面对流量攻击时，企业要选择专业的有实力有一定规模的服务商，这样子在安全防护时也才能更好的应对。假如有经济条件也可以选择购买节点服务器。

   

2. **破坏数据性攻击**

   这种攻击时最致命，也是对网站损伤力更大的一种攻击方式。

   网站权限被拿到，数据被删除，这样就会造成大量的无页面链接，即死链接，这对于网站来说是致命的，不仅搜索引擎会降权，还会丢失大量用户。关键性数据的丢失可能会为企业带来无法弥补的损失。

   在面对这种攻击方式时，要经常**备份网站数据和网站关键程序**，最好打包到本地电脑里;做好关键文件的权限设置;网站最好采用全静态页面，因为静态页面是不容易被黑客攻击的;ftp和后台相关密码不要用弱口令。

3. **挂马或挂黑链攻击**

   这种攻击不如第二种危害大，但也是不容忽视的，搜索引擎一旦把你的网站视为木马网站就会k掉，甚至加为黑名单，这样你能换域名了，挂黑链主要是首页。

   在面对这种攻击方式时，除了做好上述第二种攻击提到的安全措施外，要经常**检查一下网站的导出链接**，看是否有可疑链接。出现迹象或者趋势时要及时的防患于未然，及时的防护。



### 【单点登录】 

**概念：**

> 单点登录（Single Sign On），简称为 SSO，是比较流行的企业业务整合的解决方案之一。SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。   ——[百度百科](https://baike.baidu.com/item/%E5%8D%95%E7%82%B9%E7%99%BB%E5%BD%95/4940767?fr=aladdin)

[原文：](https://blog.csdn.net/xiaoguan_liu/article/details/91492110)

**登录过程：**

相比于单系统登录，sso需要一个独立的认证中心，只有认证中心能接受用户的用户名密码等安全信息，其他系统不提供登录入口，只接受认证中心的间接授权。间接授权通过令牌实现，sso认证中心验证用户的用户名密码没问题，创建授权令牌，在接下来的跳转过程中，授权令牌作为参数发送给各个子系统，子系统拿到令牌，即得到了授权，可以借此创建局部会话，局部会话登录方式与单系统的登录方式相同。这个过程，也就是单点登录的原理，用下图说明：

![network-start-sso-login](http://image.yangyhao.top/blog/network-start-sso-login.png)

流程解释如下：

1. 用户访问系统1的受保护资源，系统1发现用户未登录，跳转至sso认证中心，并将自己的地址作为参数
2. sso认证中心发现用户未登录，将用户引导至登录页面
3. 用户输入用户名密码提交登录申请
4. sso认证中心校验用户信息，创建用户与sso认证中心之间的会话，称为全局会话，同时创建授权令牌
5. sso认证中心带着令牌跳转会最初的请求地址（系统1）
6. 系统1拿到令牌，去sso认证中心校验令牌是否有效
7. sso认证中心校验令牌，返回有效，注册系统1
8. 系统1使用该令牌创建与用户的会话，称为局部会话，返回受保护资源
9. 用户访问系统2的受保护资源
10. 系统2发现用户未登录，跳转至sso认证中心，并将自己的地址作为参数
11. sso认证中心发现用户已登录，跳转回系统2的地址，并附上令牌
12. 系统2拿到令牌，去sso认证中心校验令牌是否有效
13. sso认证中心校验令牌，返回有效，注册系统2
14. 系统2使用该令牌创建与用户的局部会话，返回受保护资源



**注销流程：**

单点登录自然也要单点注销，在一个子系统中注销，所有子系统的会话都将被销毁，用下面的图来说明

sso认证中心一直监听全局会话的状态，一旦全局会话销毁，监听器将通知所有注册系统执行注销操作

![network-start-sso-logout](http://image.yangyhao.top/blog/network-start-sso-logout.png)

下面对上图简要说明：

1. 用户向系统1发起注销请求
2. 系统1根据用户与系统1建立的会话id拿到令牌，向sso认证中心发起注销请求
3. sso认证中心校验令牌有效，销毁全局会话，同时取出所有用此令牌注册的系统地址
4. sso认证中心向所有注册系统发起注销请求
5. 各注册系统接收sso认证中心的注销请求，销毁局部会话
6. sso认证中心引导用户至登录页面



