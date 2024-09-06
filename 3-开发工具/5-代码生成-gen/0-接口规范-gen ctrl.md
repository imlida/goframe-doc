---
title: '接口规范-gen ctrl'
sidebar_position: 0
---

该功能特性从 `v2.5` 版本开始提供。该命令目前仅支持 `HTTP` 接口开发， `GRPC` 部分请参考 `gen pb` 命令。未来会考虑 `HTTP` 及 `GRPC` 统一使用该命令生成控制器及 `SDK` 源代码。

## 基本介绍

### 解决痛点

在开发项目的时候，往往需要先根据业务需求和场景设计 `API` 接口，使用 `proto` 或者 `golang struct` 来设计 `API` 的输入和输出，随后再创建与 `API` 相对应的控制器实现，最后也有可能会提供 `SDK`（同为 `Golang` 语言条件下）供内/外部服务调用。在开发过程中会遇到以下痛点：

- **重复性的代码工作较繁琐**。在 `API` 中创建输入输出定义文件后还需要在控制器目录下创建对应的文件、创建对应的控制器初始化代码、从 `API` 代码中反复拷贝各个输入输出结构名称，在这过程重复性的操作比较繁琐。
- **API与控制器之间的关联没有可靠规范约束**。除了 `API` 有一定的命名约束外，控制器的创建和方法命名并没有约束，灵活度较高， `API` 的结构名称与控制器方法名称难以约束对应，当接口越来越多时会有一定维护成本。
- **团队开发多人协作时代码文件冲突概率大**。多人开发协作都往一个文件执行变更时，出现文件冲突的概率就会变大，团队协作开发中处理这种文件冲突的精力开销毫无意义。
- **缺少API的HTTP SDK自动生成工具**。当开发完 `API` 后，往往需要立即给内部或者外部调用，缺少便捷的 `SDK` 生成，需要手动来维护这部分 `SDK` 代码，那么对于调用端来说成本非常高。

### 命令特性

- 规范了 `API` 定义与控制器文件命名、控制器实现方法命名。
- 规范了 `API` 定义与控制器代码之间的关联关系，便于快速定位 `API` 实现。
- 根据 `API` 定义自动生成控制器接口、控制器初始化文件及代码、接口初始化代码。
- 根据 `API` 定义自动生成易于使用的 `HTTP SDK` 代码。该功能可配置，默认关闭。
- 支持 `File Watch` 自动化生成模式：当某个 `API` 结构定义文件发生变化时，自动增量化更新对应的控制器、 `SDK` 代码。

## 前置约定

### 重要的规范🔥

该命令的目的之一是规范化 `api` 代码的编写，那么我们应该有一些重要的规范需要了解（否则生成不了代码哦）：

- `api` 层的接口定义文件路径需要满足 `/api/模块/版本/定义文件.go`，例如： `/api/user/v1/user.go`、 `/api/user/v1/user_delete.go`、etc.
  - 这里的 **模块** 指的是 `API` 的模块划分，我们可以将 `API` 按照不同的 **业务属性** 进行拆分方便聚合维护。你也可以将模块认为是具体的业务资源。
  - 这里的 **版本** 通常使用 `v1`/ `v2`..这样的形式来定义，用以 `API` 兼容性的版本控制。当相同的 `API` 出现兼容性更新时，需要通过不同版本号来区分。默认使用 `v1` 来管理第一个版本。
  - 这里的 **定义文件** 指的是 `API` 的输入输出定义文件，通常每个 `API` 需要单独定义一个 `go` 文件来独立维护。当然也支持将多个 `API` 放到一个 `go` 文件中统一维护。
- `api` 定义的结构体名称需要满足 `操作+Req` 及 `操作+Res` 的命名方式。例如： `GetOneReq/GetOneRes`、 `GetListReq/GetListRes`、 `DeleteReq/DeleteRes`、etc.
  - 这里的操作是当前 `API` 模块的操作名称，通常对应 `CURD` 是： `Create`、 `Update`、 `GetList/GetOne`、 `Delete`。

