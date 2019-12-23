## 如何编写一个行情录制器

我在此处教你如何来开发一个简单的行情录制

首先分析一下行情录制的好处与坏处
- 好处 自己本地录制数据，可以随时调用
- 坏处 录制的数据质量无法保证，可能会出现掉线等情况导致数据缺失

其中的优缺点自行斟酌 

先回到正题 如何来录制tick
首先我们来创建一个App 
```python
from ctpbee import CtpBee

app = CtpBee("recorder", __name__)
然后载入配置信息
info = {
    "CONNECT_INFO": {
        "userid": "", # 期货账户名
        "password": "", # 登录密码
        "brokerid": "", # 期货公司id
        "md_address": "", # 行情地址
        "td_address": "", # 交易地址
        "appid": "",  # 产品名
        "auth_code": "", # 认证码
        "product_info":"" # 产品信息
    },
    "INTERFACE":"ctp",  # 登录期货生产环境接口
}
app.config.from_mapping(info)
```

环境准备好了，那么我们来讨论来如何录制tick
一般录制tick数据需要接受行情、过滤行情、行情入库.这三个步骤 
首先来收到行情

```python

from ctpbee import CtpbeeApi

class Market(CtpbeeApi):
    def on_bar(self, bar):代码
        """ 处理k线数据 """

    def on_tick(self, tick):
        """ 处理tick信息 """
        

```
在上述代码中我们提供了便捷的API让你能够直接收到行情tick和k线数据
注意k线数据是本地自行聚合的
我们现在要过滤行情，我们需要在此处思考两个点，数据不合法如何进行过滤
常见的三种不合法场景 
- 交易时间段出现非交易时间段的数据， 成为垃圾tick
- 非交易时间段出现垃圾tick
- 断线 

那么如何解决上述问题呢? 
- 第一个可能就要用到一个连续行情的算法了 来确保脏tick不会被送进数据库
- 第二个可以根据交易时间进行过滤即可 
- 第三个不好意思我也没想到你单台主机处理这个问题的可能性

那么动手做吧  直接放代码

```python

from datetime import datetime
from time import sleep

from ctpbee import CtpbeeApi, auth_time
from queue import Queue

from functools import total_ordering


@total_ordering
class TimeIt:
    """ 主要是来实现一个减法"""

    def __init__(self, datetime_obj: datetime):
        self._datetime = datetime_obj

    def __sub__(self, other: datetime):
        """
        实现减法操作
        返回绝对值
        """
        if not isinstance(other, TimeIt) and not isinstance(other, datetime):
            raise ValueError("参数类型不同无法使用减法")
        if isinstance(other, datetime):
            return int(abs(self._datetime.timestamp() - other.timestamp()))
        elif isinstance(other, TimeIt):
            return int(abs(self._datetime.timestamp() - other._datetime.timestamp()))

    def __eq__(self, other):
        if isinstance(other, datetime):
            return self._datetime == other
        elif isinstance(other, TimeIt):
            return self._datetime == other._datetime

    def __lt__(self, other):
        if isinstance(other, datetime):
            return self._datetime < other
        elif isinstance(other, TimeIt):
            return self._datetime < other._datetime


class Market(CtpbeeApi):
    def __init__(self, name):
        super().__init__(name)
        # 创建一个datetime队列
        self.datetime_queue = Queue()
        # 队列长度
        self.queue_length = 5
    
    def on_contract(self, contract):
        """ 订阅到推送过来的合约 """
        self.action.subscribe(contract.local_symbol)

    def on_bar(self, bar):
        """ 处理k线数据 """

    def on_tick(self, tick):
        """ 处理tick信息 """
        if not auth_time(tick.datetime.time()):
            """ 过滤非交易时间段的数据 """
            return

        if self.datetime_queue.empty() or self.datetime_queue.qsize() <= self.queue_length:
            pass
        else:
            q = self.datetime_queue.get()
            interval = q - tick.datetime
            if interval > 3600 * 4 or q < tick.datetime:
                """ 
                如果出现间隔过大或者时间小于队列的里面的数据，那么代表着脏数据
                """
                return
        self.datetime_queue.put(TimeIt(tick.datetime))    

```
上述我们使用减法和比较大小的程序来做一定的过滤。 

