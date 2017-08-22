# TA-Lib

[![](https://api.travis-ci.org/mrjbq7/ta-lib.svg)](https://travis-ci.org/mrjbq7/ta-lib)

This is a Python wrapper for [TA-LIB](http://ta-lib.org) based on Cython
instead of SWIG. From the homepage:
该项目是技术分析库[TA-LIB](http://ta-lib.org)的Python装饰器，基于Cython而不是SWIG开发。
以下是TA-Lib的主页对其的介绍：

> TA-Lib is widely used by trading software developers requiring to perform
> technical analysis of financial market data.
> TA-Lib在交易软件开发者中具有很好的口碑，常用于对金融市场数据进行技术分析。

> * Includes 150+ indicators such as ADX, MACD, RSI, Stochastic, Bollinger
>   Bands, etc.
> * 包含超过150多个指标，例如：ADX、MACD、RSI、Stochastic、布林带（Bollinger Bands）等等
> * Candlestick pattern recognition
> * 蜡烛图模式识别
> * Open-source API for C/C++, Java, Perl, Python and 100% Managed .NET
> * 提供针对C/C++、Java、Perl、Python和.NET的开源API接口

The original Python bindings use [SWIG](http://swig.org) which unfortunately
are difficult to install and aren't as efficient as they could be. Therefore
this project uses Cython and Numpy to efficiently and cleanly bind to TA-Lib
-- producing results 2-4 times faster than the SWIG interface.
原始的Python绑定采用[SWIG](http://swig.org)实现，不过该实现不易安装，而且效率也不高。
于是该项目决定采用Cython和Numpy来对TA-Lib进行接口绑定 -- 当前实现比原有基于SWIG的方法效率高2~4倍。

## Installation
## 安装

You can install from PyPI:
可以从PyPI中安装：

```
$ pip install TA-Lib
```

Or checkout the sources and run ``setup.py`` yourself:
或者下载源代码，并自行执行``setup.py``：

```
$ python setup.py install
```

### Troubleshooting
### 疑难解答

Sometimes installation will produce build errors like this:
有时安装过程中可能出现如下错误：
```
func.c:256:28: fatal error: ta-lib/ta_libc.h: No such file or directory
compilation terminated.
```

or:
或者：

```
common.obj : error LNK2001: unresolved external symbol TA_SetUnstablePeriod
common.obj : error LNK2001: unresolved external symbol TA_Shutdown
common.obj : error LNK2001: unresolved external symbol TA_Initialize
common.obj : error LNK2001: unresolved external symbol TA_GetUnstablePeriod
common.obj : error LNK2001: unresolved external symbol TA_GetVersionString
```

This typically means that it can't find the underlying ``TA-Lib`` library, a
dependency which needs to be installed.  On Windows, this could be caused by
installing the 32-bit binary distribution of the underlying ``TA-Lib`` library,
but trying to use it with 64-bit Python.
这通常意味着无法找到底层的``TA-Lib``库，该库需要作为依赖先行安装。在Windows平台，这种错误信息也可能是由于安装了32位的``TA-Lib``库，但是Python是64位的。

Sometimes installation will fail with errors like this:
有时安装过程中也会出现如下错误：

```
talib/common.c:8:22: fatal error: pyconfig.h: No such file or directory
 #include "pyconfig.h"
                      ^
compilation terminated.
error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
```

This typically means that you need the Python headers, and should run
something like:
这表示你需要安装Python头文件，接下来你需要运行如下命令：

```
$ sudo apt-get install python3-dev
```

### Dependencies
### 依赖

To use TA-Lib for python, you need to have the
[TA-Lib](http://ta-lib.org/hdr_dw.html) already installed:
在使用TA-Lib的Python接口前，你需要首先安装[TA-Lib](http://ta-lib.org/hdr_dw.html)

##### Mac OS X

```
$ brew install ta-lib
```

##### Windows

Download [ta-lib-0.4.0-msvc.zip](http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-msvc.zip)
and unzip to ``C:\ta-lib``
下载[ta-lib-0.4.0-msvc.zip](http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-msvc.zip)并将其解压至 ``C:\ta-lib``

> This is a 32-bit release.  If you want to use 64-bit Python, you will need
> to build a 64-bit version of the library.
> 这是32位的发行版。如果你使用的Python是64位的，你需要构建一个64位版本的TA-Lib库。

##### Linux

Download [ta-lib-0.4.0-src.tar.gz](http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz) and:
下载[ta-lib-0.4.0-src.tar.gz](http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz)并运行以下命令：

```
$ untar and cd
$ ./configure --prefix=/usr
$ make
$ sudo make install
```

> If you build ``TA-Lib`` using ``make -jX`` it will fail but that's OK!
> Simply rerun ``make -jX`` followed by ``[sudo] make install``.
> 如果你用``make-jX``命令构建``TA-Lib``库，过程中虽然会报错但没有关系。直接在运行完``make-jX``后再运行``[sudo] make install``.

## Function API
## 函数接口API

Similar to TA-Lib, the Function API provides a lightweight wrapper of the
exposed TA-Lib indicators.
与TA-Lib类似，该项目的函数接口API针对TA-Lib的指标提供了一个轻量级的装饰器。

Each function returns an output array and have default values for their
parameters, unless specified as keyword arguments. Typically, these functions
will have an initial "lookback" period (a required number of observations
before an output is generated) set to ``NaN``.
每个函数都会返回一个输出数组，并且对所有参数提供默认值，除非以关键词参数的形式对他们进行赋值。通常来说，这些函数有一个初始的被设置为``NaN``的“回溯”期（是指为了产生输出而需要前期观察的数据的个数）

For convenience, the Function API supports both ``numpy.ndarray`` and
``pandas.Series`` types.
为了使用方便，函数API不仅支出``numpy.ndarray``，同时也支持``pandas.Series``类型。

All of the following examples use the Function API:
以下所有的样例都使用了提供的函数API：

```python
import numpy
import talib

close = numpy.random.random(100)
```

Calculate a simple moving average of the close prices:
计算收盘价（close price）的简单移动平均值：

```python
output = talib.SMA(close)
```

Calculating bollinger bands, with triple exponential moving average:
计算布林带，并使用三级指数移动平均：

```python
from talib import MA_Type

upper, middle, lower = talib.BBANDS(close, matype=MA_Type.T3)
```

Calculating momentum of the close prices, with a time period of 5:
计算收盘价的动量，其中时间段设置为5：

```python
output = talib.MOM(close, timeperiod=5)
```

## Abstract API
## 抽象API

If you're already familiar with using the function API, you should feel right
at home using the Abstract API.
如果你已经很熟悉函数API，你应用抽象API应该也不会有什么困难。

Every function takes a collection of named inputs, either a ``dict`` of
``numpy.ndarray`` or ``pandas.Series``, or a ``pandas.DataFrame``. If a
``pandas.DataFrame`` is provided, the output is returned as a
``pandas.DataFrame`` with named output columns.
每个函数接收几个命名的输入，可能是``numpy.ndarray``的``dict``、``pandas.Series``，或者是一个``pandas.DataFrame``。如果参数是``pandas.DataFrame``类型，那么输出也是``pandas.DataFrame``类型，其中包括命名的数据列。

For example, inputs could be provided for the typical "OHLCV" data:
例如，可以以如下形式提供“OHLCV”数据：

```python
import numpy as np

# note that all ndarrays must be the same length!
inputs = {
    'open': np.random.random(100),
    'high': np.random.random(100),
    'low': np.random.random(100),
    'close': np.random.random(100),
    'volume': np.random.random(100)
}
```

Functions can either be imported directly or instantiated by name:
可以直接import函数或者用函数名进行实例化：

```python
from talib import abstract

# directly
sma = abstract.SMA

# or by name
sma = abstract.Function('sma')
```

From there, calling functions is basically the same as the function API:
从现在开始，调用函数基本上就和使用函数API是一样的了：

```python
from talib.abstract import *

# uses close prices (default)
# 使用收盘价（默认）
output = SMA(inputs, timeperiod=25)

# uses open prices
# 使用开盘价
output = SMA(inputs, timeperiod=25, price='open')

# uses close prices (default)
# 使用收盘价（默认）
upper, middle, lower = BBANDS(inputs, 20, 2, 2)

# uses high, low, close (default)
# 使用最高、最低和收盘价（默认）
slowk, slowd = STOCH(inputs, 5, 3, 0, 3, 0) # uses high, low, close by default

# uses high, low, open instead
# 使用最高、最低和开盘价
slowk, slowd = STOCH(inputs, 5, 3, 0, 3, 0, prices=['high', 'low', 'open'])
```

## Supported Indicators and Functions
## 支持的指标和函数

We can show all the TA functions supported by TA-Lib, either as a ``list`` or
as a ``dict`` sorted by group (e.g. "Overlap Studies", "Momentum Indicators",
etc):
我们下面展示所有TA-Lib支持的技术分析函数，以``list``或者基于函数组的``dict``形式（如“交叠研究”、“动量指标”等）

```python
import talib

# list of functions
# 函数列表
print talib.get_functions()

# dict of functions by group
# 以函数组组织的dict
print talib.get_function_groups()
```

### Indicator Groups
### 指标组

* Overlap Studies
* 交叠研究
* Momentum Indicators
* 动量指标
* Volume Indicators
* 交易量指标
* Volatility Indicators
* 波动指标
* Price Transform
* 价格变换
* Cycle Indicators
* 周期指标
* Pattern Recognition
* 模式识别

#### Overlap Studies
#### 交叠研究
```
BBANDS               布林带（ Bollinger Bands）
DEMA                 双指数移动平均（Double Exponential Moving Average）
EMA                  指数移动平均（Exponential Moving Average）
HT_TRENDLINE         Hillber变换 - 瞬时趋势线（Hilbert Transform - Instantaneous Trendline）
KAMA                 Kaufman Adaptive Moving Average
MA                   移动平均（Moving average）
MAMA                 MESA Adaptive Moving Average
MAVP                 Moving average with variable period
MIDPOINT             MidPoint over period
MIDPRICE             Midpoint Price over period
SAR                  Parabolic SAR
SAREXT               Parabolic SAR - Extended
SMA                  Simple Moving Average
T3                   Triple Exponential Moving Average (T3)
TEMA                 Triple Exponential Moving Average
TRIMA                Triangular Moving Average
WMA                  Weighted Moving Average
```

#### Momentum Indicators
```
ADX                  Average Directional Movement Index
ADXR                 Average Directional Movement Index Rating
APO                  Absolute Price Oscillator
AROON                Aroon
AROONOSC             Aroon Oscillator
BOP                  Balance Of Power
CCI                  Commodity Channel Index
CMO                  Chande Momentum Oscillator
DX                   Directional Movement Index
MACD                 Moving Average Convergence/Divergence
MACDEXT              MACD with controllable MA type
MACDFIX              Moving Average Convergence/Divergence Fix 12/26
MFI                  Money Flow Index
MINUS_DI             Minus Directional Indicator
MINUS_DM             Minus Directional Movement
MOM                  Momentum
PLUS_DI              Plus Directional Indicator
PLUS_DM              Plus Directional Movement
PPO                  Percentage Price Oscillator
ROC                  Rate of change : ((price/prevPrice)-1)*100
ROCP                 Rate of change Percentage: (price-prevPrice)/prevPrice
ROCR                 Rate of change ratio: (price/prevPrice)
ROCR100              Rate of change ratio 100 scale: (price/prevPrice)*100
RSI                  Relative Strength Index
STOCH                Stochastic
STOCHF               Stochastic Fast
STOCHRSI             Stochastic Relative Strength Index
TRIX                 1-day Rate-Of-Change (ROC) of a Triple Smooth EMA
ULTOSC               Ultimate Oscillator
WILLR                Williams' %R
```

#### Volume Indicators
```
AD                   Chaikin A/D Line
ADOSC                Chaikin A/D Oscillator
OBV                  On Balance Volume
```

#### Cycle Indicators
```
HT_DCPERIOD          Hilbert Transform - Dominant Cycle Period
HT_DCPHASE           Hilbert Transform - Dominant Cycle Phase
HT_PHASOR            Hilbert Transform - Phasor Components
HT_SINE              Hilbert Transform - SineWave
HT_TRENDMODE         Hilbert Transform - Trend vs Cycle Mode
```

#### Price Transform
```
AVGPRICE             Average Price
MEDPRICE             Median Price
TYPPRICE             Typical Price
WCLPRICE             Weighted Close Price
```

#### Volatility Indicators
```
ATR                  Average True Range
NATR                 Normalized Average True Range
TRANGE               True Range
```

#### Pattern Recognition
```
CDL2CROWS            Two Crows
CDL3BLACKCROWS       Three Black Crows
CDL3INSIDE           Three Inside Up/Down
CDL3LINESTRIKE       Three-Line Strike
CDL3OUTSIDE          Three Outside Up/Down
CDL3STARSINSOUTH     Three Stars In The South
CDL3WHITESOLDIERS    Three Advancing White Soldiers
CDLABANDONEDBABY     Abandoned Baby
CDLADVANCEBLOCK      Advance Block
CDLBELTHOLD          Belt-hold
CDLBREAKAWAY         Breakaway
CDLCLOSINGMARUBOZU   Closing Marubozu
CDLCONCEALBABYSWALL  Concealing Baby Swallow
CDLCOUNTERATTACK     Counterattack
CDLDARKCLOUDCOVER    Dark Cloud Cover
CDLDOJI              Doji
CDLDOJISTAR          Doji Star
CDLDRAGONFLYDOJI     Dragonfly Doji
CDLENGULFING         Engulfing Pattern
CDLEVENINGDOJISTAR   Evening Doji Star
CDLEVENINGSTAR       Evening Star
CDLGAPSIDESIDEWHITE  Up/Down-gap side-by-side white lines
CDLGRAVESTONEDOJI    Gravestone Doji
CDLHAMMER            Hammer
CDLHANGINGMAN        Hanging Man
CDLHARAMI            Harami Pattern
CDLHARAMICROSS       Harami Cross Pattern
CDLHIGHWAVE          High-Wave Candle
CDLHIKKAKE           Hikkake Pattern
CDLHIKKAKEMOD        Modified Hikkake Pattern
CDLHOMINGPIGEON      Homing Pigeon
CDLIDENTICAL3CROWS   Identical Three Crows
CDLINNECK            In-Neck Pattern
CDLINVERTEDHAMMER    Inverted Hammer
CDLKICKING           Kicking
CDLKICKINGBYLENGTH   Kicking - bull/bear determined by the longer marubozu
CDLLADDERBOTTOM      Ladder Bottom
CDLLONGLEGGEDDOJI    Long Legged Doji
CDLLONGLINE          Long Line Candle
CDLMARUBOZU          Marubozu
CDLMATCHINGLOW       Matching Low
CDLMATHOLD           Mat Hold
CDLMORNINGDOJISTAR   Morning Doji Star
CDLMORNINGSTAR       Morning Star
CDLONNECK            On-Neck Pattern
CDLPIERCING          Piercing Pattern
CDLRICKSHAWMAN       Rickshaw Man
CDLRISEFALL3METHODS  Rising/Falling Three Methods
CDLSEPARATINGLINES   Separating Lines
CDLSHOOTINGSTAR      Shooting Star
CDLSHORTLINE         Short Line Candle
CDLSPINNINGTOP       Spinning Top
CDLSTALLEDPATTERN    Stalled Pattern
CDLSTICKSANDWICH     Stick Sandwich
CDLTAKURI            Takuri (Dragonfly Doji with very long lower shadow)
CDLTASUKIGAP         Tasuki Gap
CDLTHRUSTING         Thrusting Pattern
CDLTRISTAR           Tristar Pattern
CDLUNIQUE3RIVER      Unique 3 River
CDLUPSIDEGAP2CROWS   Upside Gap Two Crows
CDLXSIDEGAP3METHODS  Upside/Downside Gap Three Methods
```
