## 策略模块

我将在此章向你介绍如何在ctpbee中使用策略模块，分为下面三个章节为你讲述 
- 架构
- 使用方式
- 需要注意的点

###  架构
ctpbee将策略设计为一个`Level`，而在这个`Level`里面可以容纳多个策略，他们都是基于行情驱动的

### 使用方式 
为了你的使用习惯，我们开发了两种模式供你使用`ctpbee`的**策略**模块
- 装饰器模式
- 基于类继承的模式



##### 装饰器大法
首先介绍一下基于装饰器的模式，如果你们有使用过米筐这种的在线平台，那么你应该很熟悉这种使用模式

首先来让我们让创建一个策略实例
```python
from ctpbee import CtpbeeApi
data_recorder = CtpbeeApi("data_recorder")
```
呐，我们创建一个策略实例，实例的名字的是`data_recorder`, 
同时我们传入了一个策略的名字叫`data_recorder`,而这个id是被app所承认的。
那我们现在创建了核心策略对象，那我们怎么来编写策略呐 ，看以下代码的注释

```python
@data_recorder.route(handler="tick")
def handler_tick(self, tick):
    print(tick)

@data_recorder.route(handler="bar")
def handler_bar(self, bar):
    print(bar)

# 然后把他载入到app中
app.add_extension(data_recorder)
```
那么当行情tick到来的时候会自动触发`handler_tick`里面的代码，bar/k线也是同样的道理
但是只有这两种API吗，不, 远不如此，`route`里面的`handler`还可以接受更多的参数，不过他们是非必须，按照你们的意见进行编写即可，
包括以下:
- `contract` 合约
- `position` 持仓
- `trade`    成交
- `order`    报单
- `init`     初始化
- `shared`   分时图 / todo: 后面进行移除
- `timer`    定时器，一秒触发一次

但是随之而来又产生了另外一个问题，我只能写这个几个事件的处理代码吗， 我就不能自己实现一些好用的功能吗? 

答案当然是可以的，我们提供了`register`装饰器来让你自定义功能函数，并可以在事件处理代码中进行调用,但是你需要注意的是你的函数必须是一个`Method`的编写方式而不是一个普通`function`
的编写方式。

**示例**：
```python
@data_recorder.register()
def get_position_by_ld(self, local_symbol, direction)
    """ 根据local_symbol和direction获取持仓 """
    pass

```

上述我们通过策略`data_recorder`的注册函数实现了一个`get_position_by_ld` 示例方法，注意他传入的参数第一个是self,也就是示例本身，然后传入
`local_symbol`和`direction`，他是一个`Method`,所以我们在调用他们的时候只需要传入`local_symbol`和`direction`。

通过这个很小巧的API，你可以自定义很多很多的功能，尽情享用他吧

**完整代码**

```python
from ctpbee import CtpBee, CtpbeeApi


data_recorder = CtpbeeApi("data_recorder")

@data_recorder.route(handler="tick")
def handler_tick(self, tick):
    print(tick)

@data_recorder.route(handler="bar")
def handler_bar(self, bar):
    # 调用注册的事件
    self.get_position_by_ld(bar.local_symbol, bar.direction)    
    print(bar)

@data_recorder.register()
def get_position_by_ld(self, local_symbol, direction):
    """ 根据local_symbol和direction获取持仓 """
    print("I AM A METHOD")       

if __name__ == '__main__':
    app = CtpBee("demo", __name__)
    info = {}       
    app.config.from_mapping(info)
    app.add_extension(data_recorder)      
    app.start()      
```



##### 基于类继承的模式
不同于装饰器模式的先创建示例，再进行编写代码的模式，基于类的方式需要你先写代码，再创建实例

**示例**
```python
from ctpbee import CtpbeeApi

class DataRecorder(CtpbeeApi):
    def __init__(self, name, app):
        super().__init__(name, app)
    
    def on_bar(self, bar):
        self.get_position_by_ld(bar.local_symbol, bar.direction)            
        pass
    
    def on_tick(self, tick):
        pass

    def get_position_by_ld(self, local_symbol, direction):
        print("I AM A METHOD")
```
聪明细心的你肯定发现了上述代码其实和装饰器非常相似，不同的是函数名字换成on_bar这样的模式。

没错，基于继承的方式需要你重写函数，同样的on_bar和on_tick必须要被重写，其余按照你的需要进行重写就好,同样的这里面有一份函数名单需要你去了解

- on_realtime  实时策略检查 , 一秒运行一次

> 函数签名
`def on_realtime(self)`

- on_order    报单

> 函数签名
`def on_order(self, order)`

- on_trade    成交

> 函数签名
`def on_trade(self, trade)`

- on_position    持仓

> 函数签名
`def on_position(self, position)`

- on_init       初始化

> 函数签名 def on_init(self, init)

- on_contract     合约

> 函数签名 def on_contract(self, contract)

- on_shared     分时图

> 函数签名 def on_shared (self, shared)

然后我们创建实例并载入到app中去

**示例**
```python

if __name__ == '__main__':
    from ctpbee import CtpBee
   
    app = CtpBee("demo", __name__) 
    info = {}        
    app.config.from_mapping(info)
    data_reocorder = DataRecorder("data_recorder", app)
    app.start()
```
到这里你应该就能正常运行代码，但是等等， 这里有个小问题，为啥我们没有用`add_extension`方法?
 
 我来解释下, 策略肯定要能够拥有app这个属性，所以我们肯定需要将app传入进去 ，他们应该是互相拥有的方式.
 但是如何让他们互相拥有这个的实现方式大有文章，
 **有四种可能**：
 - app创建的时候传入 //  app作为核心，创建的时候传入明显不合适 
 - app创建后传入        可以，通过`add_extension` 传入策略
 - 策略创建的时候传入    可以，策略角色属于运行方。`data_recorder = DataRecorder("data_recorder", app)`，但是这不一定是必要的，作为可选方案
 - 策略创建后传入       `data_recorder.init_app(app=app)`

所以基于这四种方式，你可以很灵活的进行工程化~~ 


### 需要注意的点

我在这个地方和你讲述一些目前存在的问题，避免你走向自闭的道路

