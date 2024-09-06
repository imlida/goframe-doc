---
title: 'UDP组件-工具方法'
sidebar_position: 1
---

`gudp` 模块也提供了一些常用的工具方法。

**使用方式**：

```
import "github.com/gogf/gf/v2/net/gudp"
```

**接口文档**：

[https://pkg.go.dev/github.com/gogf/gf/v2/net/gudp](https://pkg.go.dev/github.com/gogf/gf/v2/net/gudp)

```
func Checksum(buffer []byte) uint32
func NewNetConn(raddr string, laddr ...string) (*net.UDPConn, error)
func Send(addr string, data []byte, retry ...Retry) error
func SendPkg(addr string, data []byte, retry ...Retry) error
func SendPkgWithTimeout(addr string, data []byte, timeout time.Duration, retry ...Retry) error
func SendRecv(addr string, data []byte, receive int, retry ...Retry) ([]byte, error)
func SendRecvPkg(addr string, data []byte, retry ...Retry) ([]byte, error)
func SendRecvPkgWithTimeout(addr string, data []byte, timeout time.Duration, retry ...Retry) ([]byte, error)
```

`gudp` 的工具相对比较简单。

1. 其中， `NewNetConn` 方法用于创建标准库的 `net.UDPConn` 通信对象。
2. `Send` 与 `SendRecv` 用于根据给定的 `UDP Server` 地址直接地进行 `UDP` 通信，数据写入及读取。
3. `*Pkg` 方法用于简便的简单包协议数据传输。