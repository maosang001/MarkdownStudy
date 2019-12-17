# Python教程
***
##  <font color=yellow>简述</font>
* 该教程参考廖雪峰的python教程：https://www.liaoxuefeng.com/wiki/1016959663602400
* Python是一门简单优雅、跨平台的高级编程语言。在人工智能、大数据、云计算领域有广泛应用。
* python目前有2.x和3.x两个版本，这两个版本互不兼容。
***
## <font color=yellow>Python安装使用</font>
* 安装过程略（自行百度）
* 启动cmd命令行，输入python回车，看到 >>> 就表示进入了python交互环境。输入exit()回车就可以退出python交互环境（直接关闭命令行窗口也可以）。
* 使用python file.py命令执行file.py文件
***
## <font color=yellow>输入和输出</font>
* 输出使用print()函数
	```python
	print('hello')
	print(100+200)
	print('100+200=',100+200)
	```

* 输入使用input()函数
	```python
	x = input()
	```
***
## <font color=yellow>Python基础</font>

* Python语法采用缩进方式，并且应该坚持使用4个空格缩进（在文本编辑器中，需要设置把Tab自动转换为4个空格）。当语句以`:`结尾时，缩进的语句视为代码块
* Pyhon对大小写敏感
* Python行注释使用#

### <font color=Orange>数据类型和变量</font>

* int类型：整数，Python可以处理任意大小的整数。

* float类型：浮点数，python默认提供17位数字的精度（包括整数位在内）

* str类型：字符串，使用`'`或`"`括起来，`r''`表示内部的字符串不转义，`'''...'''`可以省略写`\n`。

* bool类型：布尔值，包括`True`和`False`两种值（注意大小写），支持`and`、`or`、`not`运算

* None类型：空值。

  ```python
  # int类型
  i = 1
  i = 0x1a2c
  # float类型
  i = 1.2
  i = 1.2e-10
  # str类型
  i = 'abc'
  i = "abc"
  i = r'\nabc'	#不转义
  i = '''line1    #等效于 i = 'line1\nline2\nline3'
  line2
  line3'''
  # bool类型
  i = True
  i = False
  i = True and True
  i = True or False
  i = not False
  # 空值
  i = None
  ```
  
* `list`和`tuple`：`list`和`tuple`都是一种有序集合，区别是`tuple`一旦初始化，就不能修改

  ```python
  ####################################### list ##########################################
  #1、 list可以包含不同类型的元素
  list = [0,'1',2]
  #2、 获取list元素个数
  length = len(list)
  #3、通过索引访问list元素
  i = list[0]		# 第一个元素
  i = list[-1]	# 倒数第一个元素
  #4、追加元素到末尾
  list.append(3)
  #5、追加元素到指定位置
  list.insert(4,'4')
  #6、删除末尾元素
  list.pop()
  #7、删除指定位置元素
  list.pop(3)
  #8、替换指定为位置元素值
  list[0]='0'
  
  ####################################### tuple #########################################
  #1、tuple没有append、insert、pop等函数，因为它初始化后不能修改，但访问元素的方式和list一样
  #2、单元素tuple
  tuple = (1,)
  #3、tuple内置list元素，list元素修改，但tuple对其指向不变，也符合tuple一旦初始化不能修改的规则
  tuple = (1,['a','b'])
  tuple[1].append('c')
  ```
  
