# 函数API示例

与TA-Lib类似，该项目的函数接口API针对TA-Lib的指标提供了一个轻量级的装饰器。

每个函数都会返回一个输出数组，并且对所有参数提供默认值，除非以关键词参数的形式对他们进行赋值。通常来说，这些函数有一个初始的被设置为``NaN``的“回溯”期（是指为了产生输出而需要前期观察的数据的个数）。

以下所有的样例都使用了提供的函数API：

```python
import numpy
import talib

close = numpy.random.random(100)
```

计算收盘价（close price）的简单移动平均值：

```python
output = talib.SMA(close)
```

计算布林带，并使用三重指数移动平均：

```python
from talib import MA_Type

upper, middle, lower = talib.BBANDS(close, matype=MA_Type.T3)
```

计算收盘价的动量，其中时间段设置为5：

```python
output = talib.MOM(close, timeperiod=5)
```

所有函数的文档：

* [交叠研究](func_groups/overlap_studies.html)
* [动量指标](func_groups/momentum_indicators.html)
* [交易量指标](func_groups/volume_indicators.html)
* [波动指标](func_groups/volatility_indicators.html)
* [模式识别](func_groups/pattern_recognition.html)
* [周期指标](func_groups/cycle_indicators.html)
* [统计函数](func_groups/statistic_functions.html)
* [价格变换](func_groups/price_transform.html)
* [数学变换](func_groups/math_transform.html)
* [数学算子](func_groups/math_operators.html)

[文档索引](doc_index.html)
[FLOAT_RIGHTNext: 使用抽象API](abstract.html)
