## 操作模块(Action module)

前面说到ctpbee的设计充斥了模块和层的概念， 其实这个理解算是有一点模糊。需要你细心体会。

话不多说让我说到正题 ---> `Action` Module(Level)

### 小故事
首先让我来讲个很浅显的道理，我们知道如果你在路边看到一棵果树，而你又很渴口，内心势必会非常难受，
对于不太聪明的做法而言就是爬树，摘果解渴。 这固然能够解决问题， 但是走了一段路你又渴了咋办？ 

这个时候选择就来了，是要再爬一次树吗? 不不不，又要爬树, 这是在浪费你的体力和时间。那能不能有个比较好的方式来解决口渴的问题?

ok，这个时候我们来把问题捋以下，我们现在要解决口渴的问题，解决办法是摘果子。但是我们面临着一个问题，就是我们每次都要重复去爬树，这是一个很麻烦的动作。
那我们能不能把这个动作抽象并固化呢？ 答案是可以的，我们是需要把果子弄下来， 但是我们每次都要重复爬树，简化它，我们是不是可以弄来一根杆子来敲果子呢？ 
这样我们口渴的时候就只要每次去调用杆子，但是这样有个缺陷是你必须要时时刻刻都要带着一根杆子，而我们只是口渴，一个人会觉得难以接受，太重了！

但是这里涉及一个价值观的问题，如果一个工具带来的收益是大于成本，那这就是血赚! 所以这时候如果是一群人呢？他们可以轮流来背，那心理会不会舒服很多 ～ 而且如果这根杆子能变形呢，那是不是就能够符合多个人的不同需求，A1需要钓鱼，A2需要击落水果，
A3需要盛水，他能在不同的人手里发挥出不同的作用,同时每个人也都可以使用杆子的全部功能。 这是不是很酷？

没错我这边说的杆子就是`Action`模块 (我想把它称呼为**全自动千奇百怪功能杆**)，它在`ctpbee`就充当这样一个功能。 而A1，A2..他们就代表着每个策略， 我们把基础的功能添加在其中，然后让你可以自行添加功能

这说起很激动人心对吧， 现在让我为你介绍他的详细功能~

### 详细功能

#####  导入
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
上述我们其实就扩展了一个操作，但是我们如何把他载入到系统中去以及如何调用相信你还是一头雾水。
因为杆子是提前创建好的，所以我们需要在app创建的时候将其传入 
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

诚如你所见，策略会有一个属性名字叫做action,通过它，你就可以访问到操作模块，等k线进来进行了 **平空一手** 的操作。
同时你也可以通过`self.action.operation()`调用到你自己创建的函数 。通过这样的方式就可以实现 `一次编写->所有策略都可以进行调用`

#### API
为了给你们提供一些好用的函数，ctpbee特意为你们写了一些API，他们分为两种，一种是底层API， 一种是上层API(应用API).


###### 底层API

诚如所见，底层API是负责更加底层的一点事情,比如说发单操作，下面我给出一个函数包含函数前面的列表以及如何使用的方法 

- send_order   发单

```python

def send_order(self, order):
    """
    接受一个标准格式的发单请求， 返回报单本地id
    """
    pass

```


- cancel_order   撤单

```python
def cancel_order(self, cancel_req):
    """
    接受一个请求， 返回成功与否（1/0）
    """
    pass
```

###### 高层API

- `buy`   **买多**

```python
from ctpbee.constant import *

def buy(self, price: float, volume: float, origin,price_type: OrderType = OrderType.LIMIT,
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

- `short` **卖空**

```python
from ctpbee.constant import *

def short(self, price: float, volume: float, origin,price_type: OrderType = OrderType.LIMIT,
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

- `cover` **平多头仓位**

```python
from ctpbee.constant import *

def cover(self, price: float, volume: float, origin,price_type: OrderType = OrderType.LIMIT,
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


- `sell` **平空头仓位**

```python
from ctpbee.constant import *

def sell(self, price: float, volume: float, origin,price_type: OrderType = OrderType.LIMIT,
         stop: bool = False, lock: bool = False, **kwargs):
    """
    此函数主要实现平空头的操作
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

##### 还有一些话

我们写了一些关于银行转账的API，但是苦于没有自己的账户可以进行实现， 这里就不过多加以阐述。后面如果完善了会补上完整的文档。