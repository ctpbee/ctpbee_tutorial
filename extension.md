# 插件系统

> `ctpbee`提供了一些比较有意思的插件, 使得一些功能变成可插拔式的. 这样可以避免多余的性能浪费以及延时浪费

以下是提供的插件列表

--- 

- [ctpbee_kline](https://github.com/ctpbee/ckline) k线支持插件,提供一分钟k线快速实现
- [ctpbee_webline](https://github.com/ctpbee/webline) 使用flask编写后端, vue3+tauri编译前端. 通过`with_tools`接口接入策略
  可为策略提供控制台介入功能, 同时前端应用不占用当前进程资源, 节约性能.

> 如果你想参与到插件开发中来, 推荐参阅上述代码