* `dict`和`set`：`dict`和`list`的区别是，dict内存占用大，但查找和插入不会随元素的增多而变慢。`list`则相反。

  <font color=red>`dict`的`key`必须是不可变对象</font>，`set`和`dict`类似，是一组`key`的集合（无序），唯一区别是不存储`value`，`set`中没有重复`key`，<font color=red>`set`的`key`必须是不可变对象</font>
  
  说明：不可变对象就是，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回。
  
  ```python
  ################################ dict ###########################
  #1、 dict初始化
  dic = {'k1':'v1','k2':'v2'}
  #2、判断key是否存在
  >>> 'k' in dic
  False
  >>> dic.get('k',-1)	#可以自定义key不存在时返回的值
  -1
  #3、删除元素
  dic.pop('k1')
  
  ############################ set ##################################
  #1、set初始化
  s = {1,2,3}
  s = set([1,2,3])
  #2、添加元素
  s.add(4)
  #3、删除元素
  s.remove(4)
  #4、取交集/并集
  >>> s1 = set([1, 2, 3])
  >>> s2 = set([2, 3, 4])
  >>> s1 & s2
  {2, 3}
  >>> s1 | s2
  {1, 2, 3, 4}
  ```
  
  
  
  
  * 变量：Pyhton属于动态语言，变量本身类型不固定
  * 常量：不能变的变量，Python中通常用全部大写表示常量，如`PI`,但常量实际还是变量，你还是可以去改变它的值。只是习惯这样，命名常量。

### <font color=Orange>字符串和编码</font>

* ASCII编码：使用一个字节，只包含127个字符

* Unicode编码：一般用2个字节编码字符，生僻字符使用4个字节编码，包含所有字符。但是冗余度高。

* UTF-8编码：UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，既能满足所有字符解析，又能避免冗余。

* 计算机使用字符编码的方式：内存中统一使用Unicode编码，保存到硬盘或网络传输时一般使用UTF-8编码。

* Python使用字符编码的方式：Python3的字符串使用Unicode编码，支持多语言。下面展示如何编码和解码，以及和`bytes`类型的互相转换

  ```python
  >>> ord('中')   # 解码
  20013
  >>> chr(25991)  # 编码
  '文'
  
  x = b'abc'										# 转bytes类型，仅支持ASCII编码
  x = '中文'.encode('utf-8')						# 转bytes，支持不能编码
  >>> x = b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8') # bytes转str
  '中文'
  ```
  
* 通常.py文件最开始会包含下面两行，第一行告诉Linux/OS X系统，这是一个Python3可执行程序（windows系统忽略），第二行告诉Python解释器该文件的编码方式（python解释器默认使用utf-8编码读取.py文件）。
  
  ```python
  #!/usr/bin/env python3
  # -*- coding: utf-8 -*-
  ```
  
* 格式化：Python使用`%`实现格式化输出：

  ```python
  # 整数格式化输出
  >>> print('%d-%d' % (3,3))
  3-3
  >>> print('-%4d-%2d' % (3,3333))
  -   3-3333
  >>> print('%04d-%02d' % (3,3333))
  0003-3333
  >>> print('%x' % 10)	# 十六进制整数格式
  a
  # 浮点数格式化输出
  >>> print('%f-%f' % (3.1415926,3.1415926))
  3.1415926-3.1415926
  >>> print('%.2f-%.2f' % (3.1415926,3.1))
  3.14-3.10
  >>> print('%1.2f-%4.2f' % (3.1415926,3.1))
  3.14-   3.10
  >>> print('%1.2f-%04.2f' % (3.1415926,3.1))
  3.14-0003.10
  # 字符串输出
  >>> print('%s' % 10)
  10
  >>> print('%d%%' % 3)
  3%
  ```
### <font color=Orange>条件判断</font>

* `if`语句判断从上向下匹配，当满足条件时执行对应的块内语句，后续的`elif`和`else`都不再判断和执行

  ```python
  age = 20
  if age>=18:
      print('adult')
  elif age >=6:
      print('teenager')
  else:
      print('kid')
  ```

### <font color=Orange>循环</font>

* Python有两种循环：`for ... in `和`while`,可以使用`continue`和`break`控制循环

  ```python
  for x in range(10):
      print(x)
  
  n = 10
  while n > 0:
      print(n)
  ```

***

## <font color=yellow>函数</font>

* 函数是最基本的一种代码抽象方式
### <font color=Orange>内置函数</font>

* Python内置了很多有用的函数，比如`abs`、`max`等，可以使用`help()`函数获取函数的帮助信息
* Python内置的常用函数还包括类型转换函数。比如`int()`、`float()`、`str()`、`bool()`等

