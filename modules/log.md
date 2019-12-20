## 日志模块
在任意的地方你都可以通过`self.info`, `self.error`, `self.warning`, `self.debug`实现调度输出，
但是为了方便你进行统一的日志处理。 我们为你开放了日志接口

> 后续我们会开发邮件接口，微信等接口供你们使用处理


```python

from ctpbee import VLogger

class Logger(VLogger):
    def handler_record(self, record: dict):
        """ 处理日志接口"""
        print(record)
```

#### 如何载入

同样的道理，你需要把你编写的代码插入到ctpbee的核心App里面， 在初始化的函数里面指定它。

```python
from ctpbee import CtpBee
# 通过logger_class将类传进去
app = CtpBee("demo", __name__, logger_class=Logger)
```
