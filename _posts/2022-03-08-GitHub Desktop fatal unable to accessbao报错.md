
---
layout: post
title: "完美解决GitHub Desktop报错"
date: 2022-03-08
toc: true
tags: [GitHub Desktop]
comments: true
author: 暮光烛日
---

# 完美解决GitHub Desktop报错 fatal: unable to access 'https://github.com/XXXX.io.git/': schannel: failed to receive handshake, SSL/TLS connection failed

只需要在命令行中执行

```
git config --global --unset http.proxy 
git config --global --unset https.proxy
```