### <font color=Orange>定义函数</font>

* 如下代码定义了函数`func`,没有`return`语句时函数默认返回`None`

  ```python
  def func(i):
      print(i)
  
  # 空函数
  def func_no():
      pass
  
  # 带参数类型检查的函数定义
  def my_abs(x):
      if not isinstance(x, (int, float)):
          raise TypeError('bad operand type')
      if x >= 0:
          return x
      else:
          return -x
      
  # 返回多个值的函数（其实是返回一个tuple）
  def func_multi(i):
      return i,i+1
  ```
### <font color=Orange>函数的参数</font>

#### <font color=Brown>位置参数</font>
> * 位置参数：必填参数，按顺序填写参数值
>
>   ```python
>   def f(x,y):
>       print(x,y)
>   ```
#### <font color=Brown>默认参数</font>
> * 默认参数：非必填参数，不填时使用默认值，可以按顺序填入参数值，也可以不按顺序（此时需填写参数名）。<font color=red>默认参数必须指向不可变对象</font>
>
>   ```python
>   # 多个默认参数的入参方式
>   def f(x,y=0,z='a'):
>       print(x,y,z)
>       return
>       
>   f(1,2,3)    # 按顺序入参
>   f(1,z='b')  # 不按顺序入参
>   ```
#### <font color=Brown>可变参数</font>
> * 可变参数：非必填参数，指传入的参数个数是可变的（0~任意个）。将会被组装成`tuple`传入函数
>
> * 当已经有一个`list`或`tuple`名叫vs，要将它作为可变参数传入函数，方式是在`vs`前加`*`
>
>   ```python
>   def f(*nums):
>       for n in nums:
>           print(n)
>       return
>   
>   # 可以传入任意多个参数
>   f(1)
>   f(1,2,3)
>   vs = [1,2,3]
>   f(*vs)	# 将list或tuple作为可变参数传入函数
>   
>   ```
#### <font color=Brown>关键字参数</font>
> * 关键字参数：非必填参数，指传入任意个带参数名的参数。将会组装成`dict`传入函数。
>
> * 当已经有一个`dict`名叫`dic`，要将他作为关键字参数传入函数，方式是在`dic`前加`**`
>
>   ```python
>   def f(**kw):
>       print(kw)
>       return
>   
>   # 可以传入任意多个带参数名的参数
>   >>> f(name='mike',age=27)
>   {'name':'mike','age':27}
>   
>   dic = {'name':'mike','age':27}	# 将dict作为关键字参数传入函数
>   >>> f(**dic)
>   {'name':'mike','age':27}
>   ```
#### <font color=Brown>命名关键字参数</font>
> * 命名关键字参数：必填参数，传入参数时，必须填写参数名。*后面或可变参数后面的参数就是命名关键字参数
>
>   ```python
>   def f(i,*,j,k):		# *后面的j、k都是命名关键字参数，入参时必须填写参数名
>       print(i,j,k)
>       return
>   
>   def f1(i,*v,j,k): 	# 可变参数v后面的j、k都是命名关键字参数，入参时必须填写参数名
>   ```

#### <font color=Brown>参数组合</font>

> * Pyhthon中定义函数，上面5种类型参数可以组合使用，但参数定义的顺序需遵循
>   1. 必选参数
>   2. 默认参数
>   3. 可变参数
>   4. 命名关键字参数
>   5. 关键字参数

### <font color=Orange>递归函数</font>

* 递归函数相较于循环，逻辑更清晰，但Python递归过深会导致栈溢出
* Python没有尾递归优化，因此任何递归函数都存在栈溢出的风险

***

## <font color=yellow>高级特性</font>

* Python代码真言：代码越少越好，越简单越好。基于此，我们来介绍Python的高级特性

### <font color=orange>切片(Slice)</font>

