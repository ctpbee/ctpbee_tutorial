## 模块化的构造方式 

首先明确 ctpbee的核心架构  **CtpBee** ， 他是我们ctpbee的核心主体，相当于一艘船的龙骨一样，船的行情全靠他。那如何创建呢 ？ 请看下面代码 

```python
from ctpbee import CtpBee

app = CtpBee("somewheve", __name__)
```

那上述代码做了什么事情 ，结果非常显而易见， 我们创建了一个app的核心对象，但令人困惑的是我们如何去了解他传入的参数  **somewheve** 和 **__name__**， 那他们是什么呢 ？ 

我们在设计ctpbee的时候将每个账户都隔离开来，所以我们必须要为每个账户创建 一个`独一无二`的名字，那这就是 `somewheve`的由来， 而`__name__`则是导入模块的名称， 你无须知道他是什么意义， 但请确保 上述两个参数是必须传入的 ！那我们现在有了主要核心的app， 现在 就来让我们探索`ctpbee` 的模块， 下面所述的app皆指上述所创建的app实例。、

- [配置模块](modules/config.md)

- [策略模块](modules/strategy.md)

- [数据模块](modules/rec.md)

- [操作模块](modules/action.md)

- [日志模块](modules/log.md)












