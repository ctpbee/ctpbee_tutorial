## 配置模块

与ctpbee核心模块同样的是回测模块也同时持有一份回测配置。
```json
{
  "looper":
     {
         "initial_capital": 100000,
         "commission": 0.005,
         "deal_pattern": "price",
         "size_map": {"ag1912.SHFE": 15},
         "today_commission": 0.005,
         "yesterday_commission": 0.02,
         "close_commission": 0.005,
         "slippage_sell": 0,
         "slippage_cover": 0,
         "slippage_buy": 0,
         "slippage_short": 0,
         "close_pattern": "yesterday",
     },
  "strategy": 
    {
    }
 }
```
下面我为你解释上述字段的含义
- initial_capital  ***初始金额***
- commission       ***开仓手续费***
- deal_pattern     ***撮合方式***
- size_map          
- today_commission  ***平今手续费比率***
- yesterday_commission ***平昨手续费比率***
- close_commission   ***平手续费比率***
- slippage_sell    ***平空头手滑点***
- slippage_cover   ***平多头滑点***
- slippage_buy   ***买多滑点***
- slippage_short ***卖空滑点***
- close_pattern ***优先平仓模式* **