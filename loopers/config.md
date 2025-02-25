## 配置模块

与ctpbee核心模块同样的是回测模块也同时持有一份回测配置.
```
{
    "PATTERN": "looper",  // 限定当前APP的运行方式为回测 
    "LOOPER": {
        "initial_capital": 10000,   // 指定初始资金 
        "deal_pattern": "price",   // 指定回测方式  price, simnow, umatch 等等 
        "margin_ratio": {        // 限定保证金占用率 
            "rb2105.SHFE": 0.1,
        },
        "commission_ratio": {    // 回测限定手续费，会自动识别手续费比率或者固定金额 
            "rb2105.SHFE": {
                "close": 0.00001,   // 平昨仓 
                "close_today": 0.00001  // 平今
            },
        },
        "size_map": {
            "rb2105.SHFE": 10   // 合约乘数 
        }
    }
}
```


具体参数意义详见注释, [下一章为数据要求](/loopers/data.md) 

