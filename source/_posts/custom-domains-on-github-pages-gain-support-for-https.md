---
title: <译>Custom domains on GitHub Pages gain support for HTTPS
date: 2019-10-19 16:24:24
tags:
categories: 英文向
---
**Github Pages 自定义域名获取对 HTTPS 的支持**

[[原文]Custom domains on GitHub Pages gain support for HTTPS - The GitHub Blog](https://github.blog/2018-05-01-github-pages-custom-domains-https/)

Today, custom domains on GitHub Pages are gaining support for HTTPS.
如今，GitHub Pages 上的自定义域名正在获得对HTTPS的支持。
￼![2018-05-01-github-pages-custom-domains-lock](https://tva3.sinaimg.cn/large/a9034e0egy1gjvly5zewjj20xc0hi74o.jpg)

Github Pages is the best way to quickly publish beautiful websites for you and your projects. Just edit, push, and your changes are live. GitHub Pages has supported custom domains since 2009, and sites on the `*.github.io` domain have supported HTTPS since 2016. Today, custom domains on GitHub Pages are gaining support for HTTPS as well, meaning over a million GitHub Pages will be served over HTTPS.
Github Pages 是为您和您的项目快速发布漂亮的网站最好的方式。仅通过编辑、推送，您的修改就上线了。GitHub Pages 自从 2009 年开始支持自定义域名（custom domains），并且从 2016 年让所有的`*.github.io` 域名支持HTTPS。而现在，Github Pages 上的自定义域名也正在获得对 HTTPS 的支持，意味着超过百万的 Github Pages 将通过 HTTPS 提供服务。

HTTPS(most recognizable as the lock icon in your browser’s address bar) encrypts traffic between Github’s servers and your browser giving you confidence that the page you asked for is the page you’re reading, from the site you think it is, and that others can’t snoop on or modify its contents along the way.
HTTPS（最容易辨认的是浏览器地址栏中的锁图标）对GitHub服务器和浏览器之间的流量进行加密，让你确信你要求的页面是你正在阅读的页面，来自你认为的网站，并且其他人无法窥探或修改其内容。

We have partnered with the certificate authority [Let’s Encrypt](https://letsencrypt.org/) on this project. As supporters of Let’s Encrypt’s mission to make the wen more secure for everyone, we’ve officially become Silver-level sponsors of the initiative.
我们已经与证书颁发机构Let's Encrypt合作开展此项目。作为Let's Encrypt的支持者，我们已经正式成为该计划的银级赞助商。

## Configuring your domain（配置您的域名）
Action may be required on your part to secure your custom domain.
您可能需要采取一些行动来保护您的自定义域名。

If you are using `CNAME` or `ALIAS` records for your custom domain, you’re all set and your site should be accessible over HTTPS. If it is, and your site loads correctly over HTTPS, you can optionally enforce HTTPS in your repository’s setting. Users who request your site over HTTP will be upgraded to HTTPS.
如果您使用 CNAME 或 ALIAS 记录来保护您的自定义域，您已经准备就绪并且您的网站应该可以通过 HTTPS 访问。如果是这样，并且您的网站通过HTTPS正确加载，您可以在您的版本库设置中选择强制执行HTTPS。通过HTTP请求您的网站的用户将被升级到HTTPS。
域名解析时，记录类型选择 CNAME，记录值直接使用 `<your name>.github.io` 即可；最后确认 `Enforce HTTPS` 选框选中。
![2D7E467D-2897-45E9-92CE-9214C35C77D6](https://tva1.sinaimg.cn/large/a9034e0egy1gjvly5swhfj21um0kmwir.jpg)
![A22BC560-9CFC-4BF3-A2A8-2BFE91BB1408](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvly65bkej216g196wh8.jpg)
![B1936EFD-EC60-473E-8857-7642F6EEC3FC](https://tva2.sinaimg.cn/large/a9034e0egy1gjvly6irsbj21fs0y6afz.jpg)

If you are using `A` records, you must update your site’s DNS records with new IP addresses. Please [see our guide to setting up your custom domain with Pages and update any A records you might have set](https://help.github.com/articles/setting-up-an-apex-domain/).
如果你正在使用A记录，你必须用新的IP地址更新你网站的DNS记录。请参阅我们的指南，设置你Pages的自定义域名并更新任何你可能已经设置A记录。

Once your updated DNS records have propagated, and you’ve confirmed that your site loads correctly over HTTPS, you can optionally `"Endorce HTTPS" for your domain` in your repository’s settings, ensuring users who request your site over HTTP are upgraded to HTTPS.
一旦你更新的 DNS 记录被传播，并且你已经确认你的网站通过 HTTPS 正确加载，你可以在你的Repository的设置中为你的域名选择 "强制执行 HTTPS"，确保通过 HTTP 请求你的网站的用户升级到 HTTPS。

## Faster site load times（提升网站加载速度）
These new IP addresses don’t just allow us to serve your site over HTTPS, but also places your site behind a [content delivery network(CDN)](http://en.wikipedia.org/wiki/Content_delivery_network), allowing us to serve your site from data centers around the world at fast speeds, and offering additional protection against [DDoS attack](http://en.wikipedia.org/wiki/Denial-of-service_attack). While the previous IP addresses will remain available for a transition period, we recommend you migrate to the new IP addresses to gain these benefits.
这些新的IP地址不仅让我们能够通过HTTPS为您的网站提供服务，而且还将您的网站置于内容交付网络（CDN）的后面，使我们能够从世界各地的数据中心以快速的速度为您的网站提供服务，并提供额外的DDoS攻击保护。虽然之前的IP地址在过渡期内仍然可用，但我们建议您迁移到新的IP地址以获得这些好处。

HTTPS is immediately available for all Github Pages sites. Learn more [in our documentation for the feature](https://help.github.com/articles/securing-your-github-pages-site-with-https/). Having trouble? [Drop up a line](https://github.com/contact?form%5Bsubject%5D=GitHub%20Pages%20HTTPS%20for%20Custom%20Domains).
所有GitHub Pages网站都可以立即使用HTTPS。在我们的文档中了解更多关于该功能的信息。有问题？请给我们留言。