* 利用切片功能，可以非常方便的进行截取。

  ```python
  # 切片功能总结：
  #	1、左索引和右索引，索引值为0时，可忽略不写，索引值可以为负数，比如-1表示倒数第一个元素
  
  l = range(10)
  lt = l[:]		# 取所有元素
  lt = l[0:3] 	# 截取前3个元素，不包含l[3]
  lt = l[:3]	    # 等价于lt=l[0:3],第一个索引为0时，可省略
  lt = l[-2:]     # 等价于lt=l[-2:0],取末尾两个元素
  lt = l[::2]     # 所有元素，每2个取一个
  lt = l[-10::5]  # 末尾10个元素，每5个取一个
  ```

### <font color=orange>迭代</font>

* 我们将遍历元素称为迭代，python使用`for ... in`来进行迭代，可以用`for ... in`进行迭代的对象称作<font color=Red>可迭代对象</font>，使用`collection`模块的`iterable`可判断是否是可迭代对象。

* python内置的`enumerate`函数可以把一个`list`变成 索引-元素 对

  ```python
  # 可迭代对象判断 
  from collection import Iterable
  >>> isinstance('abc',Iterable)
  True
  
  # dict的迭代
  dic = {0:'0',1:'1'}
  # 默认迭代key
  for key in dic:		
      print(key)
  # 迭代value
  for value in dic.values(): 
      print(value)   
  # 迭代key和value    
  for k,v in dic.items():	   
      print(k,v)			  
      
  # enumerate函数可以把一个list变成 索引-元素 对
  for i,v in enumerate(['a','b','c']):
      print(i,v)
  ```

### <font color=orange>列表生成式</font>

* Python内置的列表生成式，可以非常便捷的生成`list`，但它不是惰性的，因此会立即申请内存，受到内存容量限制。

  ```python
  # 列表生成式：
  #	1、生成的元素，比如x*x写在前面，后面跟for循环就可以把list创建出来
  #	2、for循环后面可以跟if判断，进行筛选
  # 	3、可以使用两层循环，生成全排列,三层以上的很少用st
  >>> [x * x for x in range(1, 6) if x % 2 == 0]
  [4, 16]
  >>> [m+n for m in 'ABC' for n in 'XYZ']
  ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
  ```

### <font color=orange>生成器</font>

* 使用列表生成式可以直接创建一个列表，但受内存限制，列表容量肯定是有限的。所以，如果列表元素可以按照某种算法推出来，我们一边循环一边生成元素，这样就可以节省大量内存。在Python中，这种一边循环一边计算的机制，称为生成器：generator

* 生成器和列表生成式的区别有：

  1. 生成器是惰性的，列表生成式不是惰性的
  2. 把列表生成式的`[]`换成`()`就创建了一个生成器，也可以通过函数实现逻辑复杂的生成器
  3. 列表生成式返回`list`，生成器返回`generator`

  ```python
  #################################### 简单的generator ########################################
  g = (x*x for x in range(1, 6))
  # 访问generator的下一个元素
  next(g)	   
  # generator是可迭代对象(每次迭代，其实也是调用next()函数)
  for i in g:
      print(i)
  
  ################################ 通过函数实现复杂的generator ##################################
  # 	1、如果一个函数定义中包含yield，那么这个函数就不再是一个普通函数，而是一个generator
  # 	2、变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句#	   处继续执行
  # 这里用斐波那契数列举例（当前元素总是前两个元素的和），定义一个generator
  def fib(max):
      n,a,b = 0,0,1
      while n < max:
          yield b
          a,b = b,a+b		# 这里相当于tuple = (b, a + b)、a = tuple[0]、b = tuple[1]
          n = n+1
      return 'done'		# 包含在StopIteration的value中返回
  # 
  g = fib(4)
  # 若想获取generator的return语句的返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中
  >>> while True:
  ...     try:
  ...         x = next(g)
  ...         print('g:', x)
  ...     except StopIteration as e:
  ...         print('Generator return value:', e.value)
  ...         break
  ...
  g: 1
  g: 1
  g: 2
  g: 3
  Generator return value: done
  ```

