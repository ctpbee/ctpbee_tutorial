## 数据记录模块
一个标准的交易系统，肯定要维护一份交易系统数据， 那么我将为你介绍我们`ctpbee`的核心数据记录器。它由两个部分组成,
`recorder`和`position_manager`。现在我将向你介绍如何去访问到他们。

#### recorder
此部分描述了recorder一些基本调用API

`get_all_active_order()`
> 返回所有未成交的单子

`get_all_orders()`
