# API 

这里面描述了一些系统级别的`API`, 可以从`ctpbee`直接导入

### API说明
- `current_app()`

解释: 回当前处于栈顶的App核心对象，也就是作为你的app的代理，让你在全局的任意地方能够访问到你的核心App对象

示例:
```
  from ctpbee import current_app
  app = current_app()
```
---

- `get_app()` --->params: app_name

解释: 通过传入你的App创建的时候传入的名字获取App对象。

示例:
```python
from ctpbee import CtpBee, get_app
app1 =  CtpBee("somewheve", __name__)
# 假定你已经通过上述代码创建了名为somewheve的App，那么下面代码将让你获取该app
app2 = get_app("somewheve")
assert app1 == app2
```


- `del_app()` ---> params: app_name

解释：顾名思义，删除账号,需要传入账户名 ，注意这是一个**危险操作**，谨慎使用。


- `switch_app()` ---> params: app_name

解释: 将指定账户切换到栈顶，使得可以通过current_app可以进行访问

示例:
```python
# 我们在此处创建了第一个账户App
from ctpbee import current_app, switch_app, CtpBee
app = CtpBee("yutiansut1", __name__)
print(current_app.name)


app2 = CtpBee("yutiansut2", __name__)
# 此时候你会发现current_app2被切换到 current_app上面去
print(current_app.name)

# 现在通过调用switch_app将yutiansut1再次切换到栈顶， 也就是current_app上面
switch_app("yutiansut1")
print(current_app.name)
```
> 输出

```textmate
yutainsut1
yutiansut2
yutiansut1
```

在上述的输出中，我们三次都打印了current_app的name属性， 可以看出前两次输出的都是最新创建的，但是当调用了switch_app之后，成功输出了`yutiansut1`的值


--- 

- `send_order()` ---> params: order_req, app_name

解释: 传入发单请求和账户名字，使得账户能够发单，当app_name不传入的时候默认使用current_app, 使用此API需要你熟练掌握`ctpbee`的机制。
知道如何生成一个发单请求

示例:
```python
from ctpbee import send_order 
from ctpbee import helper
# 通过helper提供的方法来快速生成报单请求

```

- `cancel_order()` ---> params: cancel_req, app_name
> 和send_order类似，不过是传入撤单请求，撤单 

- `subscribe()` ---> params: local_symbol
> 订阅行情. 传入订阅的代码即可，可以调用for循环来订阅多个行情

- `auth_time()` ---> params: data_time
> 传入数据的datetime.time()格式的时间，校验时间合不合法。返回True/False

- `get_ctpbee_path()`
> 返回ctpbee系统的默认路径 

示例:
```python

from ctpbee import get_ctpbee_path

path = get_ctpbee_path()
print("ctpbee path: ", path)
```

输出:

```textmate
ctpbee path:  /home/somewheve/.ctpbee
```
注意此输出视个人电脑而定