# 密钥轮转

密钥轮转是一种安全最佳实践，通过定期更换密钥材料，限制单个密钥版本被破解后的影响范围。UKMS 支持**自动轮转**和**按需手动轮转**两种方式。

## 轮转原理

UKMS 密钥轮转不会改变 KeyId，调用方无需修改任何代码：
- 轮转后系统生成新的底层密钥材料，KeyId 保持不变
- 新的加密操作使用新版密钥材料
- 旧版密钥材料仍被保留，用于解密之前加密的数据
- `KeyVersion` 字段递增，记录当前密钥版本

## 支持轮转的密钥条件

密钥轮转（自动或手动）必须同时满足：
- `KeySpec = SYMMETRIC_DEFAULT`（仅对称密钥支持轮转）
- `Origin = UCLOUD_KMS`（BYOK 密钥不支持轮转）
- 密钥处于 `Enabled` 状态

## 自动轮转

### 启用自动轮转

通过 `EnableKeyRotation` 接口开启密钥的定期自动轮转。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| KeyId | string | 是 | 密钥 ID 或别名 |
| RotationPeriodInDays | int | 否 | 轮转周期（天），范围 90–2560，默认 365 |

**请求示例**

```json
POST /ukms/enable_key_rotation

{
  "Action": "EnableKeyRotation",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "RotationPeriodInDays": 90
}
```

**响应示例**

```json
{
  "RetCode": 0
}
```

### 禁用自动轮转

通过 `DisableKeyRotation` 接口关闭自动轮转。禁用自动轮转不会影响已完成的轮转，也不会影响按需手动轮转的能力。

```json
POST /ukms/disable_key_rotation

{
  "Action": "DisableKeyRotation",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc"
}
```

### 查询轮转状态

通过 `GetKeyRotationStatus` 接口查询密钥的轮转配置和状态。

```json
POST /ukms/get_key_rotation_status

{
  "Action": "GetKeyRotationStatus",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc"
}
```

**响应字段**

| 字段 | 说明 |
|------|------|
| KeyRotationEnabled | 是否已开启自动轮转 |
| KeyId | 密钥 ID |
| RotationPeriodInDays | 自动轮转周期（天） |
| NextRotationDate | 下次自动轮转的预计时间（Unix 时间戳） |
| OnDemandRotationStartDate | 最近一次按需轮转的开始时间（Unix 时间戳） |

## 按需手动轮转

通过 `RotateKeyOnDemand` 接口立即触发一次密钥轮转，无论是否开启了自动轮转。

```json
POST /ukms/rotate_key_on_demand

{
  "Action": "RotateKeyOnDemand",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc"
}
```

**按需轮转特点**：
- 立即执行，不等待自动轮转周期
- 不影响自动轮转的配置和计划
- 同一密钥不支持并发轮转（系统内置锁保护）
- 轮转失败时自动回滚，保证数据一致性
- 轮转成功后密钥别名会自动同步更新

## 轮转后的行为

| 行为 | 说明 |
|------|------|
| KeyId 不变 | 应用程序无需修改密钥 ID |
| 新加密使用新密钥材料 | 轮转后的加密操作使用新版本密钥材料 |
| 旧密文仍可解密 | 旧版本密钥材料被保留，历史密文可正常解密 |
| KeyVersion 递增 | 通过 DescribeKey 可查询当前密钥版本号 |

## 轮转周期建议

| 场景 | 建议周期 |
|------|----------|
| 高安全要求（金融、政务） | 90–180 天 |
| 标准生产环境 | 365 天（默认） |
| 测试/开发环境 | 不限 |

## 自动轮转时间线

```
创建密钥 → 开启自动轮转（90天周期）
    |
    |──── 第 90 天：第 1 次轮转 ──── 第 180 天：第 2 次轮转 ──── ...
    |
    KeyVersion: 1              2                           3
```

## 注意事项

- 轮转操作期间（进行中）不支持对同一密钥再次触发轮转
- 如果在按需轮转执行过程中发生错误，系统会自动回滚，密钥材料不会处于不一致状态
- 对于 RSA、ECC、HMAC 密钥，不支持轮转操作
