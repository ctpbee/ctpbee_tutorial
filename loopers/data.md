# 数据模块

## 数据来源
`ctpbee`本身框架只提供一个运行环境，并不直接提供数据来源， 所以此处我们需要使用`quantdata`， 注意此处的我们仅仅使用`quantdata`的历史数据

#### 安装`quantdata` 
```bash
pip install quantadata
```
> 注意由于`tqsdk`暂时不支持3.8版本的Python，推荐使用3.7


### 取的数据
```python
from quantdata import QuantPlatform

platform = QuantPlatform(owner="tqsdk", support_platform="ctpbee", method="client")
```





