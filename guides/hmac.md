# HMAC 消息验证码

UKMS 支持使用 HMAC 密钥生成和验证消息验证码（MAC，Message Authentication Code），用于验证消息的完整性和真实性。

## 概述

HMAC（Hash-based Message Authentication Code）是一种使用密钥和哈希函数生成消息验证码的算法。与数字签名不同，HMAC 是对称操作——生成和验证使用同一个密钥。

## HMAC 密钥规格

| KeySpec | 哈希算法 | MAC 长度 |
|---------|----------|---------|
| `HMAC_256` | SHA-256 | 32 字节 |
| `HMAC_384` | SHA-384 | 48 字节 |
| `HMAC_512` | SHA-512 | 64 字节 |

## 创建 HMAC 密钥

```json
POST /ukms/create_key

{
  "Action": "CreateKey",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeySpec": "HMAC_256",
  "Description": "API 请求完整性验证密钥"
}
```

## 生成 MAC（GenerateMac）

使用 HMAC 密钥对消息生成 MAC 值。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| KeyId | string | 是 | HMAC 密钥 ID 或别名 |
| Message | string | 是 | 消息，Base64 编码 |
| MacAlgorithm | string | 是 | MAC 算法（须与密钥 KeySpec 匹配） |

**MacAlgorithm 与 KeySpec 对应关系**

| MacAlgorithm | 适用 KeySpec |
|-------------|-------------|
| `HMAC_SHA_256` | HMAC_256 |
| `HMAC_SHA_384` | HMAC_384 |
| `HMAC_SHA_512` | HMAC_512 |

**请求示例**

```json
POST /ukms/generate_mac

{
  "Action": "GenerateMac",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "Message": "dXNlcjppZD0xMjMmb3JkZXI9NDU2",
  "MacAlgorithm": "HMAC_SHA_256"
}
```

**响应示例**

```json
{
  "RetCode": 0,
  "Mac": "k5V2xT8...",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "MacAlgorithm": "HMAC_SHA_256"
}
```

`Mac` 为 Base64 编码的 HMAC 值。

## 验证 MAC（VerifyMac）

验证消息的 MAC 值是否有效。

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| ResourceId | string | 是 | UKMS 实例 ID |
| KeyId | string | 是 | HMAC 密钥 ID 或别名 |
| Message | string | 是 | 原始消息，Base64 编码 |
| Mac | string | 是 | 待验证的 MAC 值，Base64 编码 |
| MacAlgorithm | string | 是 | 生成 MAC 时使用的算法 |

**请求示例**

```json
POST /ukms/verify_mac

{
  "Action": "VerifyMac",
  "Region": "cn-bj2",
  "ResourceId": "ukms-xxxxxxxx",
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "Message": "dXNlcjppZD0xMjMmb3JkZXI9NDU2",
  "Mac": "k5V2xT8...",
  "MacAlgorithm": "HMAC_SHA_256"
}
```

**响应示例**

```json
{
  "RetCode": 0,
  "KeyId": "fd601a9a-c0c9-4dfb-a7ff-e5fd9f484ddc",
  "MacValid": true,
  "MacAlgorithm": "HMAC_SHA_256"
}
```

## 典型使用场景

### API 请求防篡改

服务端使用 UKMS HMAC 密钥对请求参数生成 MAC，将 MAC 附加在请求中发送给客户端。客户端在使用前调用 `VerifyMac` 验证请求未被篡改：

```
1. 服务端：GenerateMac(message=请求体, keyId=验证密钥)→ mac
2. 发送：{request_body, mac}
3. 接收方：VerifyMac(message=请求体, mac=收到的mac, keyId=验证密钥)→ 验证通过/失败
```

### 数据完整性校验

在写入数据库时为敏感字段生成 MAC 并一同存储，读取时验证 MAC，确保数据未被未授权修改。

## 限制说明

- HMAC 密钥只能用于 `GenerateMac` 和 `VerifyMac` 操作
- HMAC 密钥不支持加密/解密和签名/验签操作
- 生成和验证 MAC 必须使用同一个密钥
