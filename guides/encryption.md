# 加密与解密

UKMS 支持直接使用 CMK 对最大 4KB 的数据进行加密和解密，同时支持加密上下文（Encryption Context）提供额外的安全性。

## 加密（Encrypt）

使用 CMK 对数据进行加密。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| KeyId | string | 是 | 密钥 ID 或别名 |
| Plaintext | string | 是 | 要加密的数据，Base64 编码，最大 4096 字节 |
| EncryptionAlgorithm | string | 否 | 加密算法（RSA 密钥必填，对称密钥可省略） |
| EncryptionContext | object | 否 | 加密上下文键值对 |

**加密算法选择**

| 密钥类型 | 可用算法 | 默认值 |
|----------|----------|--------|
| SYMMETRIC_DEFAULT | `SYMMETRIC_DEFAULT` | 自动 |
| RSA_* | `RSAES_OAEP_SHA_1`、`RSAES_OAEP_SHA_256` | `RSAES_OAEP_SHA_256` |

**请求示例（对称加密）**

```json
POST /ukms/encrypt

{
  "Action": "Encrypt",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "Plaintext": "SGVsbG8gV29ybGQ=",
  "EncryptionContext": {
    "user": "alice",
    "purpose": "db-field"
  }
}
```

**请求示例（RSA 加密）**

```json
POST /ukms/encrypt

{
  "Action": "Encrypt",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "Plaintext": "c2VjcmV0LWRhdGE=",
  "EncryptionAlgorithm": "RSAES_OAEP_SHA_256"
}
```

**响应示例**

```json
{
  "RetCode": 0,
  "CiphertextBlob": "AQICAHg5z...",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "EncryptionAlgorithm": "SYMMETRIC_DEFAULT"
}
```

## 解密（Decrypt）

使用 CMK 对密文进行解密。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| KeyId | string | 条件 | 密钥 ID（非对称密钥必填，对称密钥可省略） |
| CiphertextBlob | string | 是 | 密文，Base64 编码（来自 Encrypt 响应） |
| EncryptionAlgorithm | string | 否 | 加密算法（与加密时一致） |
| EncryptionContext | object | 否 | 加密上下文（须与加密时完全一致） |

> **重要**：如果加密时使用了 `EncryptionContext`，解密时必须传入完全相同的上下文，否则解密将失败。

**请求示例（对称解密，不需要 KeyId）**

```json
POST /ukms/decrypt

{
  "Action": "Decrypt",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "CiphertextBlob": "AQICAHg5z...",
  "EncryptionContext": {
    "user": "alice",
    "purpose": "db-field"
  }
}
```

**请求示例（RSA 解密，需要 KeyId）**

```json
POST /ukms/decrypt

{
  "Action": "Decrypt",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "CiphertextBlob": "AQICAHg5z...",
  "EncryptionAlgorithm": "RSAES_OAEP_SHA_256"
}
```

**响应示例**

```json
{
  "RetCode": 0,
  "Plaintext": "SGVsbG8gV29ybGQ=",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "EncryptionAlgorithm": "SYMMETRIC_DEFAULT"
}
```

`Plaintext` 为 Base64 编码的解密结果，请在应用层进行 Base64 解码以得到原始数据。

## 重加密（ReEncrypt）

将一个 CMK 保护下的密文，重新加密为另一个 CMK 的密文，而无需先解密。这在密钥迁移场景中非常有用。

```json
POST /ukms/re_encrypt

{
  "Action": "ReEncrypt",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "CiphertextBlob": "AQICAHg5z...",
  "DestinationKeyId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

## 加密上下文

加密上下文（Encryption Context）是一组键值对，作为附加认证数据（AAD）参与加密运算。

**使用加密上下文的好处**：
- 加密时绑定上下文，解密时必须提供相同上下文才能成功
- 防止密文被挪用：即使密文泄露，没有正确的上下文也无法解密
- 提供细粒度的访问控制语义

**示例**：对用户 `alice` 的数据进行加密，并绑定用途标识

```json
{
  "EncryptionContext": {
    "userId": "alice",
    "dataType": "personal-info",
    "version": "v1"
  }
}
```

解密时必须提供完全相同的上下文（键值对完全匹配）。

## 限制说明

| 限制项 | 值 |
|--------|-----|
| 直接加密最大数据量 | 4096 字节（4KB） |
| 超过 4KB 的数据 | 请使用[信封加密](data-key.md)（数据密钥）模式 |
| Plaintext 编码 | 必须为 Base64 编码 |
| ECC 密钥 | 不支持加密/解密，仅支持签名/验签 |
| HMAC 密钥 | 不支持加密/解密，仅支持 MAC 操作 |

## 常见错误

| 错误码 | 含义 | 解决方法 |
|--------|------|---------|
| 密钥未启用 | 密钥处于 Disabled 或 PendingDeletion 状态 | 重新启用密钥或取消删除计划 |
| 算法不匹配 | 密钥类型与加密算法不兼容 | 检查密钥 KeySpec 并选择正确算法 |
| 数据过大 | Plaintext 超过 4KB | 改用数据密钥（信封加密）模式 |
| 上下文不匹配 | 解密时 EncryptionContext 与加密时不同 | 确保两次调用传入完全相同的上下文 |
