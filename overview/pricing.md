# 计费说明

## 计费模式

UKMS 采用**预付费**模式，购买时需选择计费周期和密钥配额。

| 计费类型 | 说明 |
|----------|------|
| `Month`（按月） | 购买指定月数的使用权限 |
| `Year`（按年） | 购买指定年数的使用权限，通常享有折扣 |

## 实例规格
专业版为包年包月付费模式，是物理密码机集群模式的云上密钥管理服务，提供了密钥创建、启用、禁用等生命周期管理，支持与UCloud 云产品无缝集成，实现透明加密。专业版支持1,000 个根密钥（CMK），具体计费详情如下：
<table>
<tr>
<td rowspan="1" colspan="1" >规格名称</td>

<td rowspan="1" colspan="1" >说明</td>

<td rowspan="1" colspan="1" >单价</td>

<td rowspan="1" colspan="1" >计费周期</td>
</tr>

<tr>
<td rowspan="1" colspan="1" >专业版</td>

<td rowspan="1" colspan="1" >支持管理1,000个 CMK</td>

<td rowspan="1" colspan="1" >7,699 元</td>

<td rowspan="1" colspan="1" >月</td>
</tr>

<tr>
<td rowspan="4" colspan="1" >API QPS限制</td>

<td rowspan="1" colspan="1" >Encrypt：300次/秒</td>

<td rowspan="1" colspan="1" >Decrypt：对称300次/秒，非对称200次/秒</td>

<td rowspan="1" colspan="1" >Sign：200次/秒</td>

<td rowspan="1" colspan="1" >Verify：200次/秒</td>

<td rowspan="1" colspan="1" >GetPublicKey：300次/秒</td>

<td rowspan="1" colspan="1" >GenerateRandom：300次/秒</td>

</tr>

</table>


> 扩展API QPS性能，请通过[联系我们](https://spt.ucloud.cn/)咨询。

## 软件实例与硬件实例

| 特性 | 软件实例（software） | 硬件实例（hardware） |
|------|---------------------|---------------------|
| 密钥保护 | 软件加密模块 | 物理 HSM |
| 安全级别 | 标准 | 金融/政务级别 |
| 适用场景 | 通用业务 | 高安全合规要求 |

## 续费

实例到期前可通过控制台续费。续费后实例恢复正常使用。

## 退费

预付费产品一经购买不支持退费。

## 价格变更

如价格调整，以购买时控制台显示的最新价格为准。

