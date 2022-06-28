#  python list 列表


## 1. 查对象（切片）

```bash
$ vim list0.py
a=['wuhao','jinxing','xiaohu','sanpang','ligang']
print(a[0]) #取第一个
print “a[1]:”,a[1]          #取第一个
print(a[1]) #取第二个
print(a[-1]) #取最后一个
print(a[1:4]) #取第二个到第四个
print(a[1:]) #取第二个到最后个
print(a[1:-1]) #取第二个到最后个
print(a[1:-1:1]) #从左到右一个一个取
print(a[1::2]) #从左到右隔一个一个取
b=a[3::-1] #从右到左一个一个取
print(b)
print(a[-2::-1])#从右到左一个一个取
print(a[1:-1:-2])#从右到左隔一个一个取
$ python list1.py
wuhao
a[1]: jinxing
jinxing
ligang
['jinxing', 'xiaohu', 'sanpang']
['jinxing', 'xiaohu', 'sanpang', 'ligang']
['jinxing', 'xiaohu', 'sanpang']
['jinxing', 'xiaohu', 'sanpang']
['jinxing', 'sanpang']
['sanpang', 'xiaohu', 'jinxing', 'wuhao']
['sanpang', 'xiaohu', 'jinxing', 'wuhao']
[]

```
特色版
```bash
first_two = [1, 2, 3, 4, 5][0:2]
print(first_two)
# [1, 2]

steps = [1, 2, 3, 4, 5][0:5:2]
print(steps)
# [1, 3, 5]

mystring = "abcdefdn nimt"[::2]
print(mystring)
# 'aced it'
```

## 2. 查索引(index)

```bash
$ cat list1.py
#!/usr/bin/python
#!---coding:utf-8----

a=['wuhao','jinxin','xiaohu','ligang','sanpang','ligang']
print(a.index('jinxin'))
first_lg_index = a.index("ligang")
print("first_lg_index",first_lg_index)
little_list = a[first_lg_index+1:]  #切片
second_lg_index = little_list.index("ligang")
print("second_lg_index",second_lg_index)
second_lg_index_in_bgg_list = second_lg_index + second_lg_index + 1
print("second_lg_index_in_bgg_list",second_lg_index_in_bgg_list)
print("sedcond lg:",a[second_lg_index_in_bgg_list])

$ python list9.py
1
('first_lg_index', 3)
('second_lg_index', 1)
('second_lg_index_in_bgg_list', 3)
('sedcond lg:', 'ligang')
```



##  3. 插入(append)

```bash
$ cat list2.py 
#!/usr/bin/python
#!---coding:utf-8----

a=['wuhao','jinxing','xiaohu','sanpang','ligang']
list = []          ## 空列表
a.append('xuepeng')  #将数据插入到最后一个位置
print(a)
a.insert(1,"liming") #将数据插入到任意一个位置（有一个查询动作，效率高）
print(a)

numbers = [12,37,5,42,8,3]
even = []
odd = []
while len(numbers) > 0:
  number = numbers.pop()
  if(number % 2) == 0:
     even.append(number)
  else:
     odd.append(number)
print even
print odd
$ python list2.py 
['wuhao', 'jinxing', 'xiaohu', 'sanpang', 'ligang', 'xuepeng']
['wuhao', 'liming', 'jinxing', 'xiaohu', 'sanpang', 'ligang', 'xuepeng']
[8, 42, 12]
[3, 5, 37]
```
##  4. 增添(extend)
**第一种增添**
```bash
$ cat  list3.py
#!/usr/bin/python
#!---coding:utf-8----

a = [1,2,3]
b = [4,5,6]
a.extend(b)
print a

$ python list3.py
[1, 2, 3, 4, 5, 6]
```
**第二种增添**
```bash
listone = [1, 2, 3]
listtwo = [4, 5, 6]

mergedlist = listone + listtwo

print(mergelist)
>>>
[1, 2, 3, 4, 5, 6]
```
**增添" + "的扩展（class）**

