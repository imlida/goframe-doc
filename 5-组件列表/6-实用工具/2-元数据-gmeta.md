---
title: '元数据-gmeta'
sidebar_position: 2
---

## 基本介绍

主要用于嵌入到用户自定义的结构体中，并且通过标签的形式给 `gmeta` 包的结构体打上自定义的标签内容（元数据），并在运行时可以特定方法动态获取这些自定义的标签内容。

**使用方式：**

```
import "github.com/gogf/gf/v2/util/gmeta"
```

**接口文档**：

[https://pkg.go.dev/github.com/gogf/gf/v2/util/gmeta](https://pkg.go.dev/github.com/gogf/gf/v2/util/gmeta)

**方法列表：**

```
func Data(object interface{}) map[string]interface{}
func Get(object interface{}, key string) *gvar.Var
```

## 使用示例

### `Data` 方法

`Data` 方法用于获取指定 `struct` 对象的元数据标签，构成 `map` 返回。

```
package main

import (
	"fmt"
	"github.com/gogf/gf/v2/frame/g"
	"github.com/gogf/gf/v2/util/gmeta"
)

func main() {
	type User struct {
		gmeta.Meta `orm:"user" db:"mysql"`
		Id         int
		Name       string
	}
	g.Dump(gmeta.Data(User{}))
}
```

执行后，终端输出：

```
{
    "db": "mysql",
    "orm": "user"
}
```

### `Get` 方法

`Get` 方法用于获取指定 `struct` 对象中指定名称的元数据标签信息。

```
package main

import (
	"fmt"
	"github.com/gogf/gf/v2/util/gmeta"
)

func main() {
	type User struct {
		gmeta.Meta `orm:"user" db:"mysql"`
		Id         int
		Name       string
	}
	user := User{}
	fmt.Println(gmeta.Get(user, "orm").String())
	fmt.Println(gmeta.Get(user, "db").String())
}
```

执行后，终端输出：

```
user
mysql
```