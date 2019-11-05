## 配置模块

ctpbee采用了类似于flask的配置模块，可能这就是所有量化框架中扩展性最强的配置项了，希望你能爱上它。

我们每个核心App都有持有一份config，它是一个字典格式的配置。为了方便你进行载入自定义的配置，我们转移了flask里面的部分代码用来构建
`ctpbee`的配置系统
下面我将从两个方面来阐述它。
- 配置载入
- 配置项解释
---
#### 配置载入
我们为了你能够快速载入配置项，编写了一系列的API， 使得你能够快速将配置文件载入到`ctpbee`中去。
- `json support`

```python
app.config.from_json(json_path)
```

- `mapping support`

```python
# 支持一个字典格式的映射
app.config.from_mapping(mapping_obj)
```

- `pyfile support`

```python
app.config.from_mapping(pyfile_path)
```

- `obj support`

```python
# 实例对象的支持
app.config.from_obj(obj)
```

上述四个API应该能帮助你很灵活地传入你的配置。

#### 配置项
