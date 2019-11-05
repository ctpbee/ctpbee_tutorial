## 示例
我将在这里为你演示一个比较小的`ctp`核心
我会将所有的代码放在同一个文件中并加上注释， 所以请你们仔细阅读哦
话不多说上`code`

```python
from ctpbee import CtpBee, CtpbeeApi
from ctpbee.constant import *


class DoubleMA(CtpbeeApi):
    
    def on_tick(self, tick: TickData) -> None:
        """
        tick行情触发的时候会调用此函数，你可以通过print来打印它查看详情
        """
    
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
                self.on_3_min_bar(self, bar)
        """
        pass


def create_app():
    """
    工厂函数 创建app变量并加载相关变量，最后返回    
    """
    app = CtpBee("ctpbee", __name__) # 在此处我们创建我们的核心App。
    double_ma = DoubleMA("double_ma") # 创建我们的策略实例
    app.add_extension(double_ma) # 将我们的策略通过app的add_extension接口加入进系统
    return app       


if __name__ == "__main__":
    """
    通过ctpbee自己提供的24小时运行模块，让ctpbee能够自行运行程序，并在交易时间段和
    非交易时间段自动上下线
    """
    from ctpbee import hickey
    hickey.start_all(create_app)
   
```

