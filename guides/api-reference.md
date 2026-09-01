# API 参考

本文档列出 UKMS 全部 API 接口，按功能分类组织。完整 API 文档请参见 [UKMS API](https://docs.ucloud.cn/api/ukms-api/)。

## 公共参数

所有 UKMS API 均需以下公共参数：

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| Action | string | **是** | API 指令名称 |
| PublicKey | string | **是** | 用户公钥，从 [控制台](https://console.ucloud.cn/uapi/apikey) 获取 |
| Signature | string | **是** | 根据公钥及 API 指令生成的签名，参见 [签名算法](https://docs.ucloud.cn/api/summary/signature) |
| Region | string | **是** | 地域，参见 [地域和可用区列表](https://docs.ucloud.cn/api/summary/regionlist) |
| ProjectId | string | 否 | 项目 ID，不填为默认项目，子账号必须填写 |

---

## 一、密钥管理

### CreateKey — 创建密钥

创建新的 CMK。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| ResourceId | string | **是** | UKMS 实例资源 ID |
| Description | string | 否 | 密钥描述，最多 8192 字符 |
| Alias | string | 否 | 密钥别名，格式 `alias/name` |
| KeySpec | string | 否 | 密钥规格，默认 `SYMMETRIC_DEFAULT`。可选值见下表 |
| KeyUsage | string | 否 | 密钥用途 |
| Origin | string | 否 | 密钥材料来源，默认 `UCLOUD_KMS`。`EXTERNAL`（BYOK）为规划值，当前传入会返回 100660 |
| DeletionProtection | string | 否 | 是否开启删除保护，默认 `false` |

**KeySpec 可选值**

`SYMMETRIC_DEFAULT`（AES_256）、`RSA_2048`、`RSA_3072`、`RSA_4096`、`ECC_NIST_P256`、`ECC_NIST_P384`、`ECC_NIST_P521`、`HMAC_256`、`HMAC_384`、`HMAC_512`

---

### DescribeKey — 查看主密钥

查看指定密钥的元数据。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

**响应 — KeyMetadata 数据模型**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| ProjectId | string | 密钥所属项目别名（org-xxx） |
| KeyId | string | 密钥资源长 ID |
| CreationDate | int | 创建时间（Unix 时间戳） |
| Enabled | string | 是否启用（true/false） |
| KeyUsage | array[string] | 密钥用途（ENCRYPT_DECRYPT、SIGN_VERIFY、GENERATE_VERIFY_MAC、KEY_AGREEMENT） |
| KeyState | string | 密钥状态（Enabled、Disabled、PendingDeletion、PendingImport、Unavailable） |
| Origin | string | 密钥来源（UCLOUD_KMS、EXTERNAL） |
| KeySpec | string | 密钥规格 |
| DeletionProtection | boolean | 是否开启删除保护 |
| KeyVersion | int | 当前密钥版本 |
| ResourceId | string | 密钥所属 UKMS 实例资源 ID |
| Description | string | 密钥描述 |
| DeletionDate | int | 计划删除时间（Unix 时间戳） |
| Arn | string | 密钥资源 ARN |
| OrganizationId | int | 密钥所属组织的数字 ID |

---

### ListKeys — 获取主密钥列表

查询用户的主密钥信息列表。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| ResourceId | string | 否 | UKMS 实例资源 ID |
| Alias | string | 否 | 按密钥 ID 或别名模糊过滤 |
| Offset | int | 否 | 列表起始位置偏移量 |
| Limit | int | 否 | 返回数据长度，最大 1000 |
| Status | string | 否 | 状态筛选：Active、Deactivated、PendingDeletion |
| OrderBy | string | 否 | 排序字段 |
| Sort | string | 否 | 排序方向，默认 desc |

**响应 — DEK 数据模型（每项）**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| ProjectId | string | 密钥所属项目的对外别名，格式为 org-xxx |
| KeyId | string | 对外主密钥 ID（ukms_key_info.key_id） |
| KeySpec | string | 密钥规格。取值：SYMMETRIC_DEFAULT、RSA_2048、RSA_3072、RSA_4096、ECC_NIST_P256、ECC_NIST_P384、ECC_NIST_P521、HMAC_256、HMAC_384、HMAC_512 |
| KeyUsage | array[string] | 按 KeySpec 派生的密钥用途。取值：ENCRYPT_DECRYPT、SIGN_VERIFY、GENERATE_VERIFY_MAC、KEY_AGREEMENT |
| Origin | string | 密钥来源，由 Origin 派生。取值：UCLOUD_KMS、EXTERNAL |
| Status | string | 数据库密钥状态。常见取值：Active、Deactivated、PendingDeletion |
| CreatedTime | int | 创建时间，Unix 时间戳 |
| UpdateTime | int | 更新时间，Unix 时间戳 |
| KeyRotationEnabled | boolean | 是否已开启自动轮转；未配置或已关闭均为 false |
| RotationPeriodInDays | int | 自动轮转周期（天）；未开启时为 0 |
| ResourceId | string | 密钥所属的 UKMS 实例资源 ID |
| Description | string | 密钥描述 |
| PlanDeleteTime | int | 计划删除时间，Unix 时间戳 |
| NextRotationDate | int | 下次自动轮转时间（Unix 时间戳，秒）；仅在已开启自动轮转时返回 |
| OrganizationId | int | 密钥所属组织的数字 ID，来源于密钥关联的资源交易记录 |

---

### EnableKey — 启用密钥

启用指定密钥。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### DisableKey — 禁用密钥

禁用指定密钥。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### UpdateKeyDescription — 更新主密钥描述信息

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| Description | string | **是** | 新的密钥描述，最多 8192 字符；空字符串表示清空描述 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### ScheduleKeyDeletion — 计划删除密钥

计划删除指定密钥。UCloud 管理密钥不能删除。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| DeleteDay | int | 否 | 删除等待天数，取值范围 7~30 天，默认 30 天 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### CancelKeyDeletion — 取消密钥计划删除

取消计划删除中的密钥。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

## 二、加密与解密

### Encrypt — 加密

使用指定密钥加密明文数据。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| Plaintext | string | **是** | 待加密明文，Base64 编码 |
| EncryptionContext | string | 否 | 加密上下文，JSON Object。该参数内容会记录在日志中，请勿传入敏感信息 |
| ResourceId | string | 否 | UKMS 实例资源 ID |
| EncryptionAlgorithm | string | 否 | 加密算法。可选值：`SYMMETRIC_DEFAULT`、`RSAES_OAEP_SHA_1`、`RSAES_OAEP_SHA_256`。对称密钥默认 `SYMMETRIC_DEFAULT`，RSA 默认 `RSAES_OAEP_SHA_256` |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| CiphertextBlob | string | 加密后的密文 |
| KeyId | string | 密钥资源长 ID |
| EncryptionAlgorithm | string | 实际使用的加密算法。取值：SYMMETRIC_DEFAULT、RSAES_OAEP_SHA_1、RSAES_OAEP_SHA_256 |

---

### Decrypt — 解密

解密使用对称或非对称 KMS 密钥加密的密文。非对称密钥解密时须指定 KeyId 和 EncryptionAlgorithm。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| CiphertextBlob | string | **是** | 待解密密文 |
| KeyId | string | 否 | 主密钥 KeyId；对称密钥可空（从 CiphertextBlob 自动识别），非对称必填 |
| EncryptionContext | string | 否 | 加密上下文，JSON Object。该参数内容会记录在日志中，请勿传入密码、密钥、令牌等敏感信息 |
| EncryptionAlgorithm | string | 否 | 解密算法。可选值：`SYMMETRIC_DEFAULT`、`RSAES_OAEP_SHA_1`、`RSAES_OAEP_SHA_256`。非对称密钥解密时必填 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| Plaintext | string | 解密后的明文，Base64 编码 |
| KeyId | string | 密钥资源长 ID |
| EncryptionAlgorithm | string | 实际使用的解密算法。取值：SYMMETRIC_DEFAULT、RSAES_OAEP_SHA_1、RSAES_OAEP_SHA_256 |

---

## 三、数据密钥

### GenerateDataKey — 创建数据密钥

生成数据密钥，返回明文和密文。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |
| KeySpec | string | 否 | 数据密钥规格。可选值：`AES_256`、`AES_128`，默认 `AES_256`。与 NumberOfBytes 二选一，同时填写时 NumberOfBytes 优先 |
| NumberOfBytes | int | 否 | 生成数据密钥的字节长度，取值范围 1~1024。与 KeySpec 二选一，同时填写时本字段优先 |
| EncryptionContext | string | 否 | 加密上下文，JSON Object |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| Plaintext | string | 数据密钥明文，Base64 编码 |
| CiphertextBlob | string | 加密后的数据密钥 |
| KeyId | string | 加密该数据密钥的密钥资源长 ID |

---

### GenerateDataKeyWithoutPlaintext — 创建数据密钥（无明文）

仅返回加密后的数据密钥，不返回明文。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |
| KeySpec | string | 否 | 数据密钥规格。可选值：`AES_256`、`AES_128`，默认 `AES_256` |
| NumberOfBytes | int | 否 | 生成数据密钥的字节长度，取值范围 1~1024 |
| EncryptionContext | string | 否 | 加密上下文，JSON Object |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| CiphertextBlob | string | 加密后的数据密钥 |
| KeyId | string | 加密该数据密钥的密钥资源长 ID |

---

### GenerateDataKeyPair — 创建数据密钥对

创建数据密钥对（公钥+私钥），私钥由指定 CMK 保护。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| KeyPairSpec | string | **是** | 密钥对类型 |
| EncryptionContext | string | 否 | 加密上下文 |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| KeyId | string | 用于加密私钥的 UKMS 密钥 |
| KeyPairSpec | string | 生成的数据密钥对类型 |
| PrivateKeyCiphertextBlob | string | 私钥的加密副本 |
| PrivateKeyPlaintext | string | 私钥的明文副本 |
| DataPublicKey | string | 公钥（明文） |

---

### GenerateDataKeyPairWithoutPlaintext — 创建数据密钥对（无明文返回）

创建数据密钥对，不返回私钥明文。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| KeyPairSpec | string | **是** | 指定生成的数据密钥对类型 |
| EncryptionContext | string | 否 | 指定加密私钥时使用的加密上下文 |

---

## 四、签名与验签

### Sign — 签名

使用非对称密钥对消息或消息摘要签名。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| SigningMessage | string | **是** | 待签名消息，Base64 编码，最大 4096 字节 |
| SigningAlgorithm | string | **是** | 签名算法。可选值见下表。须与密钥 KeySpec 匹配 |
| ResourceId | string | 否 | UKMS 实例资源 ID |
| MessageType | string | 否 | 消息类型：`RAW`（默认）或 `DIGEST` |

**SigningAlgorithm 可选值**

`RSASSA_PSS_SHA_256`、`RSASSA_PSS_SHA_384`、`RSASSA_PSS_SHA_512`、`RSASSA_PKCS1_V1_5_SHA_256`、`RSASSA_PKCS1_V1_5_SHA_384`、`RSASSA_PKCS1_V1_5_SHA_512`、`ECDSA_SHA_256`、`ECDSA_SHA_384`、`ECDSA_SHA_512`

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| SignatureResult | string | 签名结果，Base64 编码 |
| KeyId | string | 密钥资源长 ID |
| SigningAlgorithm | string | 实际使用的签名算法。取值：RSASSA_PSS_SHA_256、RSASSA_PSS_SHA_384、RSASSA_PSS_SHA_512、RSASSA_PKCS1_V1_5_SHA_256、RSASSA_PKCS1_V1_5_SHA_384、RSASSA_PKCS1_V1_5_SHA_512、ECDSA_SHA_256、ECDSA_SHA_384、ECDSA_SHA_512 |

---

### Verify — 验签

使用非对称密钥验证签名。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| SigningMessage | string | **是** | 待验签的消息或消息摘要，Base64 编码 |
| SignatureResult | string | **是** | 待验证的签名，Base64 编码 |
| SigningAlgorithm | string | **是** | 签名时使用的算法。可选值：RSASSA_PSS_SHA_256、RSASSA_PSS_SHA_384、RSASSA_PSS_SHA_512、RSASSA_PKCS1_V1_5_SHA_256、RSASSA_PKCS1_V1_5_SHA_384、RSASSA_PKCS1_V1_5_SHA_512、ECDSA_SHA_256、ECDSA_SHA_384、ECDSA_SHA_512；须与密钥 KeySpec 匹配 |
| ResourceId | string | 否 | UKMS 实例资源 ID |
| MessageType | string | 否 | 消息类型：`RAW`（默认）或 `DIGEST` |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| SignatureValid | boolean | 签名是否有效 |
| KeyId | string | 密钥资源长 ID |
| SigningAlgorithm | string | 使用的签名算法。取值：RSASSA_PSS_SHA_256、RSASSA_PSS_SHA_384、RSASSA_PSS_SHA_512、RSASSA_PKCS1_V1_5_SHA_256、RSASSA_PKCS1_V1_5_SHA_384、RSASSA_PKCS1_V1_5_SHA_512、ECDSA_SHA_256、ECDSA_SHA_384、ECDSA_SHA_512 |

> **注意**：验签失败时 `RetCode` 仍为 0，须通过 `SignatureValid` 字段判断结果。

---

## 五、HMAC

### GenerateMac — 生成 MAC

使用 HMAC 密钥为消息生成基于哈希的消息认证码（HMAC）。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| MacMessage | string | **是** | 待哈希的消息 |
| MacAlgorithm | string | **是** | 用于生成消息认证码的 MAC 算法 |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| Mac | string | 针对指定消息生成的 HMAC |
| MacAlgorithm | string | 用于生成 HMAC 的 MAC 算法 |

---

### VerifyMac — 验证 MAC 签名

验证指定消息的 HMAC。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| MacMessage | string | **是** | 用于验证的消息，须与生成 HMAC 时所用消息相同 |
| Mac | string | **是** | 要验证的 HMAC |
| MacAlgorithm | string | **是** | 验证过程中将使用的 MAC 算法，须与计算 HMAC 时相同 |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| MacValid | boolean | HMAC 是否已验证 |
| KeyId | string | 密钥资源长 ID |
| MacAlgorithm | string | 验证中使用的 MAC 算法 |

---

## 六、密钥轮转

### EnableKeyRotation — 开启密钥轮转

开启对称密钥自动轮转。仅 `SYMMETRIC_DEFAULT` 且 `Origin=UCLOUD_KMS` 的密钥支持轮转。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥 ID、ARN 或别名 |
| ResourceId | string | **是** | UKMS 实例资源 ID |
| RotationPeriodInDays | int | 否 | 轮转周期（天），取值范围 1~2560，默认 365 |

---

### DisableKeyRotation — 关闭密钥轮转

关闭密钥自动轮转。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### GetKeyRotationStatus — 获取密钥轮转状态

查询密钥自动轮转状态。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| KeyRotationEnabled | boolean | 是否开启自动轮转。取值：true、false |
| KeyId | string | 密钥资源长 ID |
| RotationPeriodInDays | int | 轮转周期（天）；未开启时返回 0 |
| NextRotationDate | int | 下次轮转时间（Unix 时间戳） |
| OnDemandRotationStartDate | int | 按需轮转开始时间（Unix 时间戳） |
| LastRotationDate | int | 上次轮转时间（Unix 时间戳） |

---

### RotateKeyOnDemand — 按需轮转密钥

立即触发一次密钥轮转。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

## 七、别名管理

### CreateAlias — 创建别名

为密钥创建别名。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| AliasName | string | **是** | 密钥别名，格式 `alias/name` |
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### UpdateAlias — 更新别名

将别名更新到另一个密钥。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| AliasName | string | **是** | 密钥别名，格式 `alias/name` |
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### DeleteAlias — 删除别名

删除密钥别名。删除别名后密钥本身不受影响。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| AliasName | string | **是** | 密钥别名，格式 `alias/name` |
| ResourceId | string | 否 | UKMS 实例资源 ID |

---

### ListAliases — 获取别名列表

获取 UKMS 实例下的别名列表。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| ResourceId | string | 否 | UKMS 实例资源 ID |
| KeyId | string | 否 | 按密钥资源长 ID 筛选 |
| Offset | int | 否 | 列表起始位置偏移量 |
| Limit | int | 否 | 返回数据长度 |
| Alias | string | 否 | 按完整别名（含 `alias/` 前缀）进行子串模糊匹配 |
| OrderBy | string | 否 | 排序字段 |
| Sort | string | 否 | 排序方向，默认 desc |

**响应 — AliasInfo 数据模型（每项）**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| AliasName | string | 别名（含 `alias/` 前缀） |
| TargetKeyId | string | 别名指向的密钥资源长 ID |
| CreationDate | int | 创建时间（Unix 时间戳） |
| LastUpdatedDate | int | 最后更新时间（Unix 时间戳） |

---

## 八、公钥与随机数

### GetPublicKey — 获取公钥

获取非对称密钥的公钥。

**请求参数**

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| KeyId | string | **是** | 密钥资源长 ID、ARN 或别名 |
| ResourceId | string | 否 | UKMS 实例资源 ID |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| KeyId | string | 密钥资源长 ID |
| KmsPublicKey | string | PEM 或 DER 编码的公钥 |
| KeySpec | string | 密钥规格 |
| KeyUsage | array[string] | 密钥用途。取值：ENCRYPT_DECRYPT、SIGN_VERIFY、GENERATE_VERIFY_MAC、KEY_AGREEMENT |
| SigningAlgorithms | array[string] | 支持的签名算法列表。取值：RSASSA_PSS_SHA_256、RSASSA_PSS_SHA_384、RSASSA_PSS_SHA_512、RSASSA_PKCS1_V1_5_SHA_256、RSASSA_PKCS1_V1_5_SHA_384、RSASSA_PKCS1_V1_5_SHA_512、ECDSA_SHA_256、ECDSA_SHA_384、ECDSA_SHA_512 |
| EncryptionAlgorithms | array[string] | 支持的加密算法列表。取值：RSAES_OAEP_SHA_1、RSAES_OAEP_SHA_256 |

---

### GenerateRandom — 生成随机数

生成密码学安全的随机字节序列。

| 参数名 | 类型 | 必填 | 说明 |
|:---|:---|:---|:---|
| NumberOfBytes | int | **是** | 随机数长度，最大 1024 |

**响应字段**

| 字段名 | 类型 | 说明 |
|:---|:---|:---|
| Plaintext | string | 随机字节串 |

---

## API 调用方式

您可以选择以下方式中的任意一种发起 API 请求：

- [UAPI 浏览器](https://console.ucloud.cn/uapi/ucloudapi)
- [CloudShell 云命令行](https://shell.ucloud.cn/)
- SDK（参见各语言 SDK 文档）

## 参考链接

- [UKMS API 完整文档](https://docs.ucloud.cn/api/ukms-api/)
- [UCloud API 签名算法](https://docs.ucloud.cn/api/summary/signature)
- [地域和可用区列表](https://docs.ucloud.cn/api/summary/regionlist)
