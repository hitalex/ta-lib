# 关于TA-Lib

该项目是技术分析库[TA-LIB](http://ta-lib.org)的Python装饰器，基于Cython而不是SWIG开发。
以下是TA-Lib的主页对其的介绍：

> TA-Lib在交易软件开发者中具有很好的口碑，常用于对金融市场数据进行技术分析。

> * 包含超过150多个指标，例如：ADX、MACD、RSI、Stochastic、布林带（Bollinger Bands）等等
> * 蜡烛图模式识别
> * 提供针对C/C++、Java、Perl、Python和.NET的开源API接口

原始的Python绑定采用[SWIG](http://swig.org)实现，不过该实现不易安装，而且效率也不高。
于是该项目决定采用Cython和Numpy来对TA-Lib进行接口绑定 -- 当前实现比原有基于SWIG的方法效率高2~4倍。

#### [Install TA-Lib](install.html) or [Read the Docs](doc_index.html)
### [安装TA-Lib](install.html) 或 [阅读文档](doc_index.html)

## 示例

与TA-Lib类似，该项目的函数接口API针对TA-Lib的指标提供了一个轻量级的装饰器。

每个函数都会返回一个输出数组，并且对所有参数提供默认值，除非以关键词参数的形式对他们进行赋值。通常来说，这些函数有一个初始的被设置为``NaN``的“回溯”期（是指为了产生输出而需要前期观察的数据的个数）

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

## 抽象API快速入门

如果你已经很熟悉函数API，你应用抽象API应该也不会有什么困难。每个函数的输入相同，都是以一个字典（dict）或Numpy数组：

```python
import numpy as np
# 需要注意所有的ndarray的长度必须相等！
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

从[这里](abstract.html)了解更多TA-Lib的高级用法。

## 支持的指标

我们下面展示所有TA-Lib支持的技术分析函数，以``list``或者基于函数组的``dict``形式（如“交叠研究”、“动量指标”等）

```python
import talib

print talib.get_functions()
print talib.get_function_groups()
```

### Function Groups

* [Overlap Studies](func_groups/overlap_studies.html)
* [Momentum Indicators](func_groups/momentum_indicators.html)
* [Volume Indicators](func_groups/volume_indicators.html)
* [Volatility Indicators](func_groups/volatility_indicators.html)
* [Price Transform](func_groups/price_transform.html)
* [Cycle Indicators](func_groups/cycle_indicators.html)
* [Pattern Recognition](func_groups/pattern_recognition.html)
* [Statistic Functions](func_groups/statistic_functions.html)
* [Math Transform](func_groups/math_transform.html)
* [Math Operators](func_groups/math_operators.html)

#### [Overlap Studies](func_groups/overlap_studies.html)

```
BBANDS               Bollinger Bands
DEMA                 Double Exponential Moving Average
EMA                  Exponential Moving Average
HT_TRENDLINE         Hilbert Transform - Instantaneous Trendline
KAMA                 Kaufman Adaptive Moving Average
MA                   Moving average
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

#### [Momentum Indicators](func_groups/momentum_indicators.html)

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

#### [Volume Indicators](func_groups/volume_indicators.html)

```
AD                   Chaikin A/D Line
ADOSC                Chaikin A/D Oscillator
OBV                  On Balance Volume
```

#### [Volatility Indicators](func_groups/volatility_indicators.html)

```
ATR                  Average True Range
NATR                 Normalized Average True Range
TRANGE               True Range
```

#### [Price Transform](func_groups/price_transform.html)

```
AVGPRICE             Average Price
MEDPRICE             Median Price
TYPPRICE             Typical Price
WCLPRICE             Weighted Close Price
```

#### [Cycle Indicators](func_groups/cycle_indicators.html)

```
HT_DCPERIOD          Hilbert Transform - Dominant Cycle Period
HT_DCPHASE           Hilbert Transform - Dominant Cycle Phase
HT_PHASOR            Hilbert Transform - Phasor Components
HT_SINE              Hilbert Transform - SineWave
HT_TRENDMODE         Hilbert Transform - Trend vs Cycle Mode
```

#### [Pattern Recognition](func_groups/pattern_recognition.html)

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

#### [Statistic Functions](func_groups/statistic_functions.html)

```
BETA                 Beta
CORREL               Pearson's Correlation Coefficient (r)
LINEARREG            Linear Regression
LINEARREG_ANGLE      Linear Regression Angle
LINEARREG_INTERCEPT  Linear Regression Intercept
LINEARREG_SLOPE      Linear Regression Slope
STDDEV               Standard Deviation
TSF                  Time Series Forecast
VAR                  Variance
```