```bash
class User(object):
    def __init__(self, age):
        self.age = age

    def __repr__(self):
        return 'User(%d)' % self.age

    def __add__(self, other):
        age = self.age + other.age
        return User(age)

user_a = User(10)
user_b = User(20)

c = user_a + user_b

print(c)

>>>
User(30)
```

## 5. 删除 (remove pop del）

### 5.1 remove

```bash
$ cat list4.py 
#!/usr/bin/python
#!---coding:utf-8----

a=['wuhao','jinxing','xiaohu','sanpang','ligang']
a.remove(a[0])
print(a)
a.remove('jinxing')
print(a)

$ python list4.py 
['jinxing', 'xiaohu', 'sanpang', 'ligang']
['xiaohu', 'sanpang', 'ligang'] 
```

### 5.2 pop

```bash
$ cat list5.py
#!/usr/bin/python
#!---coding:utf-8----

a=['wuhao','jinxing','xiaohu','sanpang','ligang']
a.pop(1) #pop删除时会返回被删除的元素
b=a.pop(1)
print(b)
print(a)

$  python list5.py 
xiaohu
['wuhao', 'sanpang', 'ligang']
```

### 5.3 del

```bash
$ cat list6.py
#!/usr/bin/python
#!---coding:utf-8----
a=['wuhao','jinxing','xiaohu','sanpang','ligang']

del a[0]
print(a)

del a[2:4]  #删除从第2个元素开始，到第4个为止的元素(但是不包括尾部元素)
print(a)

$ python list6.py 
['jinxing', 'xiaohu', 'sanpang', 'ligang']
['jinxing', 'xiaohu']
```

## 6. 修改

```bash
$ cat list7.py 
#!/usr/bin/python
#!---coding:utf-8----

a=['wuhao','jinxing','xiaohu','sanpang','ligang']
a[1]='haidilao'
print(a)
a[1:3]=['a','b']
print(a)

$  python list7.py 
['wuhao', 'haidilao', 'xiaohu', 'sanpang', 'ligang']
['wuhao', 'a', 'b', 'sanpang', 'ligang']
```
##  7. 计数(count)

```bash
$ cat list8.py
#!/usr/bin/python
#!---coding:utf-8----

print ['to','be','or','not','to','be'].count('to')
x = [[1, 2],1,1,[1,2],3]
print x.count(1)
print x.count([1,2])

$  python list8.py
2
2
2
```



## 8.  颠倒(reverse)

```bash
$ cat  list10.py
#!/usr/bin/python
#!---coding:utf-8----

a=['wuhao','jinxin','xiaohu','ligang','sanpang','ligang']
a.reverse()
print(a)

$ python list10.py
['ligang', 'sanpang', 'ligang', 'xiaohu', 'jinxin', 'wuhao']
```
## 9. 排序(sort)
### 9.1 普通版
```bash
$ cat  list11.py
#!/usr/bin/python
#!---coding:utf-8----

x = [4,6,1,7,9]
x.sort()
print(x)
x.sort(reverse=True)    
print(x)
a = ['Wuhao','jinxin','xiaohu','ligang','sanpang','ligang']
a.sort()
print(a)

$  python list11.py
[1, 4, 6, 7, 9]
[9, 7, 6, 4, 1]
['Wuhao', 'jinxin', 'ligang', 'ligang', 'sanpang', 'xiaohu']
```
默认是按升序排列，指定 reverse=True 按降序排列 
### 9.2 升级版

```bash
items = [{'name': 'Homer', 'age': 39},
         {'name': 'Bart', 'age': 10},
         {"name": 'cater', 'age': 20}]

items.sort(key=lambda item: item.get("age"))

print(items)

>>>
[{'age': 10, 'name': 'Bart'}, {'age': 20, 'name': 'cater'}, {'age': 39, 'name': 'Homer'}]
```
## 10. 不改变源列表排序（sorted）

