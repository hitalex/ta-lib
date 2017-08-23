# 安装

你可以通过PyPI安装：

```
$ pip install TA-Lib
```

或者下载源代码，然后自行运行``setup.py``：

```
$ python setup.py install
```

### 安装错误的疑难解答

```
func.c:256:28: fatal error: ta-lib/ta_libc.h: No such file or directory
compilation terminated.
```

如果你遇到这种安装错误，这通常意味着无法找到底层的``TA-Lib``库，该库需要作为依赖先行安装。

# 依赖
在使用TA-Lib的Python接口前，你需要首先安装[TA-Lib](http://ta-lib.org/hdr_dw.html)

#### Mac OS X
```
$ brew install ta-lib
```

#### Windows
下载[ta-lib-0.4.0-msvc.zip](http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-msvc.zip)并将其解压至 ``C:\ta-lib``

#### Linux
下载[ta-lib-0.4.0-src.tar.gz](http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz)并运行以下命令：

```
$ untar and cd
$ ./configure --prefix=/usr
$ make
$ sudo make install
```

> 如果你用``make-jX``命令构建``TA-Lib``库，过程中虽然会报错但没有关系。直接在运行完``make-jX``后再运行``[sudo] make install``.

[文档索引](doc_index.html)
[FLOAT_RIGHTNext: 使用函数API](func.html)
