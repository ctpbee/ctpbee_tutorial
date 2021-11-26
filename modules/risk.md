## 风控模块

ctpbee相较于其他的量化框架而言更加偏向于底层，我们会为你提供构建风控的工具而不是完整的风控，
它提供了一个可以描述为事前/事后的检查和控制模型，可以对你的每个操作执行细致的控制，当然这一切的前提是你愿意。
所以下面我会为你介绍如何在ctpbee中使用风控！ 

首先让我提一下上一章中我们提到的操作层，`ctpbee`的风控就是基于此来实现的 

按照正常人的思维逻辑， 我们做一件事肯定是需要检查手上的工具，也就是掂量下自己能不能做 。
这个过程就被称为函数被调用前参数检查, 这就是`before`机制，
而等你处理完之后需要检查事件结果，这就是`after`机制。
这是一个完整的模型 ，所以做一件事需要一个完整的`before`和`after`控制。

那么重点来了，了解完上述话， 你肯定很好奇ctpbee中如何实现， 风控也是被描述为一种可插拔的工具
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
我在ctpbee设计的时候将函数命名为`before_ + operation_func.__name__ 和 after_ + operation_func.__name__`,
也就是说你需要拿到需要被风控的函数的名字，在风控中以 `before_`加上函数名字的 来命名你的风控函数。 这应该很好理解，对吗？
我现在来为你解释下上述代码
--- 
**事前**

```python
    def before_send_order(self, *args, **kwargs):
        print(self)
        return True, args, kwargs

```
*args和**kwargs 如果你的工地扎实那么你应该很清楚它代表这个这个函数传入的任意参数，例如你send_order传入一个 order,那么在这边就可以通过
`order = args[0]` 来拿到， 同时注意他返回的参数， 第一个表示检查的结果，如果为False则不通过检验，放弃此次操作。 但是我们还返回了args和kwargs, 
等等我们还把参数返回去了， 所以我们是不是做一些操作?

答案是可以的，你可以修改他们，来达到校验参数的功能。 但是你需要注意的是 `args`是一个**元组**(这意味你无法修改它)， 你需要转换它为列表。  我推荐你在实际的开发过程中打印它们来查看具体内容 

**事后**

```python
    def after_send_order(self, result):
        print("I got the result: ",result)
        while True:
            from time import sleep
            sleep(1)
            print("I AM CHECKING")
```

事后的代码就显得简单，拿到返回的结果然后执行一下检查，注意你被执行风控的函数必须返回一个值， 
同时被限定为一个。如果你要返回多个参数，推荐你返回一个列表或者元组来达到此功能 。看起来很简单对吗， 
等等你写了一个`While True`,它会无限等待下去啊。

别担心，这个函数被我使用一个线程来执行，同时它有一个生命周期，默认为3秒，超过这个时间它会被杀死掉毛。你可以通过配置项 `AFTER_TIMEOUT`来指定超时的时间。

我们现在实现了事前/事后模型，同时提供了一个一直在运行检查的函数在运行。

```python
def realtime_check(self):
    """
    此处的代码都会被一秒执行一次
    """
```

### 载入
 风控程序也是需要被事先载入的
```python
from ctpbee import RiskLevel,CtpBee,Action
class ActionMe(Action):
    def __init__(self, app):
        super().__init__(app)
        self.add_risk_check(self.send_order)
        

app = CtpBee("demo", __name__, risk=RiskLevel, action_class=ActionMe)
```
如果你必须使用风控代码的话，那么你必须重写`Action`的`__init__`函数，上面我有讲过 `当然这一切的前提是你愿意。`，
这个地方需要你手动通过`add_risk_check`来将其加入风控。 同样你只要加`RiskLevel`类传入给app。

到此我们的风控介绍是已经完毕， 总体来说我们没有提供具体的风控代码，但是给了你画图的工具，希望你能创建出优秀的风控代码。

> 最后提醒一下ctpbee的风控是装饰器类。所以无法使用`@property`装饰器哦，
>同时一个账户只能支持一个风控类，如果你们的账户很多， 那么你要考虑如何使用工厂函数来搞定它。
