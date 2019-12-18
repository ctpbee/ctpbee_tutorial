## 数据记录器

一个比较完善的交易系统肯定是能够记录数据啊，然后让你快速调用到交易数据是很重要的事情。
我将从下面三个板块来开始阐述`ctpbee`的数据。

#### 1.数据记录器

** 记住所有方法都是在`self.app.center`

> 原本的recorder数据访问同样支持,文档中不再阐述此API， 但是你可以通过阅读取源代码

#### API
- 属性  `active_orders`  --->  还未成交的报单

注意他返回的是一个列表 [orderdata1, orderdata2, orderdata3]
```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        active_orders = self.app.center.active_orders
        print(active_orders)
```

- 属性 `orders` ---> 返回所有报单信息 

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 拿到所有报单
        orders = self.app.center.orders
        print(orders)
```

- 属性 `trades` ---> 返回当日所有成交单

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 拿到所有成交单
        trades = self.app.center.trades
        print(trades)
```

- 属性 `account` ---> 返回账户信息

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 拿到账户信息
        account = self.app.center.account
        print(account)
```

- 函数 `get_tick` ---> 返回最近一条tick信息
注意： 它返回的是一个TickData, 需要传入 `local_symbol`

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 此处我们拿到ag1912的最新一条tick
        tick = self.app.center.get_tick("ag1912.SHFE")
        print(tick)
        print(type(tick))
```

- 函数 `get_active_order` ---> 返回某个合约的未成交单 
注意：他返回的是一个列表， 需要传入 `local_symbol`

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 此处我们拿到ag1912的所有未成交单
        active_orders = self.app.center.get_active_order("ag1912.SHFE")
        print(active_orders)
        print(type(active_orders))
```

#### 2.持仓管理

###### 账户持仓管理
注意： `get_position`函数，需要传入一个`local_symbol`，返回一个None(如果没有持仓的话)或者`PositionModel`
, 下面的二级列表中列举了属性名称以及含义
- `get_position(local_symbol) -> PositionModel`  获取某个合约的持仓信息

    + `long_volume` 长头总持仓
    + `long_yd_volume` 长头历史持仓
    + `long_price` 长头持仓均价
    + `long_pnl` 长头持仓盈利
    + `long_frozen` 长头冻结
    + `short_volume` 空头总持仓
    + `short_yd_volume` 空头历史持仓
    + `short_price` 空头持仓均价
    + `short_pnl` 空投持仓盈利
    + `short_frozen` 空头冻结
    + `exchange` 交易所代码
    + `local_symbol` 本地合约代码
  
调用示例

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 此处我们拿到ag1912的持仓信息
        pos = self.app.center.get_position("ag1912.SHFE")
        print(pos)
        print(type(pos))
        if not pos:
            print("没持仓不好意思")
        else:
            print("long: ", pos.long_volume)
            print("short: ", pos.short_volume)
```




###### 策略持仓管理

为了`ctpbee`能够更好的适应环境，我们开发了策略持仓管理
基于场景，策略访问自己的持仓和策略访问其他策略的持仓
> 注意: 上面是使用center,而此处需要使用策略自带的level_position_manager
- 策略访问自己的持仓信息  `get_position(local_symbol) -> PositionModel`

代码示例

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 此处我们拿到ag1912的持仓信息
        pos = self.level_position_manager.get_position("ag1912.SHFE")
        print(pos)
        print(type(pos))
        if not pos:
            print("没持仓不好意思")
        else:
            print("long: ", pos.long_volume)
            print("short: ", pos.short_volume)
``` 

- 其他地方访问策略的持仓
> 注意：此处需要用到我们的center

代码示例

```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        # 假定另外一个策略的名字叫somewheve
        pos = self.center['somewheve'].level_position_manager.get_position("ag1912.SHFE")
        print(pos)
        print(type(pos))
        if not pos:
            print("没持仓不好意思")
        else:
            print("long: ", pos.long_volume)
            print("short: ", pos.short_volume)
```

注意:不同于账户持仓，策略持仓对某些字段做了删减，你只能访问一些持仓的信息。
+ `long_volume` 长头总持仓
+ `long_yd_volume` 长头历史持仓
+ `long_price` 长头持仓均价
+ `short_volume` 空头总持仓
+ `short_yd_volume` 空头历史持仓
+ `short_price` 空头持仓均价
+ `exchange` 交易所代码
+ `local_symbol` 本地合约代码

#### 3.数据API

关于`ctpbee`的数据构成，请参见 [数据结构](constant.md)

值得注意的是你需要将明白Data类型的数据都有以下的API，比如`on_tick`里面的`TickData`

- `_to_df()`
> 转换为DataFrame

- `_to_dict()`
> 转换为字典， 但是值得注意的是会自动将枚举类变量转换为对应的字符串

- `_asdict()`
> 返回字典，不做数据转换处理

