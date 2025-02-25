## 示例
我将在这里为你演示一个比较小的`ctp`核心
我会将所有的代码放在同一个文件中并加上注释， 所以请你们仔细阅读哦
话不多说上`code`

```python
from ctpbee import Action
from ctpbee import CtpBee, CtpbeeApi
from ctpbee.constant import *


class ActionMe(Action):
    def __init__(self, app):
        # 请记住要对父类进行实例化 你可以在此处保存一些上下文环境  使得开发逻辑隔离开来 让代码更加干净
        super().__init__(app)


class DoubleMA(CtpbeeApi):
    def __init__(self, name):
        super().__init__(name)
        self.instrument_set = ['rb2001.SHFE']

    def on_contract(self, contract: ContractData):
        if contract.local_symbol in self.instrument_set:
            self.action.subscribe(contract.local_symbol)

    def on_tick(self, tick: TickData) -> None:
        """
        tick行情触发的时候会调用此函数，你可以通过print来打印它查看详情
        """
        print(tick)


def create_app():
    """
    工厂函数 创建app变量并加载相关变量，最后返回
    """
    app = CtpBee("ctpbee", __name__, action_class=ActionMe)  # 在此处我们创建我们的核心App。
    info = {
        "CONNECT_INFO": {
            "userid": "089131",
            "password": "350888",
            "brokerid": "9999",
            "md_address": "tcp://218.202.237.33:10112",
            "td_address": "tcp://218.202.237.33:10102",
            "product_info": "",
            "appid": "simnow_client_test",
            "auth_code": "0000000000000000"
        },
        "INTERFACE": "ctp",  # 接口声明
        "TD_FUNC": True,  # 开启交易功能
    }
    app.config.from_mapping(info)
    double_ma = DoubleMA("double_ma")  # 创建我们的策略实例
    app.add_extension(double_ma)  # 将我们的策略通过app的add_extension接口加入进系统
    return app


if __name__ == "__main__":
    """
    通过ctpbee自己提供的24小时运行模块，让ctpbee能够自行运行程序，并在交易时间段和
    非交易时间段自动上下线
    """
    from ctpbee import hickey

    # 注意你在此处传入的是创建App的函数
    hickey.start_all(create_app)
```