以下是项目工程模板中的 `Hello` 接口示例：

![](/markdown/71be1e0ac8d8eaa7794a476086c110c2.png)

### 建议性的命名

我们对一些常用的接口定义做了一些建议性的命名，供大家参考：

| 操作名称 | 建议命名 | 备注 |
| --- | --- | --- |
| **查询列表** | `GetListReq/Res` | 通常是从数据库中分页查询数据记录 |
| **查询详情** | `GetOneReq/Res` | 通常接口需要传递主键条件，从数据库中查询记录详情 |
| **创建资源** | `CreateReq/Res` | 通常是往数据表中插入一条或多条数据记录 |
| **修改资源** | `UpdateReq/Res` | 通常是按照一定条件修改数据表中的一条或多条数据记录 |
| **删除资源** | `DeleteReq/Res` | 通常是按照一定条件删除数据表中的一条或多条数据记录 |

## 命令使用

该命令通过分析给定的 `api` 接口定义目录下的代码，自动生成对应的控制器/ `SDK Go` 代码文件。

### 手动模式

如果是手动执行命令行，直接在项目根目录下执行 `gf gen ctrl` 即可，她将完整扫描 `api` 接口定义目录，并生成对应代码。

```
$ gf gen ctrl -h
USAGE
    gf gen ctrl [OPTION]

OPTION
    -s, --srcFolder       source folder path to be parsed. default: api
    -d, --dstFolder       destination folder path storing automatically generated go files. default: internal/controller
    -w, --watchFile       used in file watcher, it re-generates go files only if given file is under srcFolder
    -k, --sdkPath         also generate SDK go files for api definitions to specified directory
    -v, --sdkStdVersion   use standard version prefix for generated sdk request path
    -n, --sdkNoV1         do not add version suffix for interface module name if version is v1
    -c, --clear           auto delete generated and unimplemented controller go files if api definitions are missing
    -m, --merge           generate all controller files into one go file by name of api definition source go file
    -h, --help            more information about this command

EXAMPLE
    gf gen ctrl

```

如果使用框架推荐的项目工程脚手架，并且系统安装了 `make` 工具，也可以使用 `make ctrl` 快捷指令。

参数说明：

| 名称 | 必须 | 默认值 | 含义 |
| --- | --- | --- | --- |
| `srcFolder` | 否 | `api` | 指向 `api` 接口定义文件目录地址 |
| `dstFolder` | 否 | `internal/controller` | 指向生成的控制器文件存放目录 |
| `watchFile` | 否 |  | 用在IDE的文件监控中，用于根据当文件发生变化时自动执行生成操作 |
| `sdkPath` | 否 |  | 如果需要生成 `HTTP SDK`，该参数用于指定生成的SDK代码目录存放路径 |
| `sdkStdVersion` | 否 | `false` | 生成的 `HTTP SDK` 是否使用标准的版本管理。标准的版本管理将自动根据 `API` 版本增加请求的路由前缀。例如 `v1` 版本的API将会自动增加 `/api/v1` 的请求路由前缀。 |
| `sdkNoV1` | 否 | `false` | 生成的 `HTTP SDK` 中，当接口为 `v1` 版本时，接口模块名称是否不带 `V1` 后缀。 |
| `clear` | 否 | `false` | 是否删除 `controller` 中与 `api` 层定义不存在的控制器接口文件。 |
| `merge` | 否 | `false` | **用以控制生成的 `ctrl` 控制器代码文件按照 `api` 层的文件生成，而不是默认按照 `api` 接口拆分为不同的接口实现文件。** |

### 自动模式（推荐）

