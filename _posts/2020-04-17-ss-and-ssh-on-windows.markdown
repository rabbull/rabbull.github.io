---
layout: post
title:  "在 Windows 上丝滑使用 shadowsocks 并代理 ssh 登录远程服务器"
date:   2020-04-14 15:23:14 +0800
categories: proxy shadowsocks ssh windows
---


# shadowsocks

## 尝试 shadowsocks 本体

github: https://github.com/shadowsocks/shadowsocks

建立一个 python 虚拟环境并安装 shadowsocks：
```cmd
cd working_directory
pip install virtualenv
virtualenv shadowsocks-venv
shadowsocks-venv/Scripts/activate.bat
pip install shadowsocks
sslocal -c config.json  ::这里 config.json 是提前编辑好的
```

然后发现我这个 windows 上的预装 openssl 并不支持 aes-*-gcm
```cmd
openssl help
REM outputs:
REM ...
REM Cipher commands (see the `enc' command for more details)
REM aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb
REM aes-256-cbc       aes-256-ecb       aria-128-cbc      aria-128-cfb
REM aria-128-cfb1     aria-128-cfb8     aria-128-ctr      aria-128-ecb
REM aria-128-ofb      aria-192-cbc      aria-192-cfb      aria-192-cfb1
REM aria-192-cfb8     aria-192-ctr      aria-192-ecb      aria-192-ofb
REM aria-256-cbc      aria-256-cfb      aria-256-cfb1     aria-256-cfb8
REM aria-256-ctr      aria-256-ecb      aria-256-ofb      base64
REM bf                bf-cbc            bf-cfb            bf-ecb
REM bf-ofb            camellia-128-cbc  camellia-128-ecb  camellia-192-cbc
REM camellia-192-ecb  camellia-256-cbc  camellia-256-ecb  cast
REM cast-cbc          cast5-cbc         cast5-cfb         cast5-ecb
REM cast5-ofb         des               des-cbc           des-cfb
REM des-ecb           des-ede           des-ede-cbc       des-ede-cfb
REM des-ede-ofb       des-ede3          des-ede3-cbc      des-ede3-cfb
REM des-ede3-ofb      des-ofb           des3              desx
REM rc2               rc2-40-cbc        rc2-64-cbc        rc2-cbc
REM rc2-cfb           rc2-ecb           rc2-ofb           rc4
REM rc4-40            seed              seed-cbc          seed-cfb
REM seed-ecb          seed-ofb          sm4-cbc           sm4-cfb
REM sm4-ctr           sm4-ecb           sm4-ofb           zlib
REM ...
```

如果是这个列表里支持的方法就可以搞，不支持就只能再去折腾 openssl，果断放弃

## 尝试 shadowsocks-windows

github: https://github.com/shadowsocks/shadowsocks-windows

下载，启动，支持的 method 很丰富。

因为不是用来翻墙的只是用来访问内网的，所以启动之后不要启动系统代理，保持禁用就可以。


# ssh

## 系统自带 ssh 客户端

平时用着很方便，然而涉及到代理…… 只能说我懒得学了，图形界面真香。↓

## PuTTY

下载，安装，在 Connection->Proxy 下设置自己的 socks5 代理地址，回到 Session 里面链接，搞定。

无比丝滑。

