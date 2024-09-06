---
title: '日志组件-JSON格式'
sidebar_position: 7
---

`glog` 对日志分析工具非常友好，支持输出 `JSON` 格式的日志内容，以便于后期对日志内容进行解析分析。

## 使用 `map/struct` 参数

想要支持 `JSON` 数据格式的日志输出非常简单，给打印方法提供 `map`/ `struct` 类型参数即可。

使用示例：

```
package main

import (
	"context"

	"github.com/gogf/gf/v2/frame/g"
)

func main() {
	ctx := context.TODO()
	g.Log().Debug(ctx, g.Map{"uid": 100, "name": "john"})
	type User struct {
		Uid  int    `json:"uid"`
		Name string `json:"name"`
	}
	g.Log().Debug(ctx, User{100, "john"})
}
```

执行后，终端输出结果：

```
2019-06-02 15:28:52.653 [DEBU] {"name":"john","uid":100}
2019-06-02 15:28:52.653 [DEBU] {"uid":100,"name":"john"}
```

## 结合 `gjson.MustEncode`

此外，也可以结合 `gjson.MustEncode来` 实现 `Json` 内容输出，例如：

```
package main

import (
	"context"

	"github.com/gogf/gf/v2/encoding/gjson"
	"github.com/gogf/gf/v2/frame/g"
)

func main() {
	ctx := context.TODO()
	type User struct {
		Uid  int    `json:"uid"`
		Name string `json:"name"`
	}
	g.Log().Debugf(ctx, `user json: %s`, gjson.MustEncode(User{100, "john"}))
}
```

执行后，终端输出结果：

```
2022-04-25 18:09:45.029 [DEBU] user json: {"uid":100,"name":"john"}
```