```bash
items = [{'name': 'Homer', 'age': 39},
         {'name': 'Bart', 'age': 10},
         {"name": 'cater', 'age': 20}]

new_items = sorted(items, key=lambda item: item.get("age"))

print(items)
>>>
[{'name': 'Homer', 'age': 39}, {'name': 'Bart', 'age': 10}, {'name': 'cater', 'age': 20}]

print(new_items)
>>>
[{'name': 'Bart', 'age': 10}, {'name': 'cater', 'age': 20}, {'name': 'Homer', 'age': 39}]
```
----
## 11. 列表脚本操作符

列表对 + 和 * 的操作符与字符串相似。+ 号用于组合列表，* 号用于重复列表。
如下所示：

```bash
>>> len([1, 2, 3]) 
3 长度
>>> [1, 2, 3] + [4, 5, 6] 
 [1, 2, 3, 4, 5, 6]   #组合
>>> ['Hi!'] * 4   
['Hi!', 'Hi!', 'Hi!', 'Hi!']   # 重复
>>>3 in [1, 2, 3] 
True     # 元素是否存在于列表中
>>>for x in [1, 2, 3]: print x
1 2 3    #迭代
```
------

## 12. 列表函数

Python包含以下函数:

 - 1 cmp(list1, list2)比较两个列表的元素
 - 2 len(list)列表元素个数
 - 3 max(list)返回列表元素最大值
 - 4 min(list)返回列表元素最小值
 - 5 list(seq)将元组转换为列表

---------------
## 13. 列表推导式
列表推导式的基本语法如下：

```bash
[expression for item in list if conditional]
```

举一个基本的例子：用一组有序数字填充一个列表：

```bash
mylist = [i for i in range(10)]
print(mylist)
# [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

由于可以使用表达式，所以你也可以做一些算术运算：

```bash
squares = [x**2 for x in range(10)]
print(squares)
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

甚至可以调用外部函数：

```bash
def some_function(a):
    return (a + 5) / 2

my_formula = [some_function(i) for i in range(10)]
print(my_formula)
# [2, 3, 3, 4, 4, 5, 5, 6, 6, 7]
```

最后，你还可以使用 ‘if’ 来过滤列表。在如下示例中，我们只保留能被2整除的数字：

```bash
filtered = [i for i in range(20) if i%2==0]
print(filtered)
# [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```
----
## 14. 列表常用场景

### 14.1 遍历

```bash
$ cat  list12.py
#!/usr/bin/python
#!---coding:utf-8----

a_list = ['z', 'c', 1, 5, 'm']
for each in a_list:
    print(each)
$ python list12.py
z
c
1
5
m
```

### 14.2 遍历加索引

**普通版**
```bash
items = [8, 23, 45]
for index in range(len(items)):
    print(index, "-->", items[index])

>>>
0 --> 8
1 --> 23
2 --> 45
```
**优雅版**

```bash
for index, item in enumerate(items):
    print(index, "-->", item)

>>>
0 --> 8
1 --> 23
2 --> 45
```
enumerate 还可以指定元素的第一个元素从几开始，默认是0，也可以**指定从1开始：**

```bash
for index, item in enumerate(items, start=1):
    print(index, "-->", item)

>>>
1 --> 8
2 --> 23
3 --> 45
```

### 14.3 多列表遍历

#### 14.3.1 zip

```bash
$ cat list13.py 
#!/usr/bin/python
#!---coding:utf-8----

name_list = ['张三', '李四', '王五']
age_list = [54, 18, 34]
for name, age in zip(name_list, age_list):
    print(name, ':', age)

$ python3.8 list13.py 
张三 : 54
李四 : 18
王五 : 34
```

```bash
cat  list14.py
#!/usr/bin/python
#!---coding:utf-8----
list1 = [1, 2, 3, 4, 5]
list2 = ['a', 'b', 'c', 'd', 'f']
list3 = ['A', 'B', 'C', 'D', 'F'] 
for number, lowercase, capital in zip(list1, list2, list3):
    print(number, lowercase, capital)

$ python3.8 list14.py 
1 a A
2 b B
3 c C
4 d D
5 f F
```

