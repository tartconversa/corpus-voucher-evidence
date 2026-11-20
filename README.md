# 语料券的可报销交付事实与跨计划核销

语料券资助基础事实，金额以人民币分为单位。 资料为本地合成样本。

## 源交付

`sources/` 存放 JSON 原件，文件名前两位是到达序号，同一序号内的各表属于同次交付。每个文件有 `intake`、`table`、`records`，记录 `id` 在所属表内唯一。`source-fields.json` 列出各表字段类型，空值用 `null`，空集合用 `[]`。业务时间按下方约定解释。

| 数据表 | 说明 |
| --- | --- |
| `purchases` | 语料包采购总控。 |
| `deliveries` | 几次交付的资料粒度和来源。 |
| `detail` | 收费行和资料范围，费用份额来自已确认采购明细。 |
| `licenses` | 用途授予及变动（有效期左闭右开）。 |
| `funding` | 共享计划明细上的分担限额。 |
| `claims` | 申请中一条拟核销费用。 |
| `receipts` | 资金方逐项回执，ref 对应源凭证或被冲回的到账行，at 为业务时间。 |
| `periods` | 账期界限和实际封定状态。 |

## 来源约定

文档的 work 对应内容凭证，digest 标识真实编码；报销申请以该企业、购买行、内容与同一允许用途的成本权益为约束。编码修订的 equivalent 指向供应商确认内容相同的现有成员，空值表示独立交付。license 中 count 不代表权利，明确 grant 或 restrict 对企业、用途和相应内容集合生效，replaces 表示凭证明确取代。可独立核销的原始金额只来自 detail 成本凭证，组合单据的 billed 是总控。license 为有限期时按 checked_at 核对。
funding 中 cap_a、cap_b 是政策对同一明确成本分担的上限，combined 是共同上限。不存在分担规则的同一金额，只允许由任一计划支付一次。accept 占用未付部分，pay 确认支付，reject 拒绝未付，reverse 是已付款返还。每张 receipt 针对申请中某一 detail，可多条分批，不得因同一支付号重复合计。sealed 的原账期仍可查询，后续返还登记到开放账期，引用原票据。

## 资料盘点

运行环境为 Python 3.11，盘点与资料校验仅使用标准库。盘点结果按到达批次显示表名及行数。

```sh
python source_inventory.py
python -m unittest discover -s tests -v
```
