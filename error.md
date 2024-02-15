## 报错合集

---
在这里我将ctpbee的一些常见错误以及解决办法为你呈现

##### 安装错误

1. locale error 当你运行程序中出现了`locale`的错误，那么你的linux环境下的中文环境没并没有安装，参见[安装](install.md)

2. runtime error 请检查你的交易服务器地址和行情服务器地址是否有出错

3. 仿真穿透测试出错 目前ctpbee没有内置穿透接口， 但是你可以下载[ctpbee_api](https://github.com/ctpbee/ctpbee_api)
   和接口文件，替换ctp目录下面的文件,
   包含动态依赖库以及函数库等文件 然后python setup.py build 进行编译，如果没有报错, 那么pip install -e . 安装包即可
   运行你写的测试程序即可

#### feedback

如果你的使用过程中遇到问题, 请发[issue](https://github.com/ctpbee/ctpbee/issues/new)