```bash
$ cat list15.py 
#!/usr/bin/python
#!---coding:utf-8----

name_list = ['张三', '李四', '王五']
age_list = [54, 18, 34]
print(zip(name_list, age_list))
print(type(zip(name_list, age_list)))
print(*zip(name_list, age_list))
print(list(zip(name_list, age_list)))
print(dict(zip(name_list, age_list)))

$ python3.8 list15.py 
<zip object at 0x7f554227c300>
<class 'zip'>
('张三', 54) ('李四', 18) ('王五', 34)
[('张三', 54), ('李四', 18), ('王五', 34)]
{'张三': 54, '李四': 18, '王五': 34}
```

#### 14.3.2 笨办法多列表遍历

```bash
cat  list16.py
#!/usr/bin/python
#!---coding:utf-8----
list1 = [1, 2, 3, 4, 5]
list2 = ['a', 'b', 'c', 'd', 'f']
 
n = 0
for each in list1:
    print(each, list2[n])
    n += 1
$ python3.8 list16.py 
1 a
2 b
3 c
4 d
5 f
```

### 14.4 检查列表是否为空

普通版：

```p#ython
if len(items) == 0:
    print("空列表")

或者

if items == []:
    print("空列表")
```

**优雅版：**

```bash
$ cat list17.py 
#!/usr/bin/python
#!---coding:utf-8----

list1 = [1, 2, 3, 4, 5]
if not list1:
    print("空列表")
else:
    print("非空列表")

$ python3.8 list17.py 
非空列表

```

### 14.5 拷贝一个列表对象

第一种方法：
```bash
new_list = old_list[:]
```
第二种方法：
```bash
new_list = list(old_list)
```
第三种方法：
```bash
import copy
# 浅拷贝
new_list = copy.copy(old_list)
# 深拷贝
new_list = copy.deepcopy(old_list)
```

### 14.6 获取列表中的最后一个元素

索引列表中的元素不仅支持正数还支持负数，正数表示从列表的左边开始索引，负数表示从列表的右边开始索引，获取最后一个元素有两种方法。

```bash
>>> a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> a[len(a)-1]
10
>>> a[-1]
10
```

### 14.7 随机获取列表中的某个元素

```bash
import random
items = [8, 23, 45, 12, 78]

>>> random.choice(items)
78
>>> random.choice(items)
45
>>> random.choice(items)
12
```

### 14.8 list与string的转换  

#### 14.8.1 list转换string
`join()`函数
list = [1, 2, 3, 4, 5]

''.join(list) 结果即为：12345

','.join(list) 结果即为：1,2,3,4,5

```bash
a = 5
str=[] #有的题目要输出字符串，但是有时候list更好操作，于是可以最后list转string提交
for i in range(0,a):
    str.append('M')
str1=''.join(str)
print(str1)
#MMMMM
```
#### 14.8.2 string转换list

```bash
str = 'abcde'
print(str)
#输出：abcde
list1 = list(str) 
print(list1)
#输出：['a', 'b', 'c', 'd', 'e']
```

### 14.9 list（列表）添加dict（字典）
```[python]
nid = "1,2"
print(nid.split(','))
datas = []
for i in nid.split(','):
    mydict = {}
    mydict["id"] = str(i)
    mydict["checked"] = True
    datas.append(mydict)
print(str(datas))
```
输出
```bash
['1', '2']  
[{'id': '1', 'checked': True}, {'id': '2', 'checked': True}]
```

###  14.20 txt文件内容转换成列表

方法一：

```bash
# -*- coding:utf-8 -*-
f = open(r'ip.txt','r')
a = list(f)
print(a)
f.close()
```

方法二：

```bash
# -*- coding:utf-8 -*-
f = open(r'ip.txt','r')
a=[i for i in f]
print(a)
f.close()
```
----

✈<font color=	#FF4500 size=4 style="font-family:Courier New">推荐阅读：</font>

 - [python dict 字典](https://blog.csdn.net/xixihahalelehehe/article/details/104488899)
 - [pyhon tuple 元组](https://blog.csdn.net/xixihahalelehehe/article/details/104486159)
 - [python list 列表](https://blog.csdn.net/xixihahalelehehe/article/details/104437743)

