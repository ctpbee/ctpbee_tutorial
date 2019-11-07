# API 

这里面描述了一些系统级别的`API`, 可以从`ctpbee`直接导入

### API说明
- `current_app()`
> 回当前处于栈顶的App核心对象，也就是作为你的app的代理，让你在全局的任意地方能够访问到你的核心App对象

- `get_app()` --->params: app_name
> 通过传入你的App创建的时候传入的名字获取App对象。

- `del_app()` ---> params: app_name
> 删除账号 

- `switch_app()` ---> params: app_name
> 将指定账户切换到栈顶，使得可以通过current_app可以进行访问

- `send_order()` ---> params: order_req, app_name
> 传入发单请求和账户名字，使得账户能够发单，当app_name不传入的时候默认使用current_app

- `cancel_order()` ---> params: cancel_req, app_name
> 和send_order类似，不过是传入撤单请求，撤单 

- `subscribe()` ---> params: local_symbol
> 订阅行情， 传入本地的local_symbol

- `auth_time()` ---> params: data_time
> 传入数据的datetime.time()格式的时间，校验时间合不合法。返回True/False

- `get_ctpbee_path()`
> 返回ctpbee系统的默认路径