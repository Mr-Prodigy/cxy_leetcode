C++中标准容器中不仅提供了常用的vector, set, map等容器，还提供了有序容器，如ordered_map, ordered_set等。

同样， Python 也提供了有序容器：SortedList， SortedKeyList， SortedDict， SortedSet。

这些容器不常用，我总是学了就忘，特写这篇文档，系统地再复习下。

1、 ***\*SortedList\****

SortedList维护一个升序的列表。

***\*添加元素:\****

add()                                   添加一个元素，按照顺序排列进去                

update()                              添加一列元素，按照顺序排列进去  

***\*删除元素：\****

remove(value)                      删除值为value的元素，要是列表中没有这个值，会报错

discard（value）                  删除值为value的元素，如果列表中没有这个值，不会报错（内置列表没有这个方法）

pop(index)                           删除索引为index的元素

del list_name[index]            删除索引为index的元素

clear()                                  清空整个列表

***\*查找元素：\****

index(value)                         查找列表中value第一次出现的索引位置

count(value)                         返回value在列表中的个数

bisect_left(value)                  若向列表中插入value时，value所处的位置（如果有相同的，则返回最左边的位置）

bisect_right(value)                若向列表中插入value时，value所处的位置（如果有相同的，则返回最右边的位置）

同时，SortedList还支持几乎内置列表的所有功能，如切片，操作符 in， + ， * 等，功能十分强大

SortedList尚未支持的功能：append(), extend()，insert(), reverse() 和直接赋值。 原因很好理解：SortedList是一个有序的列表，这些方法会打破列表的有序性。

2、***\*SortedKeyList\****

SortedKeyList是SortedList的一个子类，支持SortedList的大部分方法。但支持自定义排序方法

-Python 代码

my_list = SortedKeyList(key = lambda x: -x)                      

额外操作:

bisect_left变为了bisect_key_left, bisect_right变为了bisect_key_right。

SortedDict

SortedDict维护一个升序字典，可自定义比较函数。

添加元素: 

update()                                     添加若干键值

my_dict[key] = value                  直接赋值

setdefault()

删除元素：

del my_dict[key]                         删除键

pop(key)                                    删除键

popitem(index = -1)                  删除索引为indx的键，默认为-1， 并返回(键，值)

clear()                                        清空字典

查找元素：

my_dict[key]                              按键求值

peekitem(index = -1)                返回元组，求索引为index的(键， 值)，默认为-1

get(key)                                     求键为key的值，可设置默认返回值

index(key)                                  求key对应的索引

***\*SortedSet\****

SortedSet维护一个升序集合，可自定义比较函数。

添加元素: 

add(),                                   添加一个元素

update()                               添加一些元素

删除元素：

remove(value)                       删除值为value的元素，如果集合中没有这个值，会报错

discard（value）                   删除值为value的元素，如果集合中没有这个值，不会报错

pop(index)                            删除索引为index的元素

del my_set[index]                  删除索引为index的元素

查找元素：

bisect(value)                          返回value的索引

index(value)                           返回value的索引

my_set[index]                          按键索引

总结：

因为是有序容器，所有在删改容器的时候，使用的是二分法，时间复杂度大多是O(nlogn), 合理地运用这些容器，可以提高算法的时间复杂度。