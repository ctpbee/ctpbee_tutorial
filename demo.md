## 示例
我将在这里为你演示一个比较小的`ctp`核心
我会将所有的代码放在同一个文件中并加上注释， 所以请你们仔细阅读哦
话不多说上`code`

```python
from ctpbee import CtpBee, CtpbeeApi
from ctpbee.constant import *
from ctpbee import Action
from ctpbee import RiskLevel
from ctpbee import VLogger


class Logger(VLogger):
    def handler_record(self, record: dict):
        """ 处理日志接口"""
        print(record)


class ActionMe(Action):
    def __init__(self, app):
        # 请记住要对父类进行实例化
        super().__init__(app)
        # 通过add_risk_check接口添加风控
        self.add_risk_check(self.sell)


class RiskMe(RiskLevel):
    def before_sell(self, *args, **kwargs):
        """
        对sell调用进行事前检查,
        """
        # 在此打印sell传入的参数
        print(args)
        return True, args, kwargs

    def after_sell(self, result):
        """
        事后进行检查, 注意此函数只能被执行有限的时间
        """
        print(result)

    def realtime_check(self):
        """
        请务必重写此函数
        此函数一秒钟执行一次， 如果你需要执行多个周期的检查，可以自行添加变量
        """


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
        # 打印 tick 信息
        print(tick)

    def on_bar(self, bar: BarData) -> None:
        """
        当有bar线生成的时候会调用此函数， 你可以通过print来打印bar，但是你需要注意的是
        Note:
            多周期的bar都会触发这个接口。他们的周期会被存放在 bar.interval这个值，如果你想实现多周期的策略，
            请自行编写
            def on_3_min_bar(self, bar):
                pass

            然后在本函数中编写
            if bar.interval == 3:
                self.on_3_min_bar(bar)
        """
        pass


def create_app():
    """
    工厂函数 创建app变量并加载相关变量，最后返回
    """
    app = CtpBee("ctpbee", __name__, risk=RiskMe, action_class=ActionMe, logger_class=Logger)  # 在此处我们创建我们的核心App。
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

