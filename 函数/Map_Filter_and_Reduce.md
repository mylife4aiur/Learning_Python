# Map, Filter 和 Reduce 函数
这篇文章会介绍 Python 3 中的 Map, Filter 和 Reduce 这三个函数，它们能简化编程中的某些函数方法。文章中的代码均是在 python 3 环境下运行，python 2 环境下这些函数会有一些不同。jupyter notebook 形式的文章放在了我的 [github](https://github.com/mylife4aiur/Learning_Python/blob/master/函数/Map_Filter_and_Reduce.ipynb) 上。
## 1. Map

```python
map(function, iterable, ...)
```
函数 ```map``` 返回一个迭代器，它将函数 ```function``` 应用在 ```iterable``` 这个可迭代参数的每一个元素上，逐个返回（yeild）结果（详见 1.1 节）。如果传入多个 ```iterable``` 参数，则要求函数 ```function``` 能接受同等数量的参数，这种情况下，```function``` 会被并行地应用于所有可迭代对象的每一个元素（详见 1.2 节）。对于多个可迭代变量，```map``` 函数产生的迭代器会在穷尽最短的可迭代对象后停止。对于那些函数输入已经被整理成参数元组（argument tuple）的情况，请参考 ```itertools.starmap()``` 函数。
### 1.1. 单一 ```iterable``` 参数
考虑一个最简单的任务，为列表 ```items``` 中的每一个元素求它的平方值，使用for循环的代码如下
```python
items = [1, 2, 3, 4, 5]
squared = []
for i in items:
squared.append(i**2)
print(squared)
```
```python
[1, 4, 9, 16, 25]
```
同样的任务我们可以使用 ```map``` 来计算
```python
items = [1, 2, 3, 4, 5]
squared = map(lambda x: x**2, items)
print(squared)
```
```python
<map object at 0x10b879898>
```
这里使用了匿名函数来当传入的 ```function``` 参数。可以看到最后的结果 ```square``` 并不是一个列表（list），而是一个 ```map``` 对象，也就是一个迭代器，每调用一次这个迭代器的 .\_\_next\_\_() 方法都会按次序生成对应的结果。
```python
items = [1, 2, 3, 4, 5]
squared = map(lambda x: x**2, items)

a=squared.__next__()
b=squared.__next__()
c=squared.__next__()
print(a,b,c)
```
```python
1 4 9
```
可以看到虽然变量 ```a b c``` 都是调用的同样的函数，但是结果却不同，这有点类似c语言的指针结构，第一次调用.\_\_next\_\_() 方法返回 1 对应的结果后，下一次调用.\_\_next\_\_() 方法就会返回 2 对应的结果，以此类推。
当然我们有更简单的方法来访问这个迭代器中的内容，可以用 ```for``` 循环依次访问或者直接将其转成列表。
```python
items = [1, 2, 3, 4, 5]

print('for 循环:')
squared = map(lambda x: x**2, items)
result=[]
for i in squared:
result.append(i)
print(result)

print('转化成列表')
squared = map(lambda x: x**2, items)
print(list(squared))
```
```python
for 循环:
[1, 4, 9, 16, 25]
转化成列表
[1, 4, 9, 16, 25]
```
如果我们稍微改写一下上面的代码，注释掉倒数第二行，结果会有一点小问题
```python
items = [1, 2, 3, 4, 5]

print('for 循环:')
squared = map(lambda x: x**2, items)
result=[]
for i in squared:
result.append(i)
print(result)

print('转化成列表')
# squared = map(lambda x: x**2, items)
print(list(squared))
```
```python
for 循环:
[1, 4, 9, 16, 25]
转化成列表
[]
```
可以看到，第二次没有输出对应的数值。这是因为实际上对 ```suqared``` 的 ```for``` 循环也在内在地调用 .\_\_next\_\_() 方法，直到穷尽所有的元素。因此当在最后一行将 ```suqared``` 列表化的时候会得到一个空列表
```python
squared.__next__()
```
```python
[0;31m---------------------------------------------------------------------------[0m
[0;31mStopIteration[0m                             Traceback (most recent call last)
[0;32m<ipython-input-6-1371fd72abe3>[0m in [0;36m<module>[0;34m()[0m
[0;32m----> 1[0;31m [0msquared[0m[0;34m.[0m[0m__next__[0m[0;34m([0m[0;34m)[0m[0;34m[0m[0m
[0m
[0;31mStopIteration[0m:
```
再次调用 ```suqared``` 的 .\_\_next\_\_() 方法时，会报迭代停止的错误。
### 1.2. 多个 ```iterable``` 参数
当为 ```map``` 函数传入多个 ```iterable``` 参数的时候，```map``` 会并行地处理这些参数。这里引用一张 [Pythoner](http://www.pythoner.com/46.html) 的图片。![](multi_iterable.png)

图片中，```map``` 函数接受了 ```M``` 个 ```seq``` 的输入和一个 ```func```  函数的输入，在执行的每一步中，map 会分别将 ```M``` 个 ```seq``` 相同位置的元素传给 ```func``` 计算结果。也就是说，第一个结果会是 ```func(seq1[0],seq1[0],...,seqM[0])``` ，第二个结果会是 ```func(seq1[1],seq1[1],...,seqM[1])```。如下面这个例子，计算两个和三个列表对应元素的乘积。
```python
print(list(map(lambda x , y : x * y, [1,2,3], [2,4,6])))
print(list(map(lambda x , y , z : x * y *z, [1,2,3], [2,4,6],[1,10,100])))
```
```python
[2, 8, 18]
[2, 80, 1800]
```
对于传入的可迭代对象长度不一致的情况，```map``` 会生成和之中最短序列等长的迭代器
```python
print(list(map(lambda x , y : x * y, [1,2,3], [2,4,6,8,10])))
```
```python
[2, 8, 18]
```
### 1.3 使用 ```map``` 进行格式转换
如果将 ```map``` 中的 ```function``` 参数换成 python 内置的某些函数，还可以完成格式转换。
```python
print(list(map(int, [1.9,2.2,3.1])))
print(list(map(str, [1,2,3,4])))
```
```python
[1, 2, 3]
['1', '2', '3', '4']
```
### 1.4. 总结
简而言之 ```map``` 将一个函数应用在一个或多个输入列表中的所有元素上
## 2. Reduce
```reduce``` 和 ```map``` 可以直接调用不同，它被放在了 ```functools``` 中：

```python
functools.reduce(function, iterable[, initializer])
```
```reduce``` 累积地将函数 ```function``` 从左至右地应用到序列 ```iterable``` 中的每一个元素，因此它将一个序列缩减到单一值。比如，```reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])``` 将会计算 ```((((1+2)+3)+4)+5)```。左侧的参数 ```x``` 是前一步的累加值，右侧的参数 ```y``` 是从序列 ```iterable``` 中获取到的更新值。如果提供了可选参数 ```initializer```，将会将其放在所有 ```iterable``` 序列元素之前，作为函数  ```function``` 的初始值。如果没有提供 ```initializer``` 参数，且 ```iterable``` 中只有一个元素，那么 ```map``` 函数将会返回这个元素。
```python
from functools import reduce
```
```python
print(reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]))
# 提供 initializer 参数
print(reduce(lambda x, y: x+y, [1, 2, 3, 4, 5],6))
# iterable 中只有一个参数
print(reduce(lambda x, y: x+y, [1]))
```
```python
15
21
1
```
## 3. Filter
```python
filter(function, iterable)
```
```function``` 函数会逐个判断 ```iterable``` 中的每个元素，返回真值的元素被留下，以此来构建一个迭代器。如果 ```function``` 是 ```None``` 的话，会默认使用恒等函数（identity function），这种情况下 ```iterable``` 中所有假值被移除。

当 ```function``` 不是 ```None``` 的情况下，```filter(function, iterable)``` 等价于生成器表达式 ```(item for item in iterable if function(item))```；当 ```function``` 是 ```None``` 的情况下，等价于 ```(item for item in iterable if item)```
```python
l1 = [ 1, 2, 3, 42, 67, 16 ]
print(list(filter(lambda x:x>10, l1)))

# function 为 None
l1 = [ 1, 2, 3, 42, 67, 16 ]
print(list(filter(None, l1)))
l1 = [ False, 2, 0, 42, None, 16 ]
print(list(filter(None, l1)))
```
```python
[42, 67, 16]
[1, 2, 3, 42, 67, 16]
[2, 42, 16]
```
## 4. 运行时间对比
```python
from time import time
```
### 4.1. Map
```python
# 计算两个列表对应元素相加
N=10001
l1=list(range(N))
l2=list(range(N,0,-1))

'''=================== Map ======================'''
tic=time()
for i in range(100):
result_1=list(map(lambda x,y:x+y , l1,l2))
toc=time()
print('map 函数运行时间：{0:6f}'.format(toc-tic))

'''=================== For ======================'''
tic=time()
for i in range(100):
result_2=[]
for i in range(len(l1)):
result_2=l1[i]+l2[i]
toc=time()
print('for 循环运行时间：{0:6f}'.format(toc-tic))

'''============= List Comprehension ============'''
tic=time()
for i in range(100):
result_3=[l1[i]+l2[i] for i in range(len(l1))]
toc=time()
print('列表推倒运行时间：{0:6f}'.format(toc-tic))
```
```python
map 函数运行时间：0.103474
for 循环运行时间：0.196260
列表推倒运行时间：0.140500
```
可以看到 ```map``` 的速度是最快的，生成器表达式要快于 ```for``` 循环。
### 4.2. Reduce
```python
# 计算前N项和
N=1001
l=list(range(N))

'''=================== Reduce ====================='''
tic=time()
for i in range(10000):
result_1=0
result_1=reduce(lambda x, y: x + y, l)
toc=time()
print('reduce 函数运行时间：{0:6f}'.format(toc-tic))
print('reduce 结果为：{0}'.format(result_1))

'''=================== For ========================'''
tic=time()
for i in range(10000):
result_2=0
for j in range(len(l)):
result_2+=l[j]
toc=time()
print('for 循环运行时间：{0:6f}'.format(toc-tic))
print('for 结果为：{0}'.format(result_2))

'''=================== Sum ========================'''
tic=time()
for i in range(10000):
result_3=0
result_3=sum(l)
toc=time()
print('sum 运行时间：{0:6f}'.format(toc-tic))
print('sum 结果为：{0}'.format(result_3))
```
```python
reduce 函数运行时间：1.092224
reduce 结果为：500500
for 循环运行时间：1.484981
for 结果为：500500
sum 运行时间：0.077001
sum 结果为：500500
```
可以看出 ```reduce``` 的速度比 ```for``` 循环要快，但是还是远远慢于 ```sum``` 函数，估计 ```sum``` 函数是经过特殊优化的。
```python
# 判断大小
N=1000
l=list(range(N))

'''===================== Filter ======================'''
tic=time()
for i in range(100000):
result_1=filter(lambda x: x > N**2/2, l)
toc=time()
print('filter 函数运行时间：{0:6f}'.format(toc-tic))
print(result_1)

'''============ Generator expression =============='''
tic=time()
for i in range(100000):
result_2=(x for x in l if  x > N**2/2)
toc=time()
print('生成器表达式 函数运行时间：{0:6f}'.format(toc-tic))
print(result_2)
```
```python
filter 函数运行时间：0.037482
<filter object at 0x10b8825c0>
生成器表达式 函数运行时间：0.044511
<generator object <genexpr> at 0x10b92cdb0>
```
可以看出，函数 ```filter``` 基本上是等价于生成器表达式的，两个方法都会返回一个可迭代对象。
```python
# 判断大小
N=1000
l=list(range(N))
'''===================== Filter ======================'''
tic=time()
for i in range(1000):
result_1=list(filter(lambda x: x < N**2/2, l))
toc=time()
print('filter 函数运行时间：{0:6f}'.format(toc-tic))
print(result_1[:5])

'''============ Generator expression =============='''
tic=time()
for i in range(1000):
result_2=list((x for x in l if  x < N**2/2))
toc=time()
print('生成器表达式 函数运行时间：{0:6f}'.format(toc-tic))
print(result_2[:5])

'''============= List Comprehension =============='''
tic=time()
for i in range(1000):
result_3=[x for x in l if  x < N**2/2]
toc=time()
print('列表推倒 函数运行时间：{0:6f}'.format(toc-tic))
print(result_3[:5])

'''===================== For ======================'''
result_4=[]
tic=time()
for i in range(1000):
for j in l:
if  j < N**2/2:
result_4.append(j)
toc=time()
print('for 函数运行时间：{0:6f}'.format(toc-tic))
print(result_4[:5])
```
```python
filter 函数运行时间：0.419213
[0, 1, 2, 3, 4]
生成器表达式 函数运行时间：0.377272
[0, 1, 2, 3, 4]
列表推倒 函数运行时间：0.337962
[0, 1, 2, 3, 4]
for 函数运行时间：0.434988
[0, 1, 2, 3, 4]
```
这里可以看到几个有意思的现象：
* 列表推倒的速度比 filter 要快。
* 从运行时间上来看，list(生成器表达式) 并不等价于 list(filter)，而是等价于列表推倒的。这里具体的原因还不清楚，可能是 python 对其专门做了优化，也有可能是因为 filter 中用到了匿名函数。
Fluent Python 一书的 [github](https://github.com/fluentpython/example-code/blob/master/02-array-seq/listcomp_speed.py) 上提供了类似的如下的比较代码。
```python
import timeit

TIMES = 10000

SETUP = """
symbols = '$¢£¥€¤'
def non_ascii(c):
return c > 127
"""

def clock(label, cmd):
# 默认重复三次
res = timeit.repeat(cmd, setup=SETUP, number=TIMES)
print(label, *('{:.3f}'.format(x) for x in res))

clock('listcomp        :', '[ord(s) for s in symbols if ord(s) > 127]')
clock('listcomp + func :', '[ord(s) for s in symbols if non_ascii(ord(s))]')
clock('filter + lambda :', 'list(filter(lambda c: c > 127, map(ord, symbols)))')
clock('filter + func   :', 'list(filter(non_ascii, map(ord, symbols)))')
```
```python
listcomp        : 0.016 0.014 0.014
listcomp + func : 0.019 0.018 0.020
filter + lambda : 0.016 0.016 0.016
filter + func   : 0.016 0.015 0.015
```
在这一字符转 Unicode 码位的任务下，filter 和列表推倒各有优劣。
所以总体来说，当想用 ```for``` 循环实现自己算法的时候，可以先考虑一下能不能用这三个函数实现，这样大多数情况下可以提升算法的速度。
## 5. 函数 ```function``` 的自定义
我们不仅可以使用匿名函数定义 ```function```，还可以自己写一个函数当作参数传入。
```python
#将小写转成大写
def l_to_u (sA
return s.upper()
print (list(map(l_to_u,'asdfd')))
```
```python
['A', 'S', 'D', 'F', 'D']
```
## 6. 参考资料
https://docs.python.org/3/library/functions.html#map

http://book.pythontips.com/en/latest/map_filter.html

http://www.pythoner.com/46.html
```python
```
