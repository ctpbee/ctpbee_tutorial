## 配置模块

ctpbee采用了类似于flask的配置模块，可能这就是所有量化框架中扩展性最强的配置项了，希望你能爱上它。

我们每个核心App都有持有一份config，它是一个字典格式的配置。为了方便你进行载入自定义的配置，我们转移了flask里面的部分代码用来构建
`ctpbee`的配置系统
下面我将从两个方面来阐述它。
- 配置载入
- 配置项解释
---
#### 配置载入
我们为了你能够快速载入配置项，编写了一系列的API， 使得你能够快速将配置文件载入到`ctpbee`中去。
- `json support`

```python
app.config.from_json(json_path)
```

- `mapping support`

```python
# 支持一个字典格式的映射
app.config.from_mapping(mapping_obj)
```

- `pyfile support`

```python
app.config.from_pyfile(pyfile_path)
```

- `obj support`

```python
# 实例对象的支持
app.config.from_object(obj)
```

上述四个API应该能帮助你很灵活地传入你的配置。

#### 配置项
--- 

| 配置项 | 默认值 | 可选值 | 解释 | 
| :------| ------: | :------: | :------:|
| TD_FUNC | False | True/False | 是否开启交易功能|
| MD_FUNC | True | True/False | 是否开启行情功能|
|INTERFACE|"ctp"| ctp/ctp_se  |载入接口名称，目前支持ctp生产以及ctp_se穿透式验证接口|
|XMIN| [] | [int] | k线序列周期，支持一小时以内的k线任意生成,默认生成一分钟的k线|
|SLIPPAGE_COVER|0| float | 平多头设置的滑点， 支持正负数|
|SLIPPAGE_SELL | 0| float|  平空头滑点设置|
|SLIPPAGE_SHORT|0 | float|  卖空滑点设置|
|SLIPPAGE_BUY|0| float|买多滑点设置|
|SHARED_FUNC| False| True/False|分时图数据--->等待删除
|REFRESH_INTERVAL| 1.5| float|定时刷新秒数， 需要在CtpBee实例化的时候将refresh设置为True才会生效|
|INSTRUMENT_INDEPEND|False| True/False| 是否开启独立行情，策略对应相应的行情,注意你需要将合约的`local_symbol`加入到`instrument_set`|
|CLOSE_PATTERN|"today"| today/yesterday| 面对支持平今的交易所，指定优先平今或者平昨 ---> today: 平今, yesterday: 平昨， 其他:触发异常|
|TODAY_EXCHANGE|[Exchange.SHFE.value, Exchange.INE.value]| Exchange.key.value| 需要支持平今的交易所代码列表|
|AFTER_TIMEOUT|3 |float| 设置执行风控after线程执行超时时间|