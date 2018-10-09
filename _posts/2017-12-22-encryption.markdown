---
layout: post
title: Diffie-Hellman 中的一些笔记
category: encryption
tag : encryption
---

前些天逛论坛, 看到有人问pgp加密中, 为什么要先用非对称加密来加密对称key, 再用对称加密来处理正文, 而不是直接用public/private key体系来处理  

看到这个突然想起了以前上学的时候学密码学这门课, 记忆非常深的Diffie-Hellman key exchange, 所谓温故知新, 可以顺便再回忆一下这个牛逼的算法(牛逼到帮助Diffie&Hellman他俩获得了2015图灵奖) 
 
当然之前那个问题的答案是--非对称加密速度要比对称加密快很多, 所以用对称加密来处理正文会来的划算(其实我在这次温故之前一直把pgp记成是用来传递对称密钥了, 而实际上pgp每次都会使用一个不同的对称密钥)  

Diffie-Hellman key exchange algorithm的有效性依赖于计算离散对数的难度  

对于离散对数, 定义:设p是质数, g是p的原根, a介于(1,p-1), 满足g^a mod p = b, 则a就是b的离散对数  

上面的定义, 原根要再解释一下, 设p是质数, a, b为整数且介于(1,p-1), 对于满足条件的任意a,b 有g^a mod p != g^b mod p, 则g就是p的原根, 也就是说g的1次幂, 2次幂一直到p-1次幂的结果去mod p所得的余数如果都不想等, 那g就是一个p的原根  

下面描述Diffie-Hellman这个算法, 假设alice跟bob要在不安全的网络环境中通讯:  
>1, alice,bob先协商一个非常大的质数p, 一个p的原根g, 这里p, g是可以在网络被人获取到的, 可以看作公开  
>2, alice生成一个小于p的随机数a,  计算g^a mod p 的值x, 将x通过明文手段告诉bob, 即可以看作公开  
>3, bob生成一个小于p的随机数b, 计算g^b mod p的值y, 同样将y通过明文手段告诉alice, 也可以看作公开  
>4, alice看到y 可以计算y^a mod p 得到key1即为共享密钥  
>5, bob看到x 计算x^b mod p 得到key2即为共享密钥  

我们可以先来看为什么key1 == key2   
key1   
= y^a mod p  
= (g^b mod p) ^a mod p  
= g^ab mod p

key2  
= x^b mod p  
= (g^a mod p)^ b mod p  
= g^ab mod p

所以key1==key2 

另外还有一点, 我们可以看到, 在这种情况下暴露在外的数据有p ,g, x, y, 如果攻击者找到了x, y的值 就可以利用p, g来穷举(1,p-1)的所有值, 直到测试出可用的a或者b, 也就可以得到密钥key, 那么所以成败的关键就在于p一定要非常大  

如果只是一个几百的质数, 那么很快就会被穷举完毕, 但是实际中我们可能会使用1024位或者2048位的质数, 那么穷举的代价会是2^1024, 2^2048, 这是非常非常巨大的操作了, 几乎不可能        
  