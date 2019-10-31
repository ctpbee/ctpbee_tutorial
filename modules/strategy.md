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
- 基于类集成的模式

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

