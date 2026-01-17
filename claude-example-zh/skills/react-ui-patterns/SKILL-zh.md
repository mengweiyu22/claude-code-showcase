---
name: react-ui-patterns
description: 现代 React UI 模式，用于加载状态、错误处理和数据获取。在构建 UI 组件、处理异步数据或管理 UI 状态时使用。
---

# React UI 模式

## 核心原则

1. **永远不要显示过时的 UI** - 只在真正加载时显示加载指示器
2. **总是暴露错误** - 用户必须知道什么时候出错了
3. **乐观更新** - 让 UI 感觉即时的
4. **渐进式披露** - 在数据可用时显示内容
5. **优雅降级** - 部分数据比没有数据好

## 加载状态模式

### 黄金法则

**只在没有数据可显示时显示加载指示器。**

```typescript
// 正确 - 只在没有数据时显示加载
const { data, loading, error } = useGetItemsQuery();

if (error) return <ErrorState error={error} onRetry={refetch} />;
if (loading && !data) return <LoadingState />;
if (!data?.items.length) return <EmptyState />;

return <ItemList items={data.items} />;
```

```typescript
// 错误 - 即使有缓存数据也显示加载器
if (loading) return <LoadingState />; // 重新获取时会闪烁！
```

### 加载状态决策树

```
是否有错误？
  → 是：显示带有重试选项的错误状态
  → 否：继续

是否正在加载且我们没有数据？
  → 是：显示加载指示器（旋转器/骨架）
  → 否：继续

我们有数据吗？
  → 是，有项目：显示数据
  → 是，但为空：显示空状态
  → 否：显示加载（后备）
```

### 骨架 vs 旋转器

| 使用骨架时 | 使用旋转器时 |
|-------------------|------------------|
| 已知的内容形状 | 未知的内容形状 |
| 列表/卡片布局 | 模态操作 |
| 初始页面加载 | 按钮提交 |
| 内容占位符 | 内联操作 |

## 错误处理模式

### 错误处理层次结构

```
1. 内联错误（字段级）→ 表单验证错误
2. Toast 通知 → 可恢复错误，用户可以重试
3. 错误横幅 → 页面级错误，数据仍可部分使用
4. 完整错误屏幕 → 不可恢复，需要用户操作
```

### 总是显示错误

**关键：永远不要静默吞咽错误。**

```typescript
// 正确 - 错误总是暴露给用户
const [createItem, { loading }] = useCreateItemMutation({
  onCompleted: () => {
    toast.success({ title: '项目已创建' });
  },
  onError: (error) => {
    console.error('创建项目失败:', error);
    toast.error({ title: '创建项目失败' });
  },
});

// 错误 - 错误被静默捕获，用户不知道
const [createItem] = useCreateItemMutation({
  onError: (error) => {
    console.error(error); // 用户看不到任何东西！
  },
});
```

### 错误状态组件模式

```typescript
interface ErrorStateProps {
  error: Error;
  onRetry?: () => void;
  title?: string;
}

const ErrorState = ({ error, onRetry, title }: ErrorStateProps) => (
  <div className="error-state">
    <Icon name="exclamation-circle" />
    <h3>{title ?? '出错了'}</h3>
    <p>{error.message}</p>
    {onRetry && (
      <Button onClick={onRetry}>重试</Button>
    )}
  </div>
);
```

## 按钮状态模式

### 按钮加载状态

```tsx
<Button
  onClick={handleSubmit}
  isLoading={isSubmitting}
  disabled={!isValid || isSubmitting}
>
  提交
</Button>
```

### 操作期间禁用

**关键：总是在异步操作期间禁用触发器。**

```tsx
// 正确 - 加载时按钮禁用
<Button
  disabled={isSubmitting}
  isLoading={isSubmitting}
  onClick={handleSubmit}
>
  提交
</Button>

// 错误 - 用户可以多次点击
<Button onClick={handleSubmit}>
  {isSubmitting ? '提交中...' : '提交'}
</Button>
```

## 空状态

### 空状态要求

每个列表/集合必须有空状态：

```tsx
// 错误 - 无空状态
return <FlatList data={items} />;

// 正确 - 明确的空状态
return (
  <FlatList
    data={items}
    ListEmptyComponent={<EmptyState />}
  />
);
```

### 上下文空状态

```tsx
// 搜索无结果
<EmptyState
  icon="search"
  title="未找到结果"
  description="尝试不同的搜索词"
/>

// 还没有项目
<EmptyState
  icon="plus-circle"
  title="还没有项目"
  description="创建您的第一个项目"
  action={{ label: '创建项目', onClick: handleCreate }}
/>
```

## 表单提交模式

```tsx
const MyForm = () => {
  const [submit, { loading }] = useSubmitMutation({
    onCompleted: handleSuccess,
    onError: handleError,
  });

  const handleSubmit = async () => {
    if (!isValid) {
      toast.error({ title: '请修复错误' });
      return;
    }
    await submit({ variables: { input: values } });
  };

  return (
    <form>
      <Input
        value={values.name}
        onChange={handleChange('name')}
        error={touched.name ? errors.name : undefined}
      />
      <Button
        type="submit"
        onClick={handleSubmit}
        disabled={!isValid || loading}
        isLoading={loading}
      >
        提交
      </Button>
    </form>
  );
};
```

## 反模式

### 加载状态

```typescript
// 错误 - 有数据时显示旋转器（导致闪烁）
if (loading) return <Spinner />;

// 正确 - 只在没有数据时显示加载
if (loading && !data) return <Spinner />;
```

### 错误处理

```typescript
// 错误 - 错误被吞咽
try {
  await mutation();
} catch (e) {
  console.log(e); // 用户不知道！
}

// 正确 - 错误被暴露
onError: (error) => {
  console.error('操作失败:', error);
  toast.error({ title: '操作失败' });
}
```

### 按钮状态

```typescript
// 错误 - 提交时按钮未禁用
<Button onClick={submit}>提交</Button>

// 正确 - 禁用并显示加载状态
<Button onClick={submit} disabled={loading} isLoading={loading}>
  提交
</Button>
```

## 清单

完成任何 UI 组件前：

**UI 状态：**
- [ ] 错误状态已处理并显示给用户
- [ ] 只在没有数据时显示加载状态
- [ ] 为集合提供空状态
- [ ] 异步操作期间按钮禁用
- [ ] 适当的时候按钮显示加载指示器

**数据和变异：**
- [ ] 变异有 onError 处理器
- [ ] 所有用户操作都有反馈（toast/视觉）

## 与其他技能的集成

- **graphql-schema**: 使用带有正确错误处理的变异模式
- **testing-patterns**: 测试所有 UI 状态（加载、错误、空、成功）
- **formik-patterns**: 应用表单提交模式