---
name: graphql-schema
description: GraphQL 查询、变异和代码生成模式。在创建 GraphQL 操作、使用 Apollo Client 或生成类型时使用。
---

# GraphQL 模式

## 核心规则

1. **永远不要内联 `gql` 字面量** - 创建 `.gql` 文件
2. **创建/修改 `.gql` 文件后总是运行代码生成**
3. **总是为变异添加 `onError` 处理器**
4. **使用生成的 hooks** - 永远不要编写原始 Apollo hooks

## 文件结构

```
src/
├── components/
│   └── ItemList/
│       ├── ItemList.tsx
│       ├── GetItems.gql           # 查询定义
│       └── GetItems.generated.ts  # 自动生成（不要编辑）
└── graphql/
    └── mutations/
        └── CreateItem.gql         # 共享变异
```

## 创建查询

### 步骤 1：创建 .gql 文件

```graphql
# src/components/ItemList/GetItems.gql
query GetItems($limit: Int, $offset: Int) {
  items(limit: $limit, offset: $offset) {
    id
    name
    description
    createdAt
  }
}
```

### 步骤 2：运行代码生成

```bash
npm run gql:typegen
```

### 步骤 3：导入并使用生成的 hook

```typescript
import { useGetItemsQuery } from './GetItems.generated';

const ItemList = () => {
  const { data, loading, error, refetch } = useGetItemsQuery({
    variables: { limit: 20, offset: 0 },
  });

  if (error) return <ErrorState error={error} onRetry={refetch} />;
  if (loading && !data) return <LoadingSkeleton />;
  if (!data?.items.length) return <EmptyState />;

  return <List items={data.items} />;
};
```

## 创建变异

### 步骤 1：创建 .gql 文件

```graphql
# src/graphql/mutations/CreateItem.gql
mutation CreateItem($input: CreateItemInput!) {
  createItem(input: $input) {
    id
    name
    description
  }
}
```

### 步骤 2：运行代码生成

```bash
npm run gql:typegen
```

### 步骤 3：与必需的错误处理一起使用

```typescript
import { useCreateItemMutation } from 'graphql/mutations/CreateItem.generated';

const CreateItemForm = () => {
  const [createItem, { loading }] = useCreateItemMutation({
    // 成功处理
    onCompleted: (data) => {
      toast.success({ title: '项目已创建' });
      navigation.goBack();
    },
    // 错误处理是必需的
    onError: (error) => {
      console.error('创建项目失败:', error);
      toast.error({ title: '创建项目失败' });
    },
    // 缓存更新
    update: (cache, { data }) => {
      if (data?.createItem) {
        cache.modify({
          fields: {
            items: (existing = []) => [...existing, data.createItem],
          },
        });
      }
    },
  });

  return (
    <Button
      onPress={() => createItem({ variables: { input: formValues } })}
      isDisabled={!isValid || loading}
      isLoading={loading}
    >
      创建
    </Button>
  );
};
```

## 变异 UI 要求

**关键：每个变异触发必须：**

1. **变异期间禁用** - 防止重复点击
2. **显示加载状态** - 视觉反馈
3. **有 onError 处理器** - 用户知道失败了
4. **显示成功反馈** - 用户知道成功了

```typescript
// 正确 - 完整的变异模式
const [submit, { loading }] = useSubmitMutation({
  onError: (error) => {
    console.error('提交失败:', error);
    toast.error({ title: '保存失败' });
  },
  onCompleted: () => {
    toast.success({ title: '已保存' });
  },
});

<Button
  onPress={handleSubmit}
  isDisabled={!isValid || loading}
  isLoading={loading}
>
  提交
</Button>
```

## 查询选项

### 获取策略

| 策略 | 使用时机 |
|--------|----------|
| `cache-first` | 数据很少更改 |
| `cache-and-network` | 需要快速+最新（默认） |
| `network-only` | 总是需要最新 |
| `no-cache` | 从不缓存（很少） |

### 常用选项

```typescript
useGetItemsQuery({
  variables: { id: itemId },

  // 获取策略
  fetchPolicy: 'cache-and-network',

  // 网络状态更改时重新渲染
  notifyOnNetworkStatusChange: true,

  // 条件不满足时跳过
  skip: !itemId,

  // 轮询更新
  pollInterval: 30000,
});
```

## 乐观更新

用于即时 UI 反馈：

```typescript
const [toggleFavorite] = useToggleFavoriteMutation({
  optimisticResponse: {
    toggleFavorite: {
      __typename: 'Item',
      id: itemId,
      isFavorite: !currentState,
    },
  },
  onError: (error) => {
    // 自动回滚
    console.error('切换收藏失败:', error);
    toast.error({ title: '更新失败' });
  },
});
```

### 何时不使用乐观更新

- 可能无法通过验证的操作
- 服务器生成值的操作
- 破坏性操作（删除）
- 影响其他用户的操作

## 片段

用于可重用的字段选择：

```graphql
# src/graphql/fragments/ItemFields.gql
fragment ItemFields on Item {
  id
  name
  description
  createdAt
  updatedAt
}
```

在查询中使用：

```graphql
query GetItems {
  items {
    ...ItemFields
  }
}
```

## 反模式

```typescript
// 错误 - 内联 gql
const GET_ITEMS = gql`
  query GetItems { items { id } }
`;

// 正确 - 使用 .gql 文件 + 生成的 hook
import { useGetItemsQuery } from './GetItems.generated';


// 错误 - 无错误处理器
const [mutate] = useMutation(MUTATION);

// 正确 - 总是处理错误
const [mutate] = useMutation(MUTATION, {
  onError: (error) => {
    console.error('变异失败:', error);
    toast.error({ title: '操作失败' });
  },
});


// 错误 - 变异期间按钮未禁用
<Button onPress={submit}>提交</Button>

// 正确 - 禁用并显示加载状态
<Button onPress={submit} isDisabled={loading} isLoading={loading}>
  提交
</Button>
```

## 代码生成命令

```bash
# 从 .gql 文件生成类型
npm run gql:typegen

# 下载 schema + 生成类型
npm run sync-types
```

## 与其他技能的集成

- **react-ui-patterns**: 查询的加载/错误/空状态
- **testing-patterns**: 在测试中模拟生成的 hooks
- **formik-patterns**: 变异提交模式