### <font color=orange>迭代器</font>
* 可以直接作用于`for`循环的对象统称为可迭代对象：Iterable，而generator不但可以作用于`for`循环，还可以被`next()`函数不断调用并返回下一个值，直到抛出`StopIteration`错误。可以被`next()`函数不断调用并返回下一个值的对象成为迭代器：Iterator。它表示一个惰性计算的序列。

* 迭代器：Iterator和`list`等的区别

  1. `list`是Iterable对象，但不是`Iterator`，可以使用`iter()`函数将`list`、`dict`、`str`等变成`Iterator`
  2. `Iterator`对象是惰性的，只有在调用`next()`函数时计算返回下一个值，它甚至可以表示一个无限大的数据流
  3. 可以使用`isinstance()`判断一个对象是否是`Iterable`对象，或`Iterator`对象。

  ```python
  # 判断可迭代对象Iterable
  >>> from collections import Iterable
  >>> isinstance([], Iterable)
  True
  # 判断迭代器Iterator
  >>> from collections import Iterator
  >>> isinstance((x for x in range(10)), Iterator)
  True
  # 转换list为Iterator
  >>> isinstance(iter([]), Iterator)
  True
  >>> isinstance(iter('abc'), Iterator)
  True
  ```

  

***
## <font color=yellow>函数方式编程</font>
* 函数式编程是一种抽象程度很高的编程范式。
* 纯粹的函数式编程语言不支持变量，因此任意一个函数，只要输入确定，输出就是确定的。这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，造成同样的输入可能得到不同的输出，这种函数是有副作用的。
* python允许使用变量，不是纯粹的函数式编程语言，它对函数式编程提供部分支持：允许把函数本身作为参数传入另一个函数。
### <font color=Orange>高阶函数</font>
#### <font color=Brown>高阶函数简述</font>
> * 把函数作为入参，这种函数称之为高阶函数
> * python中，变量可以指向函数，例如下面的代码，变量f指向函数abs()，通过f就可以调用函数abs()：
> 	```python
> 	f = abs
> 	x = f(-10)
> 	```
> * python中，函数名其实就是指向函数的变量，例如下面的代码，把函数名abs看成变量，对它赋值后，就无法执行abs函数了：
> 	```python
> 	abs = 10
> 	abs(-10)
> 
> 	Traceback (most recent call last):
>   	File "<stdin>", line 1, in <module>
> 	TypeError: 'int' object is not callable
> 	```
> * 下面代码编写了一个简单的高阶函数，接收函数作为入参
> 	```python
> 	def add(x,y,f):
> 		return f(x)+f(y)
> 	```
>
#### <font color=Brown>map/reduce函数</font>
> * Python内建了高阶函数map()和reduce()
> * map()函数接收两个参数（函数参数f和Iterable参数x），map将f作用于x的每一个参数，并把结果作为新的Iterator返回。
> 	```python
> 	y = map(abs,[0,1,-2,3,-4])
> 	```
> * reduce()函数接收两个参数（函数参数f和Iterable参数x），f必须接收两个参数，reduce将f函数的结果继续和x的下一个元素做f计算，直到最后一个元素。
> 	```python
> 	reduce(f,[x1,x2,x3,x4])  等价于  f(f(f(x1,x2),x3),x4)
> 	```
#### <font color=Brown>filter函数</font>
> * Python内建高阶函数filter()用于过滤序列，filter()函数接收两个参数（函数参数f和序列x），filter将f作用于x的每一个元素，结果是True的元素保留，结果是false的元素丢弃。并把结果作为新的Iterator返回。
> 	```python
> 	def f(n):
> 		return n>0
> 		
> 	y = filter(f,[0,-1,2,-3])
> 	```
#### <font color=Brown>sorted函数</font>
> * Python内置sorted()函数可以对list进行排序
> 	```python
> 	y = sorted([3,-1,2])
> 	```
> * sorted()函数也是一个高阶函数，它还可以接收一个key函数来实现自定义排序,此外，还可以接收bool类型参数reverse实现反向排序。
> 	```python
> 	y = sorted([3,-1,2],key=abs,reverse=True)
> 	```
### <font color=Orange>返回函数</font>
* 另一种高阶函数：将函数作为结果值返回。如下当我们调用lazy_sum()时，返回的并不是求和结果，而是求和函数sum，并且相关参数和变量都保存在返回的函数中，这种程序结构称作“闭包” 
	```python
	def lazy_sum(*args):
    	def sum():
			ax = 0
			for n in args:
				ax = ax + n
			return ax
		return sum
	```
