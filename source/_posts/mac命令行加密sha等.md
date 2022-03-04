---
title: mac命令行加密sha等
top: false
cover: false
toc: true
mathjax: true
tags:
  - null
categories:
  - null
typora-root-url: ../
date: 2022-03-04 16:51:48
password:
summary:
---

> Note: `echo -n` 代表不发送换行符; 不带参数默认发送换行符。

[base64](https://so.csdn.net/so/search?q=base64&spm=1001.2101.3001.7020) encode

```shell
echo -n 123|base64
or
echo -n "123"|base64
or 
base64 -i 1.txt
or 
echo -n 123|openssl enc -base64
1234567
```

base64 decode

```shell
echo -n MTIz|base64 -D
or
echo -n "MTIz"|base64 -D
or 
base64 -D -i 1.txt
or
echo MTIz|openssl enc -base64 -d;echo
or
echo MTIz|openssl enc -base64 -d
123456789
```

[MD5](https://so.csdn.net/so/search?q=MD5&spm=1001.2101.3001.7020)

```shell
openssl dgst -md5 123.bin
or
openssl md5 123.bin
or
md5 123.bin
or
md5 -s "text"
or 
echo -n foo|md5
or 
echo -n "foo"|md5
or
echo -n "foo"|openssl dgst -md5
12345678910111213
```

HmacMD5

```shell
openssl md5 -hmac "key" 123.bin
echo -n "value" | openssl dgst -md5 -hmac "key"
12
```

[SHA1](https://so.csdn.net/so/search?q=SHA1&spm=1001.2101.3001.7020)

```shell
openssl dgst -sha1 123.bin
or
openssl sha1 123.bin
or
echo -n "value" | openssl dgst -sha1
12345
```

HMAC_SHA1

```shell
echo -n "value" | openssl dgst -sha1 -hmac "key"
echo -n "value" | openssl sha1 -hmac "key"
12
```

SHA256

```shell
openssl sha256 123.bin
or
openssl dgst -sha256 123.bin
or 
echo -n "value" | openssl dgst -sha256
12345
```

HMAC_SHA256

```shell
echo -n "value" | openssl dgst -sha256 -hmac "key"
echo -n "value" | openssl sha256 -hmac "key"
```

参考：https://blog.csdn.net/toopoo/article/details/99657602