ok我们解决了过滤办法， 那么第三个就是来处理入库的问题


此处教程我们使用pymongo来录制到mongodb中去 
如果你没有安装pymongo请支持 `pip install pymongo`


```python
from pymongo import MongoClient

# 自定义数据的名字
DATABASE_NAME = "ctpbee_tick"
mongo_client = MongoClient()

database_c = mongo_client[DATABASE_NAME]
插入一个tick 
database_c[tick.local_symbol].insert_one(tick._to_dict())
```
等等。我们是不是少了一个类似运维的工具, 别担心，ctpbee提供了这个功能 

```python

from ctpbee import hickey

```

完整实现代码

```python
from datetime import datetime
from functools import total_ordering
from queue import Queue

from ctpbee import CtpbeeApi, auth_time
from pymongo import MongoClient

DATABASE_NAME = "ctpbee_tick"
mongo_client = MongoClient()

database_c = mongo_client[DATABASE_NAME]


@total_ordering
class TimeIt:
    """ 主要是来实现一个减法"""

    def __init__(self, datetime_obj: datetime):
        self._datetime = datetime_obj

    def __sub__(self, other: datetime):
        """
        实现减法操作
        返回绝对值
        """
        if not isinstance(other, TimeIt) and not isinstance(other, datetime):
            raise ValueError("参数类型不同无法使用减法")
        if isinstance(other, datetime):
            return int(abs(self._datetime.timestamp() - other.timestamp()))
        elif isinstance(other, TimeIt):
            return int(abs(self._datetime.timestamp() - other._datetime.timestamp()))

    def __eq__(self, other):
        if isinstance(other, datetime):
            return self._datetime == other
        elif isinstance(other, TimeIt):
            return self._datetime == other._datetime

    def __lt__(self, other):
        if isinstance(other, datetime):
            return self._datetime < other
        elif isinstance(other, TimeIt):
            return self._datetime < other._datetime


class Market(CtpbeeApi):
    def __init__(self, name):
        super().__init__(name)
        # 创建一个datetime队列
        self.datetime_queue = Queue()
        # 队列长度
        self.queue_length = 5

    def on_bar(self, bar):
        """ 处理k线数据 """

    def on_contract(self, contract):
        """ 订阅到推送过来的合约 """
        self.action.subscribe(contract.local_symbol)

    def on_tick(self, tick):
        """ 处理tick信息 """
        if not auth_time(tick.datetime.time()):
            """ 过滤非交易时间段的数据 """
            return

        if self.datetime_queue.empty() or self.datetime_queue.qsize() <= self.queue_length:
            pass
        else:
            q = self.datetime_queue.get()
            interval = q - tick.datetime
            if interval > 3600 * 4 or q < tick.datetime:
                """ 如果出现间隔过大或者时间小于队列的里面的数据，那么代表着脏数据"""
                return
        self.datetime_queue.put(TimeIt(tick.datetime))
        database_c[tick.local_symbol].insert_one(tick._to_dict())
        print(tick)


def create_app():
    from ctpbee import CtpBee
    app = CtpBee("recorder", __name__)
    info = {
        "CONNECT_INFO": {
            "userid": "",  # 期货账户名
            "password": "",  # 登录密码
            "brokerid": "",  # 期货公司id
            "md_address": "",  # 行情地址
            "td_address": "",  # 交易地址
            "appid": "",  # 产品名
            "auth_code": "",  # 认证码
            "product_info": ""  # 产品信息
        },
        "INTERFACE": "ctp",  # 登录期货生产环境接口
    }
    # 载入配置信息
    app.config.from_mapping(info)
    # 创建实例
    ext = Market("market")
    # 载入容器
    app.add_extension(ext)
    return app


if __name__ == '__main__':
    from ctpbee import hickey
    # 使用24小时模块进行分发
    hickey.start_all(create_app)

```

如何运行它? 
创建一个空白的py文件,例如data_recorder.py 将上述代码复制到data_recorder.py中，
请确保你的python解释器成功安装了ctpbee. 如果不会安装， 请参照文档开始的`安装`
然后打开命令行 
```
python data_recorder.py
```
注意上述是需要你安装`pymongo`, `mongodb数据库`, `ctpbee`,三者缺一不可
