# QSTrader教程
## <mark>简述</mark>
* 该教程参考QuantStart官网和Github项目Readme.md文档
* QuantStart官网：https://www.quantstart.com
* QuantStart是一个旨在帮助量化从业者的教育性质平台，QSTrader是其主导开发的开源量化系统
* Github项目网址：https://github.com/mhallsmoore/qstrader/tree/master
* 免费开源的交易回测引擎，目前还只是alpha版本，建议只用来做回测。新版在开发中，新版使用整合的  Alpha模块、投资组合构建模块、风险模块、模拟经纪人模块，构建完整的资产组合运维体系。

## <mark>当前特性</mark>
* 使用Python开发，跨平台
* 事件驱动，便于策略研究到实时交易的过渡。
* 回测支持：TickData级别和BarData级别
* 私有模块支持：QSTrader根目录下的private_files目录可以用作私有模块存储路径，该路径不被git追踪。
* 业绩指标支持：QSTrader支持投资组合级别和交易级别业绩衡量，它提供相关的指标统计数据

## <mark>未来预计支持特性</mark>
* 手续费：手续费目前支持IB盈透的北美股票收费标准。滑点和市场冲击预计未来支持。
* 实盘交易：将使用IB盈透的Python API来接入实盘交易，最初只用于北美股票。

## <mark>安装和使用范例</mark>
* 这里使用Python虚拟环境，下面是Windows环境下Python虚拟环境“python3”的创建和激活：
```
pip install virtualenv
mkdir -p E:\GitHubStudy\qstrader3
cd /d E:\GitHubStudy\qstrader3
virtualenv --no-site-packages python3
call python3/Scripts/activate.bat	#deactivate.bat用来退出虚拟环境
```
* 下面 ，我们就使用pip将QSTrader安装为库，QSTrader依赖于NumPy、SciPy、panda、Matplotlib以及许多其他库，如果速度极慢可临时更换pip源（添加-i https://pypi.douban.com/simple/参数），经测试requirements.txt中某些库的版本过低造成安装失败，可修改“==“为”>=“：
```
pip install git+https://github.com/mhallsmoore/qstrader.git	
pip install -r https://raw.githubusercontent.com/mhallsmoore/qstrader/master/requirements.txt
```
* QSTrader和相关依赖库已经安装，接下来配置默认的输入输出路径(首先进入当前用户目录)：
```
mkdir qstrader\examples
mkdir data 
mkdir out
cd data
wget https://raw.githubusercontent.com/mhallsmoore/qstrader/master/data/SPY.csv
cd ..
cd qstrader\examples
wget https://raw.githubusercontent.com/mhallsmoore/qstrader/master/examples/buy_and_hold_backtest.py
```
* 最后我们运行下载下来的buy_and_hold_backtest.py，进行回测(当前在examples目录下)：
```
python buy_and_hold_backtest.py
```
另外，如果想在VSCode中调试代码，需给VSCode配置Python虚拟环境，配置参考https://blog.csdn.net/SpuerCheng/article/details/81485154
* 完成回测后，你将得到如下回测结果
	* Equity curve
	* Drawdown curve
	* Monthly returns heatmap
	* Yearly returns distribution
	* Portfolio-level statistics
	* Trade-level statistics
	

这些图表看起来类似下面这样：
![alt tag](https://s3.amazonaws.com/quantstart/media/images/qstrader-buy-and-hold-tearsheet.png)

* 从buy_and_hold_backtest.py文件来查看一个策略是如何调用QSTrader的API的。进而弄清楚如何通过QSTraderAPI构建自己的策略，以及如何进行回测。

## <mark>QSTrader使用疑问相关</mark>
* QSTrader一直在开发中，所以在beta版本出来之前，可能当前API在后续版本中会出现不兼容的情况。
* 目前项目存在的bug或其他任何疑问可以参看https://github.com/mhallsmoore/qstrader/issues


# QSTrader代码分析

## <mark>Strategy说明</mark>
* 所有`自定义Strategy`都继承自一个纯虚类`AbstractStrategy`，且需实现`calculate_signals(self,event)`方法。作用是消费`TickEvent/BarEvent/SentimentEvent`,生产`SignalEvent`

## <mark>Event说明</mark>
* QSTrader区分Event类型如下（均继承自基类Event），不同的Event有着不同的生产者和消费者，驱动着各自的业务。
	1. TickEventL：生产tick数据
	2. BarEvent：生产bar数据
	3. SignalEvent：策略消费TickEvent/BarEvent，生产SignalEvent
	4. OrderEvent：消费SignalEvent，生产OrderEvent供执行系统使用
	5. FillEvent：封装成交回报，生产FillEvent
	6. SentimentEvent：预测性事件，一般由数据供应商提供的预测性数据生产

## <mark>Backtest和LiveTrading说明</mark>
* QSTrader定义了一个TradingSession类，用于控制回测或实时交易
	* TradingSession类可以配置相关属性来控制回测或实时交易
		```python
		######################################### 配置属性说明 ########################################
		self.config = config	    # 数据来源和结果导出路径
		self.strategy = strategy	# 回测的策略，消费TickEvent/BarEvent/SentimentEvent，生产SignalEvent
		self.tickers = tickers	    # 回测的Ticker列表
		self.equity = PriceParser.parse(equity) # 回测的资金数目：equity*1千万
		self.start_date = start_date	  # 回测开始时间
		self.end_date = end_date	      # 回测结束时间
		self.events_queue = events_queue   # 事件队列缓存区
		
		self.price_handler = price_handler	# 数据供应者，生产TickEvent/BarEvent
		self.portfolio_handler = portfolio_handler # 消费TickEvent/BarEvent来更新投资组合市值，消费	                                                SignalEvent来生产OrderEvent，消费FillEvent来更新投                                              资组合
		self.compliance = compliance
		self.execution_handler = execution_handler # 消费OrderEvent，生产FillEvent
		self.position_sizer = position_sizer # 传入portfolio_handler，portfolio_handler消费SignalEvent时，用于生成OrderEvent时调整下单手数
		self.risk_manager = risk_manager     # 传入portfolio_handler，portfolio_handler消费SignalEvent时，用于生成OrderEvent时进行风控处理
		self.statistics = statistics	# 用于回测结果的分析	
		self.sentiment_handler = sentiment_handler # 消费TickEvent/BarEvent，生产SentimentEvent
		self.title = title			# 传入statistics，用于回测结果的展示和保存
		self.benchmark = benchmark	 # 传入statistics，是某些回测结果的统计基准
		self.session_type = session_type #可取backtest/live，分别代表回测/实盘交易
		self.cur_time = None	# 传入SentimentEvent，标记SentimentEvent的生成时间
		self.end_session_time	# 实时交易时，用于指定交易的截止时间
		
		####################################### 功能函数说明 ##########################################
		# 初始化必要的参数
		_config_session(self)
		# 判断回测/实时交易是否继续
		_continue_loop_condition(self)
		# 执行回测/实时交易，轮询events_queue，直到events_queue为空。
		_run_session(self)
		# 执行回测/实时交易，并且在完成时导出结果
		start_trading(self, testing=False)
		```

