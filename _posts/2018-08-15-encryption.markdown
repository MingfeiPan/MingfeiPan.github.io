---
layout: post
title: 配置证书的一些笔记
category: linux
tag : linux
---

<img src="/img/in-post/linux.jpg">

最近一年的时间, google收紧了chrome对https的要求, 到现在我用的68版本已经标志http访问为不安全, 还把Symantec的证书认证也搞了(chrome已经不再信任Symantec的证书)。 其实推广https我是绝对滋瓷的, 但是不知道为啥我司线下自己配的证书全部失效了, 昨天花了些时间更新了证书, 这里记一些笔记。  

先汇总的说一下, 截止到今天chrome对于证书的要求是 要sha256 2048位 带SAN(Subject Alternative Name,多域名), 所以使用sha1的已经不可用, 另外没有配SAN的也不可用。 

`查到是在chrome58的时候取消了对原来用的COMMON NAME通用域名的支持, 改为支持SAN(多域名)系统这种写法(只对自建证书, 被ca认证的证书可以兼容)` 

先解释一些遇到的名词:   

* ssl 这个大家都知道, Secure Socket Layer, 也叫做TLS(99年被标准化之后给的称呼), https即为http over ssl。 结合非对称加密与对称加密ssl提供了可靠的信息加密身份验证等等服务   
* X.509 一种证书标准, 定义证书内应该有哪些内容, SSL使用这种标准的证书  
* pem 编码格式, Privacy Enhanced Mail, 可以用cat命令查看文本, 可以用 *openssl x509 -in example.pem -text -noout* 查看证书内容  
* der 编码格式, 这是另一种编码格式, 直接cat是二进制格式, 类似pem可以用命令 *openssl x509 -in example.der -inform der -text -noout* 查看, 通常windows服务器使用der格式而unix使用pem格式  
* crt/cer 扩展名, 有些证书直接使用pem或者der做扩展名, 有些则会使用其他的比如crt/cer, 都来自certificate这个词。注意使用这两个扩展名的证书编码不是确定的, 有可能使用pem也可能使用der, 一般会跟着操作系统的使用习惯, unix用crt windows用cer(mac也有见到cer)  
* key 存放公钥或者私钥的文件, 用pem或者der格式编码, 可以用 *openssl rsa in example.key -text -noout* 查看  
* csr Cerificate Signing Request, 证书签名请求, 用来向ca机构申请证书的东西, 其实就是你的公钥, 还附带一些相关的信息    

借助openssl命令, 生成key以及证书: 

``` 
openssl req -new -newkey rsa:2048 -sha256 -nodes -out ekwing.csr -keyout ekwing.key   
```

会有命令行提示输入信息, 国家地区公司什么的, 正常写就可以。执行完毕得到一对使用sha256 摘要算法处理的, 使用rsa算法并且密钥长度为2048位的公钥/私钥, 公钥即为csr文件, 私钥存放在key文件里  

下一步生成证书, 这里我们使用openssl命令自建, 如果线上正式使用就得去找大的ca厂商签名(记住别找Symantec...):  

```
openssl x509 -req -sha256 -days 3650 -in ekwing.csr -signkey ekwing.key -out ekwing.crt
```

生成的ekwing.crt即为证书  

<img src="/img/in-post/https.png">

然后配置到服务器, 比如nginx:

```
ssl on; 
ssl_certificate ekwing.crt;
ssl_certificate_key ekwing.key;
```

浏览器访问服务器, 加载证书, 我们需要手动添加系统信任。在之前版本的chrome中完成这些步骤之后, 证书就已经可以使用。但是现在的话, 仍旧会报错:  

<img src="/img/in-post/sanmissing.png">  

SAN missing, chrome的政策改动, 现在必须要带有SAN扩展的证书才可以通过认证, 所以生成证书的时候附带上SAN的部分, 新建文件temp.conf, 写入内容    

```
subjectAltName = @alt_names
extendedKeyUsage = serverAuth

[alt_names]
DNS.1 = *.ekwing.com
```
生成命令附带conf文件即可  

```
openssl x509 -req -sha256 -days 3650 -in ekwing.csr -signkey ekwing.key -extfile temp.conf -out ekwing.crt
```

之后我们再次下载证书, 本地系统添加信任后可以看到:  

<img src="/img/in-post/cert1.png">

现在就可以正常使用了, 另外打开证书可以看到关于SAN的部分, 这是之前证书缺失的:  

<img src="/img/in-post/cert2.png">  





