## 非对称密钥相关接口

| 接口名称 | 接口功能 | 频率限制（次/秒） |
|-|-|-|
| [AsymmetricRsaDecrypt](/document/api/573/42142) | 非对称密钥RSA解密 | 200 |
| [AsymmetricSm2Decrypt](/document/api/573/42141) | 非对称密钥Sm2解密 | 200 |
| [GetPublicKey](/document/api/573/42140) | 获取非对称密钥的公钥 | 300 |
| [PostQuantumCryptoSign](/document/api/573/90740) | 后量子密码算法签名 | 20 |
| [PostQuantumCryptoVerify](/document/api/573/90739) | 后量子密码算法验签 | 20 |
| [SignByAsymmetricKey](/document/api/573/52065) | 签名 | 200 |
| [VerifyByAsymmetricKey](/document/api/573/52064) | 验证签名 | 200 |

## 白盒密钥相关接口

| 接口名称 | 接口功能 | 频率限制（次/秒） |
|-|-|-|
| [CreateWhiteBoxKey](/document/api/573/43443) | 创建白盒密钥 | 100 |
| [DeleteWhiteBoxKey](/document/api/573/43442) | 删除白盒密钥 | 200 |
| [DescribeWhiteBoxDecryptKey](/document/api/573/43441) | 获取白盒解密密钥 | 200 |
| [DescribeWhiteBoxDeviceFingerprints](/document/api/573/45806) | 获取指定密钥的设备指纹列表 | 100 |
| [DescribeWhiteBoxKey](/document/api/573/43440) | 展示白盒密钥的信息 | 200 |
| [DescribeWhiteBoxKeyDetails](/document/api/573/43439) | 获取白盒密钥列表 | 100 |
| [DescribeWhiteBoxServiceStatus](/document/api/573/43438) | 获取白盒密钥服务状态 | 200 |
| [DisableWhiteBoxKey](/document/api/573/43437) | 禁用白盒密钥 | 200 |
| [DisableWhiteBoxKeys](/document/api/573/43436) | 批量禁用白盒密钥 | 200 |
| [EnableWhiteBoxKey](/document/api/573/43435) | 启用白盒密钥 | 200 |
| [EnableWhiteBoxKeys](/document/api/573/43434) | 批量启用白盒密钥 | 200 |
| [EncryptByWhiteBox](/document/api/573/43433) | 使用白盒密钥进行加密 | 100 |
| [OverwriteWhiteBoxDeviceFingerprints](/document/api/573/45805) | 覆盖指定密钥的设备指纹信息 | 100 |

## 密钥相关接口

| 接口名称 | 接口功能 | 频率限制（次/秒） |
|-|-|-|
| [ArchiveKey](/document/api/573/47985) | 密钥归档 | 100 |
| [BindCloudResource](/document/api/573/46918) | 绑定密钥和云产品资源的使用关系 | 100 |
| [CancelDataKeyDeletion](/document/api/573/120162) | 取消计划删除数据密钥 | 20 |
| [CancelKeyArchive](/document/api/573/47984) | 取消密钥归档 | 100 |
| [CancelKeyDeletion](/document/api/573/35986) | 取消CMK计划删除操作 | 100 |
| [CreateKey](/document/api/573/34430) | 创建主密钥 | 100 |
| [Decrypt](/document/api/573/34429) | 解密 | 300 |
| [DeleteImportedKeyMaterial](/document/api/573/38498) | 删除导入的密钥材料 | 100 |
| [DescribeDataKey](/document/api/573/120161) | 获取数据密钥的详情 | 100 |
| [DescribeDataKeys](/document/api/573/120160) | 获取数据密钥的详情列表 | 100 |
| [DescribeKey](/document/api/573/34428) | 获取主密钥属性 | 100 |
| [DescribeKeys](/document/api/573/34427) | 获取多个主密钥属性 | 100 |
| [DisableDataKey](/document/api/573/120159) | 禁用数据密钥 | 100 |
| [DisableDataKeys](/document/api/573/120158) | 批量禁用数据密钥 | 100 |
| [DisableKey](/document/api/573/34426) | 禁用主密钥 | 100 |
| [DisableKeyRotation](/document/api/573/34425) | 禁止密钥轮换 | 100 |
| [DisableKeys](/document/api/573/34424) | 批量禁用主密钥 | 100 |
| [EnableDataKey](/document/api/573/120157) | 启用数据密钥 | 100 |
| [EnableDataKeys](/document/api/573/120156) | 批量启用数据密钥 | 100 |
| [EnableKey](/document/api/573/34423) | 启用主密钥 | 100 |
| [EnableKeyRotation](/document/api/573/34422) | 开启密钥轮换 | 100 |
| [EnableKeys](/document/api/573/34421) | 批量启动主密钥 | 100 |
| [Encrypt](/document/api/573/34420) | 加密 | 300 |
| [GenerateDataKey](/document/api/573/34419) | 生成数据密钥 | 300 |
| [GenerateRandom](/document/api/573/38693) | 随机数生成接口 | 300 |
| [GetDataKeyCiphertextBlob](/document/api/573/120155) | 下载数据密钥密文 | 100 |
| [GetDataKeyPlaintext](/document/api/573/120154) | 获取数据密钥明文 | 300 |
| [GetKeyRotationStatus](/document/api/573/34418) | 查询密钥轮换状态 | 100 |
| [GetParametersForImport](/document/api/573/38497) | 获取导入主密钥（CMK）材料的参数 | 100 |
| [GetRegions](/document/api/573/47795) | 获取可以提供KMS服务的地域列表 | 100 |
| [GetServiceStatus](/document/api/573/34417) | 查询服务状态 | 100 |
| [ImportDataKey](/document/api/573/120153) | 导入数据密钥 | 100 |
| [ImportKeyMaterial](/document/api/573/38496) | 导入密钥材料 | 100 |
| [ListAlgorithms](/document/api/573/42143) | 列出当前Region支持的加密方式 | 100 |
| [ListDataKeyDetail](/document/api/573/120152) | 获取数据密钥列表详情 | 100 |
| [ListDataKeys](/document/api/573/120151) | 获取数据密钥的列表 | 100 |
| [ListKeyDetail](/document/api/573/34416) | 获取主密钥列表详情 | 100 |
| [ListKeys](/document/api/573/34415) | 获取主密钥列表 | 100 |
| [PostQuantumCryptoDecrypt](/document/api/573/90742) | 后量子密码算法解密 | 20 |
| [PostQuantumCryptoEncrypt](/document/api/573/90741) | 后量子密码算法加密 | 20 |
| [ReEncrypt](/document/api/573/34414) | 密文刷新 | 100 |
| [RotateKey](/document/api/573/135527) | 立即轮转密钥 | 20 |
| [ScheduleDataKeyDeletion](/document/api/573/120150) | 计划删除数据密钥 | 20 |
| [ScheduleKeyDeletion](/document/api/573/35985) | CMK计划删除接口 | 100 |
| [UnbindCloudResource](/document/api/573/46917) | 解绑CMK和云资源的关联关系 | 100 |
| [UpdateAlias](/document/api/573/34413) | 修改别名 | 100 |
| [UpdateDataKeyDescription](/document/api/573/120149) | 修改数据密钥描述 | 20 |
| [UpdateDataKeyName](/document/api/573/120148) | 修改数据密钥名称 | 20 |
| [UpdateKeyDescription](/document/api/573/34412) | 修改主密钥描述信息 | 100 |

>! 以上给出的接口频率限制维度为`API + 接入地域 + 子账号`，有关限频更多说明参考：[API 频率限制说明](https://cloud.tencent.com/document/product/1278/109059)