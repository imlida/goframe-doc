---
title: '定时任务-gcron与gtimer'
sidebar_position: 3
---

## `gcron` 与 `gtimer` 区别

[定时任务-gcron](/docs/组件列表/系统相关/定时任务-gcron) 与 [定时器-gtimer](/docs/组件列表/系统相关/定时器-gtimer) 区别:

- `gtimer` 属于高性能模块，是框架核心模块，构建任何定时任务的基础，任何方法操作耗时均在 `纳秒` 级别。
- `gtimer` 可适用于任何的定时任务场景中，例如: TCP通信、游戏开发等场景。
- `gcron` 支持经典的 `crontab` 形式的定时任务语法，最小时间设定间隔为 `秒`。
- `gcron` 底层实现基于 `gtimer`。

| 相似模块 | 说明 | 性能 | 类Linux Crontab模式 | 底层实现 |
| --- | --- | --- | --- | --- |
| [定时任务-gcron](/docs/组件列表/系统相关/定时任务-gcron) | 定时任务。<br />较上层封装，时间刻度以自然秒为单位。 | 一般 | 支持 | 基于 `gtimer` |
| [定时器-gtimer](/docs/组件列表/系统相关/定时器-gtimer) | 定时器。<br />底层组件，时间刻度以时间槽为单位（时间槽可自定义）。 | 高效 | 不支持 | 基于 `PriorityQueue` 数据结构自实现 |