如果您是使用的 `GolandIDE`，那么可以使用我们提供的配置文件： [watchers.xml](https://goframe.org/download/attachments/93880327/watchers.xml?version=1&modificationDate=1686817123230&api=v2)  自动监听代码文件修改时自动生成接口文件。使用方式，如下图：

![](/markdown/7d15b228b1ee57f8f34254a0413f4fc0.png)

## 使用示例

### 自动生成的接口定义文件

![](/markdown/636aedc34da9bad1f84545dcfbeb38e6.png)

### 自动生成的控制器代码文件

![](/markdown/cff8e2509fc89f6f4c4c0c82bb753334.png)

![](/markdown/e2219959e53c38a80d37254cd3e9e9de.png)

### 自动生成的 `HTTP SDK` 代码文件

![](/markdown/f2f5c6793e4aef5ea3c2004ce67edf7b.png)

![](/markdown/dd2dac2338ebf838ba317f64b32f5a5f.png)

## 常见问题

### 为什么每一个 `api` 接口生成一个 `controller` 文件而不是合并到一个 `controller` 文件中

![](https://goframe.org/download/attachments/93880327/image2023-6-15_16-29-12.png?version=1&modificationDate=1686817753666&api=v2)

当然，针对小型项目或者个人简单项目、一个 `api` 模块只有几个接口的项目而言，管理的方式并不会成为什么问题，可以根据个人喜好维护代码文件即可。我们这里以较复杂的业务项目，或者企业级项目，在一个 `api` 模块的接口比较多的场景来展开描述一下。

- 首先，开发 `api` 接口时，查找 `api` 接口实现更加清晰，而不是在一个动则上千行的代码文件中查找。
- 其次，在多人协作的项目中，如果多人同时修改同一个 `controller` 文件在版本管理中容易出现文件冲突。一个 `api` 接口对应一个 `controller` 实现文件的维护方式能最大减少代码协作时的文件冲突概率，大部分开发者也不希望花费自己宝贵的时间一次又一次地解决文件冲突上。
- 最后， `controller` 层的代码有它自身的职责：
  - 校验输入参数：客户端提交的参数都是不可信任的，大部分场景下都需要做数据校验。
  - 实现接口逻辑：直接在 `controller` 中实现接口逻辑，或者调用一个或多个 `service` 接口、第三方服务接口来实现接口逻辑。注意事项，不能在 `service` 层的接口中去实现 `api` 接口逻辑，因为 `api` 接口是与具体的业务场景绑定的，无法复用。💀 **大部分常见的错误是 `controller` 直接把请求透传给 `service` 接口来实现 `api` 接口逻辑，造成了 `controller` 看起来可有可无、 `service` 层的实现越来越重且无法复用。** 💀
  - 生成返回数据：组织内部产生的结果数据，生成接口定义的返回数据接口。
- 这些职责也就意味着 `controller` 的代码也是比较复杂，分开维护能减少开发者心智负担、易于清晰维护 `api` 接口实现逻辑。

**一些建议**：

如果一个 `api` 模块下的接口文件太多，建议将复杂的 `api` 模块进一步划分为子模块。这样可以对复杂的 `api` 模块进行解耦，也能通过多目录的方式来维护 `api` 接口定义和 `controller` 接口实现文件。目录结构会更清晰，更利于多人协作和版本管理。

_看完以上关于对此的设计后，如果您仍然想使用单源码文件来管理所有接口，可以参考 `merge` 参数。_

### 根据 `api` 模块生成对应的 `controller` 模块中为何存在一个空的 `go` 文件

**例如**：

![](/markdown/a5b84cce8be1a8b3d563102e7a4c81dd.png)

**说明**：

每个 `api` 模块会生成一个空的该模块 `controller` 下的 `go` 文件，该文件只会生成一次，用户可以在里面填充必要的预定义代码内容，例如，该模块 `controller` 内部使用的变量、常量、数据结构定义，或者包初始化 `init` 方法定义等等。 _我们提倡好的代码管理习惯，模块下的 **预定义内容** 尽量统一维护到该模块下以模块名称命名的 `go` 文件中（ `模块.go`），而不是分散到各个 `go` 文件中，以便于更好地维护代码。_

如果该 `controller` 目前没有需要自定义填充的代码内容，那么保留该文件为空即可，为未来预留扩展能力。