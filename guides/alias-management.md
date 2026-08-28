# 别名管理

别名是密钥的可读名称，使您可以用有意义的名称代替 UUID 格式的 KeyId 来引用密钥 在API接口调用中使用别名来代替密钥UUID，方便您管理密钥。

## 别名格式

所有别名必须以 `alias/` 为前缀，例如：
- `alias/prod-db-key`
- `alias/payment-service`
- `alias/code-signing-2024`

## 别名规则

| 规则 | 说明 |
|------|------|
| 格式 | 必须以 `alias/` 开头，后缀长度 1–256 字符 |
| 唯一性 | 在同一实例（组织 + 可用区）内唯一，不区分大小写 |
| 单密钥上限 | 每个密钥最多绑定 64 个别名 |
| 目标密钥 | 只能为处于 `Enabled` 状态的密钥创建别名 |

## 在 API 中使用别名

所有接收 `KeyId` 参数的 API 都支持传入别名：

```json
{
  "KeyId": "alias/prod-db-key"
}
```

等价于：

```json
{
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc"
}
```

## 创建别名（CreateAlias）

为指定密钥创建别名。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| AliasName | string | 是 | 别名，格式 `alias/名称` |
| KeyId | string | 是 | 目标密钥 ID（须为激活状态） |

**请求示例**

```json
POST /ukms/create_alias

{
  "Action": "CreateAlias",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "AliasName": "alias/prod-database-key",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc"
}
```

**响应示例**

```json
{
  "RetCode": 0
}
```

## 更新别名（UpdateAlias）

将现有别名重新指向另一个密钥。原密钥解绑该别名，新密钥获得该别名。

**使用场景**：在密钥轮转/迁移时，将别名无缝切换到新密钥，下游使用别名的应用无需修改。

**请求示例**

```json
POST /ukms/update_alias

{
  "Action": "UpdateAlias",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "AliasName": "alias/prod-database-key",
  "KeyId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

> **说明**：如果新 KeyId 与当前指向的密钥相同，操作幂等（无错误返回）。

## 删除别名（DeleteAlias）

删除指定别名。删除别名后，密钥本身不受影响，仍可通过 KeyId 访问。

```json
POST /ukms/delete_alias

{
  "Action": "DeleteAlias",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "AliasName": "alias/prod-database-key"
}
```

## 列举别名（ListAliases）

分页列出实例下的所有别名，可选择按 KeyId 过滤。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| KeyId | string | 否 | 按密钥 ID 过滤（只返回该密钥的别名） |
| Offset | int | 否 | 起始偏移量，默认 0 |
| Limit | int | 否 | 返回数量，默认 100，最大 1000 |

**请求示例**

```json
POST /ukms/list_aliases

{
  "Action": "ListAliases",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "Offset": 0,
  "Limit": 20
}
```

**响应示例**

```json
{
  "RetCode": 0,
  "TotalCount": 3,
  "Aliases": [
    {
      "AliasName": "alias/prod-database-key",
      "TargetKeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
      "CreationDate": 1752634800,
      "LastUpdatedDate": 1752634800
    },
    {
      "AliasName": "alias/payment-key",
      "TargetKeyId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "CreationDate": 1752634900,
      "LastUpdatedDate": 1752720000
    }
  ]
}
```

## 按密钥 ID 列举别名（ListAliasesByKeyId）

查询指定密钥的所有别名（AWS KMS 兼容接口）。

```json
POST /ukms/list_aliases_by_key_id

{
  "Action": "ListAliasesByKeyId",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "Offset": 0,
  "Limit": 100
}
```

## 别名在密钥迁移中的应用

别名的一个重要用途是实现密钥的无缝迁移，应用程序完全无感知：

```
1. 旧密钥：key-v1（KeyId: fd601a9a...）绑定别名 alias/app-key
2. 创建新密钥：key-v2（KeyId: a1b2c3d4...）
3. 执行 UpdateAlias，将 alias/app-key 指向 key-v2
4. 应用程序使用 alias/app-key 发起请求，自动使用 key-v2
5. 历史密文仍可由 key-v1 解密
```
