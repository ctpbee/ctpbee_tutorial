# 数据模块

## 数据来源

`ctpbee`本身回测功能只提供一个运行环境，并不直接提供数据来源，需要一个标准的`Iterator`

根据你的数据要求格式而言 进行转换到下面标准格式即可

### 标准数据格式

我们对于历史K线数据期望一个以下格式

```python
hope = [
    {"high_price": 1123,
     "low_price": 1054,
     "open_price": 1124,
     "close_price": 1200,
     "local_symbol": "ag1912.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
     },
    {"high_price": 1103,
     "low_price": 1014,
     "open_price": 1124,
     "close_price": 1200,
     "local_symbol": "ag1912.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
     },
]
```

对于Tick就参见下面的 [Tick格式](https://github.com/ctpbee/ctpbee/blob/2fcc9841db5b22816fd136bcc6d6caebca167681/ctpbee/constant.py#L332)

注意你必须将你的历史数据转换成目标格式数据才可以进行回测,

在通过你的读取完数据格式后 你可能已经拥有多个品种的数据，可以很轻松的将其送到`ctpbee`里面来去， 举例来说

```python
hope1 = [
    {"high_price": 1123,
     "low_price": 1054,
     "open_price": 1124,
     "close_price": 1200,
     "local_symbol": "ag1912.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
     },
    {"high_price": 1103,
     "low_price": 1014,
     "open_price": 1124,
     "close_price": 1200,
     "local_symbol": "ag1912.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
     },
]

hope2 = [
    {"high_price": 1123,
     "low_price": 1054,
     "open_price": 1124,
     "close_price": 1200,
     "local_symbol": "rb2105.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
     },
    {"high_price": 1103,
     "low_price": 1014,
     "open_price": 1124,
     "close_price": 1200,
     "local_symbol": "rb2105.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
     },
]

data = [hope1, hope2]

app.add_data(*data)
# 或者使用 
# app.add_data(hope1, hope2)
```

`ctpbee`会自动遍历所有不同品种的数据，对齐时间格式。

但是注意 `注意需要你提前载入配置信息 `

此种情况下你的on_bar接口或者on_tick接口会根据数据种类自动触发.

回测报单接口和实盘保持一致。如果出现问题 请发送电子邮件给[我](somewheve@gmail.com)


