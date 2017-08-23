# Abstract API Quick Start
# 抽象API快速入门

If you're already familiar with using the function API, you should feel right
at home using the abstract API. Every function takes the same input, passed
as a dictionary of Numpy arrays:
如果你已经很熟悉函数API，你应用抽象API应该也不会有什么困难。所有的函数的输入都相同，都以字典（dict）或这Numpy数组的格式：

```python
import numpy as np
# 注意所有的ndarray的长度必须相等
inputs = {
    'open': np.random.random(100),
    'high': np.random.random(100),
    'low': np.random.random(100),
    'close': np.random.random(100),
    'volume': np.random.random(100)
}
```

可以直接import函数或者用函数名进行实例化：

```python
from talib import abstract
sma = abstract.SMA
sma = abstract.Function('sma')
```

从现在开始，调用函数基本上就和使用函数API是一样的了：

```python
from talib.abstract import *
output = SMA(input_arrays, timeperiod=25) # calculate on close prices by default
output = SMA(input_arrays, timeperiod=25, price='open') # calculate on opens
upper, middle, lower = BBANDS(input_arrays, 20, 2, 2)
slowk, slowd = STOCH(input_arrays, 5, 3, 0, 3, 0) # uses high, low, close by default
slowk, slowd = STOCH(input_arrays, 5, 3, 0, 3, 0, prices=['high', 'low', 'open'])
```

## 高级用法

在TA-Lib的高级用法中，抽象API的灵活性更好。你甚至可以以``abstract.Function``为父类写一个子类，重新实现``set_input_arrays``来自定义函数接收的输入数据的格式（比如pandas的DataFrame）。

如果想了解函数的更多细节，可以通过查看它的info属性：

```python
print Function('stoch').info
{
  'name': 'STOCH',
  'display_name': 'Stochastic',
  'group': 'Momentum Indicators',
  'input_names': OrderedDict([
    ('prices', ['high', 'low', 'close']),
  ]),
  'parameters': OrderedDict([
    ('fastk_period', 5),
    ('slowk_period', 3),
    ('slowk_matype', 0),
    ('slowd_period', 3),
    ('slowd_matype', 0),
  ]),
  'output_names': ['slowk', 'slowd'],
}

```
或者采用人类易读的格式：
```python
help(STOCH)
str(STOCH)
```

其他关于``Function``的有用特性：

```python
Function('x').function_flags
Function('x').input_names
Function('x').input_arrays
Function('x').parameters
Function('x').lookback
Function('x').output_names
Function('x').output_flags
Function('x').outputs
```

除了直接调用这些函数，它们会保存自己的状态，并且一旦被设置后会保存它们的输入参数。你可以重新设置参数并使用run()函数来重新计算：
```python
SMA.parameters = {'timeperiod': 15}
result1 = SMA.run(input_arrays1)
result2 = SMA.run(input_arrays2)

# 或者设置input_arrays并改变参数：
SMA.input_arrays = input_arrays1
ma10 = SMA(timeperiod=10)
ma20 = SMA(20)
```

了解更多细节，请查看这里的[代码](https://github.com/mrjbq7/ta-lib/blob/master/talib/abstract.pyx#L46)

[文档索引](doc_index.html)
