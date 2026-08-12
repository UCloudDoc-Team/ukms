# UCloud 密钥管理服务（UKMS）文档

密钥管理服务（UKMS）是 UCloud 提供的云端密钥管理平台，基于硬件安全模块（HSM）保障密钥安全，帮助您集中管理加密密钥，构建数据加密、数字签名和消息验证等安全能力。

---

## 目录

### 概览
- [产品简介](overview/产品简介.md)
- [基本概念](overview/基本概念.md)
- [产品优势](overview/产品优势.md)
- [应用场景](overview/应用场景.md)

### 购买与计费
- [计费说明](billing/billing.md)

### 使用指南
- [快速入门](guides/quickstart.md)
- [实例管理](guides/instance-management.md)
- [密钥管理](guides/key-management.md)
- [加密与解密](guides/encryption.md)
- [数字签名与验签](guides/signing.md)
- [HMAC 消息验证码](guides/hmac.md)
- [数据密钥](guides/data-key.md)
- [密钥轮转](guides/key-rotation.md)
- [别名管理](guides/alias-management.md)
- [访问令牌管理](guides/token-management.md)

### API 参考
- [API 概览](api/overview.md)
- [实例管理 API](api/instance-api.md)
- [密钥管理 API](api/key-api.md)
- [加解密 API](api/crypto-api.md)
- [签名验签 API](api/sign-api.md)
- [HMAC API](api/hmac-api.md)
- [数据密钥 API](api/data-key-api.md)
- [密钥轮转 API](api/rotation-api.md)
- [别名管理 API](api/alias-api.md)
- [令牌管理 API](api/token-api.md)

### 常见问题
- [常见问题解答](faq/faq.md)

---

## 功能特性概览

| 功能 | 说明 |
|------|------|
| 实例管理 | 创建、升级、删除 UKMS 实例，管理密钥配额 |
| 密钥管理 | 创建、禁用、启用、删除对称与非对称密钥 |
| 加密/解密 | 使用 CMK 直接加密/解密最大 4KB 数据 |
| 数字签名 | RSA/ECC 非对称密钥签名与验签 |
| HMAC | 使用 HMAC 密钥生成和验证消息验证码 |
| 数据密钥 | 生成数据密钥，实现信封加密 |
| 密钥轮转 | 自动/手动轮转对称密钥（轮转周期 90–2560 天） |
| 别名管理 | 为密钥创建易读别名，每个密钥最多 64 个别名 |
| 令牌管理 | 为 UKMS 实例生成 JWT 访问令牌 |

---

## 相关链接

- [快速入门](guides/quickstart.md)
- [核心概念](overview/concepts.md)
- [API 参考](api/overview.md)
- [常见问题](faq/faq.md)
