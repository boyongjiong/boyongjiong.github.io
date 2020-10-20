---
title: charles 抓包 iOS 中 https 乱码问题
date: 2020-05-26 11:34:25
tags: 
categories: 工具类
---

在使用Charles调试iOS程序的时候，一直抓取不到https的内容，一直是乱码，如何解决这种问题，明确的指出：iOS的ssl证书已经进行安装,在mac系统中charles证书也已经安装，就是抓取https一直是乱码。

**不成功显示结果为**
> SSLHandshake: Received fatal alert: unknown_ca

## 使用说明
在Charles官网中在这么一段话，说明如何都正确设置还是抓取不到真正的Https内容，可以按照以下方法设置。

> In Charles go to the Help menu and choose "SSL Proxying > Install Charles Root Certificate". Keychain Access will open. Find the "Charles Proxy..." entry, and double-click to get info on it. Expand the "Trust" section, and beside "When using this certificate" change it from "Use System Defaults" to "Always Trust". Then close the certificate info window, and you will be prompted for your Administrator password to update the system trust settings.
> 
> You may need to quit and reopen Safari to see the change.
> 
> **iOS devices**
> - Set your iOS device to use Charles as its HTTP proxy in the Settings app > WIFI settings.
> - Open Safari and browse to https://chls.pro/ssl. Safari will prompt you to install the SSL certificate.
> - If you are on iOS 10.3 or later, open the Settings.app and navigate to General -> About -> Certificate Trust Settings, and find the Charles Proxy certificate, and switch it on to enable full trust for it (More information about this change in iOS 10).
> - Now you should be able to access SSL websites with Charles using SSL Proxying.
> - Charles supports App Transport Security (ATS) as of the 3.11.4 release.

**::翻译过来::**

1. 在 Mac 系统中的 `Charles -> Help -> SSL-Proxying -> Install Charles Root Certificate`
2. 搜索 `Charles Procy`
3. 双击打开，打开 `Trust` 下拉选项
4. 下拉选择 `Always Trust`
5. 然后输入你的 Mac 密码即可
6. iOS系统中设置你连接WIFI的Http代理设置为Charles所在的机子IP和端口
7. 用Safari浏览器打开https://chls.pro/ssl 地扯，安装SSL证书
8. *注意*：如果是10.3或之后的系统，在设置 -> 能用 -> 关于手机 -> 证书信任设置 -> Charles Proxy certificate 启用即可