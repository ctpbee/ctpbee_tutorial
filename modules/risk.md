## 风控模块

ctpbee相较于其他的量化框架而言更加偏向于底层，我们会为你提供构建风控的工具而不是完整的风控，
它提供了一个可以描述为事前/事后的检查和控制模型，可以对你的每个操作执行细致的控制，当然这一切的前提是你愿意。
所以下面我会为你介绍如何在ctpbee中使用这令人着迷的风控代码！ 

首先让我提一下上一章中我们提到的操作层，ctpbee的风控就是基于此来实现的 

按照正常人的思维逻辑， 我们做一件事肯定是需要检查手上的工具，也就是掂量下自己能不能做 。
这个过程就被称为事前检查。他被描述为你的工具是否给力以及能不能做完 ～ 这就是`before`机制，
而等你做完之后你肯定需要细细品位一下你做完之后的感觉。所以你肯定要拿到做完之后的结果然后一番处理，这就是`after`机制。
这是一个完整的模型 ，所以做一件事需要一个完整的`before`和`after`控制。

那么重点来了，了解完上述话， 你肯定很好奇ctpbee中的风控实现， 风控也是被描述为一种可插拔的工具
首先导入
```python
from ctpbee import RiskLevel
```
然后写一个类来继承它
```python
from ctpbee import RiskLevel


class RiskMe(RiskLevel):
    def before_send_order(self, *args, **kwargs):
        print(self)
        return True, args, kwargs

    def after_send_order(self, result):
        print("I got the result: ",result)
        while True:
            from time import sleep
            sleep(1)
            print("I AM CHECKING")
```
我现在来为你解释下上述代码
我在ctpbee设计的时候将函数命名为`before_ + operation_func.__name__ 和 after_ + operation_func.__name__`,
也就是说你需要拿到需要被风控的函数的名字，在风控中以 `before_`加上函数名字的 来命名你的风控函数。 这应该很好理解，对吗？

