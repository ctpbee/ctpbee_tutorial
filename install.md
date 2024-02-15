# ctpbee安装

ctpbee会在内核上面最大限度保持完整干净整洁, 所以ctpbee的安装也会变得异常轻松

## For windows

```
pip3 install ctpbee
```

## For linux

> 你需要在你的linux安装gcc工具，具体看你的发行版本

然后执行安装命令

```
pip3 install ctpbee
```

不同于windows, 你需要添加中文环境

```bash 
#  linux用户快速生成中文支持/ windows用户无须设置 
## for root 
bee init-locale 
## for user, xxx为你的用户密码, 注意你当前用户需要拥有sudo权限 
bee init-locale --password xxxxx 

```

## for Mac

> mac需要首先使用pip和github进行安装ctpbee_api

```bash 
# 如果你的网速不行, 可以使用gitee导入此项目
pip3 install git+https://github.com/ctpbee/ctpbee_api

pip3 install ctpbee
```