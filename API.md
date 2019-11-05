# API 

这里面描述了一些系统级别的API

### API说明
> 函数名称: current_app
```textmate
函数解释:
    返回当前处于栈顶的App核心对象，也就是作为你的app的代理，让你在全局的任意地方能够访问到你的核心App对象
```
> 函数名称: get_app   -----传入参数: app_name
````textmate
通过传入你的App创建的时候传入的名字获取App对象。
````