[http://github.com](https://link.zhihu.com/?target=http%3A//github.com)有时无法连接，即使翻墙之后也不行，此时可以通过配置本地 Hosts 文件，不再通过 DNS 服务器解析地址而连接。

首先，打开[http://tool.chinaz.com/dns](https://link.zhihu.com/?target=http%3A//tool.chinaz.com/dns)，查询[http://github.global.ssl.fastly.net](https://link.zhihu.com/?target=http%3A//github.global.ssl.fastly.net) 和 [http://assets-cdn.github.com](https://link.zhihu.com/?target=http%3A//assets-cdn.github.com) 两个域名的解析地址，用于替换下方的Hosts配置。

其次，Host中添加如下配置(Mac中Hosts编辑器推荐使用Gas Mask)：

```text
#github
192.30.253.112 Build software better, together
192.30.253.113 Build software better, together
192.30.253.119 gist.github.com
151.101.109.194 github.global.ssl.fastly.net
185.199.108.153  assets-cdn.github.com
151.101.100.133 raw.githubusercontent.com
```

以上，即可通过浏览器正常访问[http://github.com](https://link.zhihu.com/?target=http%3A//github.com)。


[参考](https://zhuanlan.zhihu.com/p/108898992?utm_source=wechat_session)