# https配置与部署

## Question

1.HTTPS证书支持覆盖二级域名吗？

2.如何选择证书提供商(CA)？

3.如何申请免费的证书？

4.使用https后对原来的应用有哪些影响？

5.使用https后怎么通过charles抓包分析？

## 概念

1. https证书的类型：

   ```
   DV SSL证书：域名型
   OV SSL证书：企业型
   EV SSL证书：增强型
   ```

   **DV SSL**证书：指只验证网站域名所有权的简易型SSL证书，此类证书仅能起到网站机密信息加密的作用，无法向用户证明网站的真实身份。所以，不推荐在电子商务网站部署 DV SSL证书，因为电子商务首先需要的是在线信任，其次才是在线安全。

   **OV SSL** 是 Organization Validation SSL 的缩写，指需要验证网站所有单位的真实身份的标准型SSL证书，此类证书也就是正常的SSL证书，不仅能起到网站机密信息加密的作用，而且能向用户证明网站的真实身份。所以，推荐在所有电子商务网站使用，因为电子商务需要的是在线信任和在线安全。从 SSL 证书的诞生史可以看出：标准型 SSL 证书就是 OV SSL证书(Organization Validation SSL)。

   **EV SSL** 是 Extended Validation SSL 的缩写，指遵循全球统一的严格身份验证标准颁发的SSL证书，是目前业界最高安全级别的SSL证书。用户访问部署了EV SSL证书的网站，不仅浏览器地址栏会显示安全锁标志，而且浏览器地址栏会变成绿色。所以，推荐所有电子商务网站都部署EV SSL证书，因为电子商务首先需要的是在线信任，其次才是在线安全。EV SSL证书，绿色安全通道，增强在线信任，促成更多在线订单！

2. 证书按覆盖范围分为：

   * **单域名证书**：只能用于单一域名
   * **通配符证书**：可以用于某个域名及其所有一级子域名。
   * **多域名证书**：可以用于多个域名

3. 选择证书提供商(CA)

   >浏览器和操作系统支持程度（即公网受信）
   >
   >证书类型
   >
   >维护成本

