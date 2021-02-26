# ctpbee安装 

 ctpbee会在内核上面最大限度保持完整干净整洁 ， 所以ctpbee的安装也会变得异常轻松

## For windows

```
pip3 install ctpbee
```

> 但是请注意目前ctpbee在windows下面仅支持Python37



## For linux

> 你需要在你的linux安装gcc工具，具体看你的发行版本 

然后执行安装命令 

```
pip3 install ctpbee
```

不同于windows, 你需要添加中文环境

```bash 
sudo ctpbee -auto generate
```

> 如果你的版本号小于0.45，那么请升级到>=0.45， ctpbee代码会保持向前兼容
>
> 如果你是使用虚拟环境请使用 sudo /path/to/python/bin/ctpbee -auto generate
> 注意此处说的是虚拟环境目录下面的bin目录下的ctpbee

## for Mac

不好意思我已经尝试过用ios的接口编译ctp接口，但是仍然无法正常进行工作。所以暂时无法支持！



## 注意 for [QA](https://github.com/QUANTAXIS/QUANTAXIS)

我们提供了QA支持，请使用下面代码进行安装
注意下面的 `--install-option`不是必需的，同时注意你需要安装本地的mongodb环境
#### for linux
```
pip install -r < (echo "ctpbee[QA_SUPPORT] --install-option="--fix=true" --install-option="--uri="https://mirrors.aliyun.com/pypi/simple")
```
然后命令行执行，如果你已经安装好了QA数据那么你可以忽略以下步骤.我会在回测那边告诉你们如何进行使用
```
quantaxis
```
再输入
```text
save future_min
```