* 闭包陷阱：在返回函数f之前，f引用的相关变量发生改变。闭包中的参数和变量也会改变
	```python
	# 闭包陷阱
	def count():
		fs = []
		for i in [1, 2]:
			def f():
				return i*i
			fs.append(f)
		return fs
		
	f1,f2 = count()
	print(f1())	#打印结果为4
	print(f2()) #打印结果为4
	```
### <font color=Orange>匿名函数</font>
* 关键字lambda表示匿名函数，匿名函数有个限制，就是只能有一个表达式
	```python
	lambda x,y:x+y  
	#等价于
	def f(x,y):
		return x+y
	```
### <font color=Orange>装饰器</font>

* 本质上"装饰器"(Decorator)就是一个高阶函数：参数为函数，返回结果为函数。下面定义了一个装饰器log，并借助@语法使用装饰器

  ```python
  import functools
  
  def log(func):
      @functools.wraps(func)	#返回的函数的函数名还是func
      def wrapper(*args,**kw):
          print('call %s():' % func.__name__)
          return func(*args,**kw)
      return wrapper
  
  @log	#调用now(),相当于执行log(now),now指向wrapper函数
  def now():
      print('Now')
  ```

* 装饰器还可以接收被修饰函数以外的参数，下面代码编写的新的log装饰器如下：

  ```python
  import functools
  
  def log(text):
      def decorator(func):
          @functools.wraps(func)	#返回的函数的函数名还是func
          def wrapper(*args,**kw):
              print('%s %s():' % (text,func.__name__))
              return func(*args,**kw)
          return wrapper
      return decorator
  
  @log('excute')	#调用now(),相当于执行log('excute')(now)，now指向wrapper
  def now():
      print('Now')
  ```