4. 申请免费的https证书

   [腾讯云免费证书](https://console.qcloud.com/ssl)

   let's encrypt：推荐比较多的，但是实际操作起来还是比较繁琐。

#### 腾讯云的免费DV证书申请

申请地址：[https://console.qcloud.com/ssl](https://console.qcloud.com/ssl)

免费证书是由`亚洲诚信TrustAsia`提供的免费版DVSSL证书，有效期是一年。

免费的DV证书只支持单域名，支持多域名和通配符的证书需要付费购买，所以如果有多个子域名，需要一个个的申请。

申请的流程也特别的简单，如果选择的是文件验证，添加文件后等待CA来验证就好了，大概几分钟左右验证通过后就会颁发证书，就可以在腾讯云的后台管理里下载证书了。

下载证书后怎么配置nginx呢，可以参考腾讯云的[证书安装指引](https://www.qcloud.com/document/product/400/4143)



## Nginx配置https

```
server{
    listen 443;
    server_name xxx.xxx.com;
    root /opt/https/;

    ssl on;
    ssl_certificate vhost/keys/xxx.xxx.com.crt;
    ssl_certificate_key vhost/keys/xxx.xxx.com.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
    ssl_prefer_server_ciphers on;

	location / {
		index index.html;
		root /opt/https;
	}
}
```

| 配置文件参数              | 说明                   |
| ------------------- | -------------------- |
| listen 443          | SSL访问端口号为443         |
| ssl on              | 启用SSL功能              |
| ssl_certificate     | 证书文件                 |
| ssl_certificate_key | 私钥文件                 |
| ssl_protocols       | 使用的协议                |
| ssl_ciphers         | 配置加密套件，写法遵循openssl标准 |

##### 使用全站加密,http自动跳转https

```
server{
	listen 80;
	server_name xxx.xxx.com;
	rewrite ^(.*) https://$host$1 permanent;
}
```



## 使用HTTPS可能带来的问题

1. https网站中存在http外部接口
2. CDN静态资源使用了http
3. charles抓包调试



## Charles配置可获取https数据（Mac os下）

本配置说明分为PC端和移动端。

### PC端

安装配置 Charles 根证书

![](https://ww3.sinaimg.cn/large/006tKfTcgy1ffhgjj75g3j30ni08yacv.jpg)

保存Charles的Root Certificate，一个xxx.pem文件。



![](https://ww4.sinaimg.cn/large/006tNc79gy1ffhgbua5ccj30o2094n01.jpg)

调出mac下的钥匙串访问



![](https://ww1.sinaimg.cn/large/006tKfTcgy1ffhggp0semj30oc0ffjsx.jpg)

点击左侧的登录，然后把第一步中保存的xxx.pem文件拖到右侧中，此时的情况是：

![](https://ww3.sinaimg.cn/large/006tKfTcgy1ffhgn3d4slj30oa0fdtcm.jpg)

系统默认是不信任 Charles 的证书的，此时对证书右键，在弹出的下拉菜单中选择『显示简介』，点击使用此证书时，把使用系统默认改为始终信任，如下图：

![](https://ww2.sinaimg.cn/large/006tKfTcgy1ffhgshfzxtj30e80c1tbg.jpg)

最终：

![](https://ww3.sinaimg.cn/large/006tKfTcgy1ffhgtih3yqj30rh0ij0v8.jpg)

配置到这一步还不能抓取https，还要配置一下Charles的SSL设置

![](https://ww4.sinaimg.cn/large/006tKfTcgy1ffhgz3fi2jj306a0a475q.jpg)

![](https://ww1.sinaimg.cn/large/006tKfTcgy1ffhh2dfb0oj30gf0c8dh3.jpg)

设置host为`*`就可以抓取所有的https站点的数据啦，当然你也可以选择一个一个添加，只抓取已经添加了的域名。

现在我们就可以在Charles中抓到https的包咯。

![](https://ww4.sinaimg.cn/large/006tKfTcgy1ffhhddrbj5j308y0ajdgq.jpg)



### 移动端

![](https://ww3.sinaimg.cn/large/006tKfTcgy1ffhhl8kb77j30ng08vq5r.jpg)

点击在移动设备上安装Root Certificate证书，会弹出下列提示：

![](https://ww4.sinaimg.cn/large/006tKfTcgy1ffhhk4fv2kj30or07lwf7.jpg)

在手机浏览器中打开`charlesproxy.com/getssl`这个链接，会跳转到证书安装界面：

![](http://img.blog.csdn.net/20160612203310693)

点击安装。

然后就可以抓取移动端的https包了。

关键的地方来了，经过上述操作之后我们可以抓取移动端Safari浏览器的https的包，因为我们已经在IOS内信任了Charles的证书，但是微信浏览器它似乎不是用的系统的信任体系，因此有了如下的错误提示：

![](https://ww4.sinaimg.cn/large/006tNc79ly1ffhifp363kj30pw02st9a.jpg)

`unknown_ca`说明微信浏览器并不信任Charles的 Root Certificate。

因为微信浏览器默认拒绝不安全的*https*，因此屏幕会直接提示网络出错。0……0

微信端的手机调试应该会比较头疼了，在此推荐一下另外一种调试方式吧。

### 微信web开发者工具

[微信web开发者工具下载地址](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/download.html)

使用**“微信web开发者工具”**，可以像chrome devTool那样看每一个请求。

![](https://ww1.sinaimg.cn/large/006tNc79gy1ffhjeaj7hjj31kg0u97ee.jpg)

微信web开发者工具可以用来做公众号和小程序开发的调试，因此我们可以用它来调试公众号内页面。但是牵涉到与微信本身功能相关的地方（比如说获取openid）就需要是该公众号的开发者才行，否则会提示：

![](https://ww1.sinaimg.cn/large/006tNc79gy1ffhjgorg5nj30fw03aaah.jpg)

遇到这种问题时就需要登陆该微信公众号的后台，把开发者的微信添加为“开发者”。

微信web开发者工具令人叫好的地方是，它有一个功能叫**代理！！！**

![](https://ww2.sinaimg.cn/large/006tNc79gy1ffhjq84wkej307k053t9r.jpg)

进入微信web开发者工具的设置->代理

![](https://ww1.sinaimg.cn/large/006tNc79gy1ffhjrb0wyij31410l8gmm.jpg)

**如上设置并保存后，我们就可以像是在手机端调试一样，被charles抓到包了！！！**

**如果没有设置代理，微信web开发者工具内的包是不会被charles抓到的！！！**



#### 参考链接

[SSL证书有哪几种？如何识别这几种SSL证书](http://www.wosign.com/FAQ/SSL_type_check.htm)

[Let's Encrypt 给网站加 HTTPS 完全指南](https://ksmx.me/letsencrypt-ssl-https/)

[HTTPS 升级指南](http://www.ruanyifeng.com/blog/2016/08/migrate-from-http-to-https.html)

[使用 Charles 获取 https 的数据](http://blog.csdn.net/yangmeng13930719363/article/details/51645435)

charles配置部分第4、5张以及倒数第2张图片摘自[使用 Charles 获取 https 的数据](http://blog.csdn.net/yangmeng13930719363/article/details/51645435)