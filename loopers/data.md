# 数据模块

## 数据来源
`ctpbee`本身框架只提供一个运行环境，并不直接提供数据来源， 所以此处我们需要使用`quantdata`， 注意此处的我们仅仅使用`quantdata`的历史数据

#### 安装`quantdata` 
```bash
pip install quantadata
```
> 注意由于`tqsdk`暂时不支持3.8版本的Python，推荐使用3.7


### 获取数据

为了提供ctpbee历史数据 我特意编写了[quantdata](https://github.com/QUANTAXIS/quantdata) ，当前的***quantdata***并不完善,此项:我将在下一个阶段来着重开发它
 
```python
from quantdata import QuantPlatform

platform = QuantPlatform(owner="tqsdk", support_platform="ctpbee", method="client")
```

### 标准数据格式
我们对于`add_data`接口期望的历史数据是一个这样的格式

```python
hope = [
    {"high_price":1123 ,
     "low_price":1054, 
     "open_price": 1124, 
     "close_price": 1200,
     "local_symbol":"ag1912.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
    },
   {"high_price":1103 ,
     "low_price":1014, 
     "open_price": 1124, 
     "close_price": 1200,
     "local_symbol":"ag1912.SHFE",
     "datetime": "你需要将原始数据其转换为一个datetime类型"
    },
]
```
注意你必须将你的历史数据转换成目标格式数据才可以进行回测， 记住这6个字段的数据是必要的.
当然如果你有额外的字段，可以自行添加， 同样你的on_bar接口或者on_tick接口都可以访问到数据.


ctpbee目前并不是开箱即用的，仍然需要你有一定的开发知识。 [参见开头](result.md)
 





