# 数据结构

> 此处描述了ctpbee里面的数据结构，敬请食用～

## TickData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|  ask_price_1  |  买一价   |  ask_price_2  |   买二价  |
|  ask_price_3  |  买三价   |  ask_price_4  |   买四价  |
|  ask_price_5  |  买五价   |  ask_volume_1 |   买一量  |
|  ask_volume_2 |  买二量   |  ask_volume_3 |   买三量  |
|  ask_volume_4 |  买四量   |  ask_volume_5 |   买五量  |
| average_price |   均价    |  bid_price_1  |   卖一价  |
|  bid_price_2  |  卖二价   |  bid_price_3  |   卖三价  |
|  bid_price_4  |  卖四价   |  bid_price_5  |   卖五价  |
|  bid_volume_1 |  卖一量   |  bid_volume_2 |   卖二量  |
|  bid_volume_3 |  卖三量   |  bid_volume_4 |   卖四量  |
|  bid_volume_5 |  卖五量   |   high_price  |   最高价  |
|   last_price  |  最新价   |  last_volume  |          |
|   limit_down  |  涨停     |    limit_up   |   跌停    |
|   low_price   |  最低价   |      name     |   中文名  |
| open_interest |  持仓量   |   open_price  |   开盘价  |
|   pre_close   |昨日收盘价|pre_settlement_price| 昨日结算价 |
|     volume    |  成交量  |      pre_open_interest   |    昨日持仓量       |

## BarData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|  close_price  |    收盘价 |   high_price  |  最高价   |
|    interval   |  周期间隔 |   low_price   |  最低价   |
|   open_price  |  开盘价格  |     volume    |k线内成交量 |

## OrderData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|  volume       |   发单量  |   direction   |  买卖方向 |
| local_order_id|本地发单id |     offset    |   开平  |
|     price     |    价格   |     status    |   状态    |
|      time     |   时间    |     traded    |   是否已经成交    |
|      type     |   价格类型    |         |          |

## TradeData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|   direction   |   买卖方向  | local_order_id|   本地发单id   |
| local_trade_id|    本地成交id  |     offset    |     开平     |
|     price     |     价格  |      time     |    时间    |
|     volume    |    成交量   |               |          |

## PositionData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|     frozen    |    冻结   |local_position_id|   持仓id   |
|      pnl      |    结算盈亏   |     price     |   持仓均价    |
|     volume    |    持仓总量   |   yd_volume   |    昨日持仓量    |
|     open_price    |    仓位持仓成本   |   float_pnl   |    浮动盈亏    |

## AccountData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|    balance    |     余额  |     frozen    |     冻结   |
|local_account_id|     本地账户id    |   available    |   可用资金  |

## ContractData

| 字段名称 | 解释说明 | 字段名称 | 解释说明|
| :-----  | :----:  | :----  | :----: |
|   min_volume  |              |  net_position |          |
| option_expiry |    到期日     | option_strike |    执行价  |
|  option_type  |    期权类型   |option_underlying|   基础商品代码     |
| stop_supported|              |      pricetick    |   最小变动价位       |
| product | 产品类型 |   size   |  合约乘数     |
| name |   合约名称 |  exchange |  交易所 |  

---