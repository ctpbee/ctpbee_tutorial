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