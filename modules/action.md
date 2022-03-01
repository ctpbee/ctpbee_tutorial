## 操作模块(Action module)

前面说到ctpbee的设计充斥了模块和层的概念， 其实这个理解算是有一点模糊。需要你细心体会。

``模块提供了默认的初始化模板， 可以被继承进行重写， 从设计角度而言, `Action`被定义为所有策略都可以被共同访问的功能模块， 同时共享一个实例变量， 此项功能在于使得代码更加优雅。
参阅以下文档或者[源代码](https://github.com/ctpbee/ctpbee/blob/master/ctpbee/level.py#L19)

### 详细功能

##### 导入

```python
from ctpbee import Action
```

这当然很简单， 不是吗

##### 扩展

如果你不需要添加任何自定义的功能，那么请直接看下面的API， 我现在来为你介绍如何扩展Action模块

```python
from ctpbee import Action


class ActionMe(Action):
    def __init__(self, app):
        super().__init__(app)

    # 尝试扩展一个操作 
    def operation(self, *args, **kwargs):
        pass

```

上述我们其实就扩展了一个操作，但是我们如何把他载入到系统中去以及如何调用相信你还是一头雾水。 因为杆子是提前创建好的，所以我们需要在app创建的时候将其传入

```python
from ctpbee import CtpBee

app = CtpBee("demo", __name__, action_class=ActionMe)
```

通过上述代码我们就可以讲`ActionMe`载入到核心App中，那我们在策略中如何进行使用呢？

```python
from ctpbee import CtpbeeApi
from ctpbee.constant import BarData, TickData


class Demo(CtpbeeApi):

    def on_bar(self, bar: BarData) -> None:
        self.action.sell(bar.high_price, volume=1, origin=bar)

    def on_tick(self, tick: TickData) -> None:
        pass
```

诚如你所见，策略会有一个属性名字叫做action,通过它，你就可以访问到操作模块，等k线进来进行了 **平空一手** 的操作。 同时你也可以通过`self.action.operation()`调用到你自己创建的函数
。通过这样的方式就可以实现 `一次编写->所有策略都可以进行调用`

#### API

为了给你们提供一些好用的函数，ctpbee特意为你们写了一些API，他们分为两种，一种是底层API， 一种是上层API(应用API).

###### 底层API

诚如所见，底层API是负责更加底层的一点事情,比如说发单操作，下面我给出一个函数包含函数前面的列表以及如何使用的方法

- send_order 发单

```python

def send_order(self, order):
    """
    接受一个标准格式的发单请求， 返回报单本地id
    """
    pass

```

- cancel_order 撤单

```python
def cancel_order(self, cancel_req):
    """
    接受一个请求， 返回成功与否（1/0）
    """
    pass
```

###### 高层API

- `buy/buy_open`   **买多**

```python
from ctpbee.constant import *


def buy(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
        stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现买多的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    """


def buy_open(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
             stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现买多的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    """
```

- `short/sell_open` **卖空**

```python
from ctpbee.constant import *


def short(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
          stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现卖空的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    """


def sell_open(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
              stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现卖空的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    """
```

- `cover/buy_close` **平多头仓位**

```python
from ctpbee.constant import *


def cover(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
          stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现平多头的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    因为个别交易所有平今和平昨的问题，ctpbee会自动区分交易所进行平仓 ，所以可能实现一个平仓请求会发送两次平仓操作，所以它返回的是一个列表
    [local_order_id1, local_order_id2]

    """


def buy_close(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
              stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现平多头的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    因为个别交易所有平今和平昨的问题，ctpbee会自动区分交易所进行平仓 ，所以可能实现一个平仓请求会发送两次平仓操作，所以它返回的是一个列表
    [local_order_id1, local_order_id2]

    """

```

- `sell/sell_close` **平空头仓位**

```python
from ctpbee.constant import *


def sell(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
         stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现平多头的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    因为个别交易所有平今和平昨的问题，ctpbee会自动区分交易所进行平仓 ，所以可能实现一个平仓请求会发送两次平仓操作，所以它返回的是一个列表
    [local_order_id1, local_order_id2]

    """


def sell_close(self, price: float, volume: float, origin, price_type: OrderType = OrderType.LIMIT,
               stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现平多头的操作
    * price: 发单价格
    * volume: 发单手数
    * origin: 源，传入k线数据或者tick数据或者其他数据, 此处传入他主要是为了获取交易所代码
    * price_type: 价格类型，默认传入限价
    * stop: 暂时无效，后面会维护
    * lock: 暂时无效，后面会维护
    * kwargs: 未启用
    因为个别交易所有平今和平昨的问题，ctpbee会自动区分交易所进行平仓 ，所以可能实现一个平仓请求会发送两次平仓操作，所以它返回的是一个列表
    [local_order_id1, local_order_id2]

    """

```

- `cancel`   **撤单**

```python
from ctpbee.constant import *
from typing import Text


def cancel(self, id: Text, origin: [BarData, TickData, TradeData, OrderData, PositionData] = None, **kwargs):
    """
    快捷撤单函数
    id: 发单返回的单号
    """

```

- `query_position` **发起一次持仓查询**

```python
def query_position(self):
    """
    向服务器发起一次持仓查询请求, 注意这是向服务器发起查询请求，而不是直接拿到数据，
    因为ctp的机制问题我们需要发起请求，然后接受他们的数据，数据的访问参见数据模块，
    下面的查询账户也是一样的道理
    """
```

- `query_account` **发起一次账户查询**

```python
def query_account(self):
    """
    向服务器发起一次账户查询请求
    """
```

##### 注意

在以上的默认平仓函数中我默认实现了实际仓位的检查以及信号修复功能，这意味着如果你有20手仓位 但是你发了平30的信号, sell和cover会自动修复成20手仓位, 这一点需要你注意.