### <font color=Orange>偏函数</font>
* `functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。本质上，它也是一个高阶函数：参数是函数`f`和若干默认参数，返回值是函数。下面的代码展示了如何获取偏函数int2
	
	```python
	import functools
	
	int2 = functools.partial(int,base=2)	#int2函数等价于base默认值为2的int函数
	```
***
## <font color=yellow>模块</font>

* 在Python中，一个`.py`文件据称之为一个模块（Module）

* 相同名字的函数和变量可以分别存在于不同的模块中，但也要注意，尽量不要与内置函数名字冲突

* 模块名不要和系统模块名冲突

* Python引入了按目录来组织模块的方法，称为包（Package），解决模块名称相同的不同模块如何引用的问题。

  ```python
  # 这里引入了两个Package（mycompany和mycompany.web），每一个包目录下都会有一个__init__.py文件（否则Python 就把这个目录当成普通目录），__init__.py可以是空文件，也可以有Python代码，因为__init__.py本身就是一个模块，它的模块名就是mycompany或mycompany.web
  mycompany
   ├─ web
   │  ├─ __init__.py
   │  ├─ utils.py
   │  └─ www.py
   ├─ __init__.py
   ├─ abc.py
   └─ utils.py
  ```
### <font color=Orange>使用模块</font>

* 如下是Python模块的标准文件模板

  1. 开头两行是标准注释
  2. 第四行是模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释
  3. 第六行使用`__author__`变量把作者写进去

  ```python
  #!/usr/bin/env python3
  # -*- coding: utf-8 -*-
  
  ' a test module '
  
  __author__ = 'Mike Mao'
  ```
  
* 导入模块使用`import`

* 关于模块中函数和变量的作用域：

  1. 正常的函数和变量名是公开的，可以直接被引用
  2. 类似`__xxx__`这样的变量是特殊变量，可以直接被引用，但是有特殊用途
  3. 类似`_xxx`或`__xxx`这样的变量就是非公开的，不应该被直接引用（但你一定要直接引用，Python也不会阻止）
### <font color=Orange>安装第三方模块</font>

* 使用pip来安装第三方模块

  ```python
  # 使用默认的pip源安装xxx库
  pip install xxx
  # 通过git安装xxx库
  pip install git+https://github.com/mhallsmoore/qstrader.git	
  # -r参数通过requirements.txt安装一系列库
  pip install -r https://raw.githubusercontent.com/mhallsmoore/qstrader/master/requirements.txt
  # -i参数，临时更换pip源安装xxx库
  pip install xxx -i http://pypi.douban.com/simple/
  ```

***
## <font color=yellow>面向对象编程</font>
* 数据封装、继承和多态是OOP的三大特点
### <font color=Orange>类和实例</font>
* class是抽象的模板，instance是具体的对象。
* 可以自由地给一个instance变量绑定属性，定义`__init__`方法把一些必要属性放进class。
* class成员函数第一个参数永远是self
### <font color=Orange>访问限制</font>

* python中，class内部属性的名称若类似`__x`形式，就变成了private属性。若变量名类似`__x__`,那么这个变量是特殊变量。
* 遵循数据封装规则，请不要在外部直接访问class内部属性，而是通过class函数进行访问。

### <font color=Orange>继承和多态</font>
* 继承：子类通过继承父类/超类/基类，获得父类的全部功能。
* 多态：子类重写父类的方法，执行时总是会执行子类的方法。这样，程序在执行时，根据子类的类型，动态选择执行各自子类的方法。
### <font color=Orange>获取对象信息</font>
* type()函数返回instance的类型,基本数据类型可以用int、str等表示，另外types模块提供函数等更多类型
	```python
	>>> type(123) == int
	True
	>>> type(abs) == types.Builtin
	```
* isinstance()函数判断一个对象是否是该类型本身，或者位于该类型的父继承链上，还可以判断是否是某些类型中的一种
	```python
	>>> isinstance(123,object)
	True
	>>> isinstance(123,(int,str))
	True
	```
* dir()函数可以获取一个instance的所有属性和方法。它返回一个`list<str>`
* 配合hasattr()、getattr()、setattr()函数可以对未知类型对象进行操作
### <font color=Orange>实例属性和类属性</font>
* 类属性类似于C#中的静态成员变量，类属性属于类所有，所有instance共享这个类属性
* 不要对实例属性和类属性使用相同的名字，否则将产生难以发现的错误
***
## <font color=yellow>面向对象高级编程</font>
### <font color=Orange>使用__slots</font>
* 类属性`__slots__`用来限制该class的instance能添加的属性。它对子类不起作用，除非在子类中也定义`__slots__`,这样子类允许定义的属性就是自身的`__slots__`加上父类的`__slots__`

  ```python
  from types import MethodType
  
  class Student(object):
      __slots__ = ('name','get_name','age','get_age') 	#允许添加name和get_name属性
  
  def get_name(x):
      return x.name
      
  def get_age(x):
      return x.age    
  
  Student.age = 27					#添加类属性age
  Student.get_age = get_age			 #添加类属性get_age
  
  s = Student()
  s.name = 'mike'						#添加实例属性name
  s.get_name = MethodType(get_name,s)	  #添加实例方法get_name
  ```
### <font color=Orange>使用@property</font>
* Python内置@property装饰器,负责把一个方法变成属性调用。作用类似C#的get和set，既保证数据封装，又使属性的操作更加简便
	```python
	class Student(object):
	    
	    @property				#get
	    def score(self):
	        return self._score
	
	    @score.setter			#set
	    def score(self, value):
	        if not isinstance(value, int):
	            raise ValueError('score must be an integer!')
	        if value < 0 or value > 100:
	            raise ValueError('score must between 0 ~ 100!')
	        self._score = value
	```
### <font color=Orange>多重继承</font>
* 一个子类继承多个父类，同时获得多个父类的所有功能，这就是多重继承的好处。MixIn程序设计方式就是选择组合不同的类的功能，快速构造出所需的子类，而不需要复杂庞大的继承链。
	```python
	class Bird(Flyable,Runnable):
		pass
	```
### <font color=Orange>定制类</font>
* Python中有许多形如`__x__`的函数帮助我们定制类，最常用的包括`__str__()`、`__iter__()`、`__getitem__()`、`__getattr__()`、`__call__()`
	1. `__str__()`返回用户看到的字符串，print()函数调用的就是`__str__()`函数
	2. `__iter__()`返回一个迭代对象，使类能够被用于`for...in`循环
	3. `__getitem__()`、`__setitem__()`和`__delitem__()`方法使类可以表现得和Python自带的list、tuple、dict没什么区别
	4. `__getattr__()`方法控制调：当调用类不存在的属性时，会发生什么。
	5. `__call__()`方法使可以直接调用实例本身`instanceX()`，而不是`instanceX.method()`来调用
### <font color=Orange>使用枚举类</font>
* Python的Enum类可用来定义枚举类型
	```python
	from enum import Enum
	
	Season = Enum('Season',('Spring','Summer','Autumn','Winter'))	#获取Month类型的枚举类
	```
* 另外一种方式产生枚举类型，可精确控制元素的值
	```python
	from enum import Enum,unique
	
	@unique		#帮助检查有无重复值
	class Season(Enum):
	    Spring = 100
	    Summer = 101
	    Autumn = 202
	    Winter = 303
	```
### <font color=Orange>使用元类</font>
* 动态语言和静态语言的最大不同就是，函数和类的定义不是编译时定义的，而是运行时动态创建的。

* 下面的例子展示了type()函数如何在运行时动态的创建Hello类型
	```python
	def fn(self):
	    print('Hello')
	
	Hello = type('Hello',(object,),dict(hello=fn))	#运行时动态创建Hello类，其实Python解释器遇到class定义时也是通过调用type()函数动态创建出class的
	```
	
* 我们还可以通过metaclass（元类）控制类的创建行为。metaclass允许你创建或修改class，你可以把class看成是metaclass创建出来的instance。
	
	这里说明几点：1、`__new__`是在`__init__`之前被调用的特殊方法，在`__new__`中可以控制class的生成，这个一般可以通过在类中定义静态数据成员和成员函数的方式实现，因此很少用；2、`__init__`只是用于初始化instance
	
	```python
	#step1：定义元类ListMetaclass（metaclass是类的模板，所以必须从type类型派生）
	class ListMetaclass(type):
	    def __new__(cls,name,bases,attrs):
	        attrs['add'] = lambda self,value:self.append(value)
	        return type.__new__(cls,name,bases,attrs)
	
	#step2：使用元类ListMetaclass创建Mylist类
	#当传入关键字参数metaclass时，它指示Python解释器在创建MyList类时要通过元类ListMetaclass.__new__()来创建，并依次传入参数：1、要创建的类型；2、类型名；3、基类们；
	class MyList(list,metaclass=ListMetaclass):		
	    pass
	
	```
***
## <font color=yellow>错误、调试和测试（待补全）</font>
## <font color=yellow>IO编程（待补全）</font>
## <font color=yellow>进程和线程（待补全）</font>
## <font color=yellow>正则表达式（待补全）</font>
## <font color=yellow>常用内建模块（待补全）</font>
## <font color=yellow>常用第三方模块（待补全）</font>
## <font color=yellow>python虚拟环境（待补全）</font>
## <font color=yellow>图形界面（待补全）</font>
## <font color=yellow>网络编程（待补全）</font>
## <font color=yellow>电子邮件（待补全）</font>
## <font color=yellow>访问数据库（待补全）</font>
## <font color=yellow>web开发（待补全）</font>
## <font color=yellow>异步IO（待补全）</font>
## <font color=yellow>实战（待补全）</font>
## <font color=yellow>FAQ（待补全）</font>
## <font color=yellow>期末总结（待补全）</font>