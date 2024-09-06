---
title: 'ORM查询-ScanAndCount'
sidebar_position: 4
---

## 基本介绍

在分页查询场景中，我们往往需要先调用 `Scan` 方法结合 `Limit/Page` 链式操作方法查询列表，随后再去掉 `Limit/Page` 链式操作方法查询总数量。这一过程较为繁琐，因此从 `v2.5.0` 版本开始，框架提供了 `ScanAndCount` 方法，用于简化分页查询的场景。

## 使用示例

示例代码来源于业务项目案例，仅供参考理解，无法独立运行。

使用传统的分页查询逻辑代码：

```
// GetList 获取实例的用户列表.
func (s sUserInfo) GetList(ctx context.Context, in model.UserInfoGetListInput) (items []entity.UserInfo, total int, err error) {
	items = make([]entity.UserInfo, 0)
	orm := dao.UserInfo.Ctx(ctx).OmitEmpty().Where(do.UserInfo{
		ResourceId: in.ResourceId,
		Status:     in.Statuses,
	})
	err = orm.Order(in.OrderBy, in.OrderDirection).Limit(in.Offset, in.Limit).Scan(&items)
	if err != nil {
		return
	}
	total, err = orm.Count()
	return
}
```

使用 `ScanAndCount` 方法实现分页查询：

```
// GetList 获取实例的用户列表.
func (s sUserInfo) GetList(ctx context.Context, in model.UserInfoGetListInput) (items []entity.UserInfo, total int, err error) {
	items = make([]entity.UserInfo, 0)
	err = dao.UserInfo.Ctx(ctx).OmitEmpty().
		Where(do.UserInfo{
			ResourceId: in.ResourceId,
			Status:     in.Statuses,
		}).
		Order(in.OrderBy, in.OrderDirection).
		Limit(in.Offset, in.Limit).
		ScanAndCount(&items, &total, true)
	return
}
```

## 注意事项

- 仅用于需要同时查询数据和总数量的场景，一般为分页场景。
- `ScanAndCount` 的第 `3` 个参数 `useFieldForCount` 表示是否在执行 `Count` 操作的时候将 `Fields` 作为 `Count` 参数，一般为 `true` 即可。传递 `false` 表示执行 `COUNT(1)` 查询总数量。