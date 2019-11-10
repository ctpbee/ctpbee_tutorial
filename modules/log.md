## 日志模块
在任意的地方你都可以通过`self.info`, `self.error`, `self.warning`, `self.debug`实现调度输出，
但是为了方便你进行统一的日志处理。 我们为你开放了日志接口
```python

from ctpbee import VLogger

class Logger(VLogger):
    def handler_record(self, record: dict):
        """ 处理日志接口"""
        print(record)
```

#### 如何载入
```python
from ctpbee import CtpBee
# 通过logger_class将类传进去
app = CtpBee("demo", __name__, logger_class=Logger)
```
