## 日志模块

在任意的地方你都可以通过`self.info`, `self.error`, `self.warning`, `self.debug`实现调度输出， 但是为了方便你进行统一的日志处理。 我们为你开放了日志接口

> 后续我们会开发邮件接口，微信等接口供你们使用处理

```python

from ctpbee import VLogger


@VLogger()
def handler_record(**record):
    msg = f"{record['time'].split(' ')[1]}   {record['name']} {record['level']}   {record['owner']}   {record['message']}"
    print(msg)
    # todo: 实现log处理

```
