## 数据记录器

一个比较完善的交易系统肯定是能够记录数据啊，然后让你快速调用到交易数据是很重要的事情。
我将从下面三个板块来开始阐述`ctpbee`的数据。

#### 1.数据记录器

** 记住所有方法都是在`self.app.recorder`， 后面标注的`params`是需要传入的参数名字 **
###### 调用示例
```python

def on_bar(self, bar):
    active_orders = self.app.recorder.get_all_active_orders()
    print(active_orders)
```

- `get_all_orders()`
> 返回所有的报单

- `get_all_active_orders()`
> 返回所有未成交的单子

- `get_all_trades()`
> 返回所有成交单

- `get_all_contracts()`
> 返回所有的合约

- `get_all_tick()`
> 返回当日的你订阅合约的所有tick

- `get_errors()`
> 返回所有的报错信息

- `get_new_error()`
> 获取最新的一条错误

- `get_tick()` --> params: `local_symbol`
> 返回当日登录之后指定合约的tick

- `get_trade()` --> params: `local_trade_id`
> 根据成交编号返回详细的成交单信息

- `get_contract_last_price()` --> params: `local_symbol`
> 根据local_symbol拿到合约的最新价格

- `get_main_contract_by_code()`  --> params: `code`
> 根据code拿到该code的主力合约，例如传入rb返回rb的主力合约,注意区分大小写
- `get_bar()` -->


- `get_contract()` ---> params: `local_symbol`
> 用途: 根据local_symbol返回合约的具体信息， 注意返回对象是一个ContractData实例

- `get_account()` --->
> 用途: 返回当前账户信息

- `.main_contract_list` 
> 属性，返回主力合约列表 --> 注意工作可能不会很完善 

- `clear_all()`
> 清除掉所有的数据信息 

#### 2.持仓管理
> 这一部分的还不够完善， 需要你的意见

###### 账户持仓管理

- `get_all_positions()`
> 用途: 获取账户所有的持仓 
> 调用方式: self.app.recorder.get_all_positions()

- `get_position_by_ld()` --> params: local_symbol, direction
> 根据local_symbol和direction拿到持仓信心, 返回一个PositionData
> 调用方式: self.app.recorder.position_manager.get_position_by_ld


###### 策略持仓管理

为了`ctpbee`能够更好的适应环境，我们开发了策略持仓管理， 但是当下它的表现不够稳定， 所以这一部分的文档我暂时不开发，
等后续内部测试ok了我会将其放出来，如果有兴趣的可以自行阅读代码！

#### 3.数据结构

关于`ctpbee`的数据构成，请参见 [数据结构](constant.md)

值得注意的是你需要将明白Data类型的数据都有以下的API
- `_to_df()`
> 转换为DataFrame

- `_to_dict()`
> 转换为字典， 但是值得注意的是会自动将枚举类变量转换为对应的字符串

- `_asdict()`
> 返回字典，不做数据转换处理

