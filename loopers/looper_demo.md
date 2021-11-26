# 回测示例

在上面的描述中我们已经取得了标准的历史数据格式, 那么我们现在来实现一个标准的历史回测

```python
from datetime import datetime
from ctpbee import get_current_trade_day, CtpBee
from ctpbee import CtpbeeApi
from ctpbee.constant import TickData, OrderData, Offset, Direction, TradeData, Status, ContractData, BarData
from ctpbee.message import Mail

import pandas as pd

CODE = "rb2105.SHFE"


class Str(CtpbeeApi):
    def __init__(self):
        super().__init__("api")
        self.instrument_set = [CODE]

    def on_bar(self, tick: BarData) -> None:
        print(tick.close_price)

    def on_init(self, init: bool):
        print("新的一天开始了")


def run_looper_app():
    info = {
        "PATTERN": "looper",
        "LOOPER": {
            "initial_capital": 10000,
            "deal_pattern": "price",
            "margin_ratio": {
                CODE: 0.1,
            },
            "commission_ratio": {
                CODE: {
                    "close": 0.00003,
                    "close_today": 0.00003
                },
            },
            "size_map": {
                CODE: 10
            }
        }
    }
    app = CtpBee("looper", __name__)
    app.config.from_mapping(info)
    strategy = Str()
    data = pd.read_csv("rbdata.csv")  # 读取k线的csv
    data = list(data.to_dict(orient="index").values())
    for x in data:
        x["datetime"] = datetime.strptime(x["datetime"], "%Y-%m-%d %H:%M:%S")  # 转换时间为标准datetime格式
    app.add_data(data)  # 加载进去
    app.add_extension(strategy)
    app.start()
    result, trade = app.get_result(report=True, auto_open=True)
    print(result)


if __name__ == '__main__':
    run_looper_app()

```

[csv下载地址](https://wws.lanzous.com/iOQmCnfvs5c
)  密码: `54en`

### 载入数据

创建完App之后，我们便开始往容器里面添加数据, 
```
app.add_data(data)
```

注意:
> `history`数据应该是一个`[{}, {}, {}]`这样类型的python数据，你需要做一重转换处理, datetime数据它会自动进行转换, 当然仅针对特定格式 


### 添加策略
我们需要在此处添加标准形式的策略实例
和`ctpbee`的`CtpbeeApi`示例类似, 用户都需要继承`CtpbeeApi`，提供了`on_bar`、`on_tick`、`on_trade`等方法
他们的调用和实盘接口是无缝的


### 开始回测
调用run接口即可完成，注意，它是调用一个协程来完成回测计算的
```python
app.start()
```

### 获取回测结果
```python
result, trade_record = app.get_result()
```
如果你什么参数都不添加的话， 那么它将返回一个dataframe和成交单信息，
但是有两个可选参数可供你使用

- `report`， 是否生成HTML报告信息 
- `auto_open`， bool型参数，每次手动复制地址到浏览器都很烦？，当这个参数为True的时候，会自动调用默认浏览器打开页面


