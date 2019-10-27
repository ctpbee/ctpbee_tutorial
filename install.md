# ctpbee安装 

 ctpbee会在内核上面最大限度保持完整干净整洁 ， 所以ctpbee的安装也会变得异常轻松

## For windows

```
pip3 install ctpbee
```

> 但是请注意目前ctpbee在windows下面仅支持Python37



## For linux

对于linux而言，你需要添加中文环境 ,但是不同的发行版本 经过实验需要区分 

对于ubuntu而言 

````bash
sudo locale-gen zh_CN.GB18030  
````

对于非ubuntu而言

```
# vim /etc/locale.gen
将下面一行添加到其中
zh_CN.GB18030 GB18030 

然后执行
sudo locale-gen
```

> 你需要在你的linux安装gcc工具，具体看你的发行版本 

然后执行 

```
pip3 install ctpbee
```