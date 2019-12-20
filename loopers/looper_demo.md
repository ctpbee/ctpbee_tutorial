# 回测示例

在上面的描述中我们已经取得了标准的历史数据格式, 那么我们现在来实现一个标准的历史回测

> 注意： 现在的回测是简陋的

```python
# 假定这个history已经读取到了历史数据
history = []
params = {"looper":
             {"initial_capital": 100000,
              "commission": 0.005,
              "deal_pattern": "price",
              "size_map": {"ag1912.SHFE": 15},
              "today_commission": 0.005,
              "yesterday_commission": 0.02,
              "close_commission": 0.005,
              "slippage_sell": 0,
              "slippage_cover": 0,
              "slippage_buy": 0,
              "slippage_short": 0,
              "close_pattern": "yesterday",
              },
         "strategy": {}
}
    

from ctpbee import Vessel, LooperApi

class Looper(LooperApi):
    
    def on_bar(self, bar):
        pass

    def on_tick(self, tick):
        pass
        

ext = Looper("looper")

# 创建一个回测容器
vessel = Vessel()
vessel.add_data(history)
vessel.add_strategy(ext)

vessel.set_params(params)

vessel.run()
result = vessel.get_result()
```

在上述的代码中我们实现了简单的回测, 下面就来详细介绍设计到的API

我们通过`ctpbee`提供的Vessel类来创建一个容器
```python
from ctpbee import Vessel
vessel = Vessel()
```

### 载入数据

在你创建vessel这个回测容器之后,我们便开始往容器里面添加数据, 
```
vessel.add_data(history)
```

注意:
> `history`数据应该是一个`[{}, {}, {}]`这样类型的python数据，你需要做一重转换处理,datetime数据它会自动进行转换

 
### 添加策略
我们需要在此处添加标准形式的策略实例
和`ctpbee`的`CtpbeeApi`示例类似, 用户都需要继承`LooperApi`，提供了`on_bar`、`on_tick`、`on_trade`等方法
他们的调用和实盘接口是无缝的


### 开始回测
调用run接口即可完成，注意，它是调用一个协程来完成回测计算的
```python
vessel.run()
```

### 获取回测结果
```python
vessel.get_result()
```
如果你什么参数都不添加的话， 那么它将返回一个字典，
但是有两个可选参数可供你使用
- `report`， bool型参数，当为True，它会给你返回输出HTML的回测报告的本地文件地址
- `auto_open`， bool型参数，每次手动复制地址到浏览器都很烦？，当这个参数为True的时候，会自动调用默认浏览器打开页面




         