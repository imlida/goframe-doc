---
title: '本地文档-doc'
sidebar_position: 10
---

该命令从框架 `v2.7.2` 版本开始提供。

## 使用场景

网络环境不能畅顺的访问 [goframe.org](http://goframe.org) 或 [pages.goframe.org](http://pages.goframe.org) 的情况，通过该命令可以将 [pages.goframe.org](http://pages.goframe.org) 文档下载到本地并运行起来。

## 使用方式

```
$ gf doc -h
USAGE
    gf doc [OPTION]

OPTION
    -p, --path     download docs directory path, default is "%temp%/goframe"
    -o, --port     http server port, default is 8080
    -u, --update   clean docs directory and update docs
    -c, --clean    clean docs directory
    -x, --proxy    proxy for download, such as https://hub.gitmirror.com/;https://ghproxy.com/;https://ghproxy.net/;
                   https://ghps.cc/
    -h, --help     more information about this command
```

如果你的网络不佳，可以使用-x参数。 `gf doc -x=https://hub.gitmirror.com/`

默认启动的时候会将文档下载到系统的临时目录当中，可通过-p参数自定义目录，但下次启动也要指定该参数，否则会重复下载文档。