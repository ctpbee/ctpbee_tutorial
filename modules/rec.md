## 数据记录器

一个比较完善的交易系统肯定是能够记录数据啊，然后让你快速调用到交易数据是很重要的事情。
我将从下面三个板块来开始阐述`ctpbee`的数据。

#### 1.数据记录器

** 记住所有方法都是在`self.app.center` 后面标注的`params`是需要传入的参数名字 **

> 原本的recorder数据访问同样支持,文档中不再阐述此API， 但是你可以通过阅读取源代码

#### API
属性  active_orders  --->  还未成交的报单
注意他返回的是一个列表 [orderdata1, orderdata2, orderdata3]
```python
from ctpbee import CtpbeeApi
class Example(CtpbeeApi):
    def on_bar(self, bar):
        active_orders = self.app.center.active_orders
        print(active_orders)
```


#### 2.持仓管理
> 这一部分的还不够完善， 需要你的意见

###### 账户持仓管理

- `get_all_positions()`
> 用途: 获取账户所有的持仓 
> 调用方式: self.app.recorder.get_all_positions()

- `get_position_by_ld()` --> params: local_symbol, direction
> 根据local_symbol和direction拿到持仓信心, 返回一个PositionData
> 调用方式: self.app.recorder.position_manager.get_position_by_ld


###### 策略持仓管理

为了`ctpbee`能够更好的适应环境，我们开发了策略持仓管理， 但是当下它的表现不够稳定， 所以这一部分的文档我暂时不开发，
等后续内部测试ok了我会将其放出来，如果有兴趣的可以自行阅读代码！

#### 3.数据结构

关于`ctpbee`的数据构成，请参见 [数据结构](constant.md)

值得注意的是你需要将明白Data类型的数据都有以下的API
- `_to_df()`
> 转换为DataFrame

- `_to_dict()`
> 转换为字典， 但是值得注意的是会自动将枚举类变量转换为对应的字符串

- `_asdict()`
> 返回字典，不做数据转换处理

