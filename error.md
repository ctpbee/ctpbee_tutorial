## 报错合集

---
在这里我将ctpbee的一些常见错误以及解决办法为你呈现

##### 安装错误

- locale error 当你运行程序中出现了`locale`的错误，那么你的linux环境下的中文环境没并没有安装，参见[安装](install.md)

- runtime error 请检查你的交易服务器地址和行情服务器地址是否有出错

- 仿真穿透测试出错 目前`ctpbee`没有内置穿透接口， 但是你可以下载[ctpbee_api](https://github.com/ctpbee/ctpbee_api)
  和接口文件，替换`ctp`目录下面的文件(参见下述替换教程),
  包含动态依赖库以及函数库等文件 然后`python setup.py build` 进行编译，如果没有报错, 那么`pip install -e . `安装包即可
  运行你写的测试程序即可
- `4097` 一般都是dll/so版本与服务器支持版本不一致导致

#### 更换依赖库文件教程

- `windows dll`替换教程

```bash
pip3 install ctpbee_api
pip3 show ctpbee_api # 会显示下面信息

Name: ctpbee_api
Version: 0.45
Summary: Trading API support for China Future
Home-page: https://github.com/ctpbee/ctpbee_api
Author: somewheve
Author-email: somewheve@gmail.com
License: MIT
Location: c:\users\somew\appdata\local\programs\python\python39\lib\site-packages
Requires:
Required-by: ctpbee

# 然后找到Location目录  并添加ctpbee_api 如果要替换ctp, 那么目录路径再增加一个ctp 然后复制同名dll到下方路径
c:\users\somew\appdata\local\programs\python\python39\lib\site-packages\ctpbee_api\ctp
```

- `linux so`替换教程

```bash 

# 假设你当前目录下已经下载 thosttraderapi_se.so 和 thostmduserapi_se.so 
# linux使用的即时编译技术 所以需要在编译之前替换so文件  先下载ctpbee_api项目
git clone https://github.com/ctpbee/ctpbee_api

# 复制并替换依赖库  此处注意需要添加lib前缀
cp ./thosttraderapi_se.so ./ctpbee_api/ctpbee_api/ctp/libthosttraderapi_se.so
cp ./thostmduserapi_se.so ./ctpbee_api/ctpbee_api/ctp/libthostmduserapi_se.so

# 进入到以下目录, 然后使用pip3进行编译安装
cd ./ctpbee_api
pip3 install .
```

- 对于open_ctp用户上述教程需要的dll请前往 https://github.com/openctp/openctp/tree/master/ctp2TTS 进行下载相关库文件
  目前ctpbee预编译文件使用的`6.6.1`版本

#### Feedback

如果你的使用过程中遇到问题, 请发[issue](https://github.com/ctpbee/ctpbee/issues/new)
