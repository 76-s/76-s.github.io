---
title: Python中的 变量、函数、库、类、方法、属性
date: 2026-03-11 13:14:52
categories: 编程
tags:
- python
---

##### what & how

变量（Variable）：存储数据的名字，指向某个值。

```python
age = 18
name = "Tom"
```

函数（Function）：可以重复调用的代码块，用来完成某个功能。

```python
def add(a, b):
    return a + b

result = add(3, 5)
print(result)
```

库/模块（Library/Module）：别人已经写好的功能集合，可直接导入使用。

```python
import math

print(math.sqrt(16))
```

类（Class）：创建对象的模版，描述一类事物的属性和方法。

```python
class Dog:
    def bark(self):
        print("汪汪")
```

方法（Method）：类中的函数称为“方法”，但它们仍然是函数，描述对象的行为。

```python
class Dog:
    def bark(self):
        print("汪汪")

dog = Dog()
dog.bark()
```

属性（Attribute）：对象或类中存储的数据变量

```python
class Dog:
    def __init__(self, name):
        self.name = name

dog = Dog("旺财")
print(dog.name)
```



##### 总结

```python
"""
函数 function
  │
  ▼
独立调用
  add(3,5)

类 class
  │
  ▼
创建对象
  dog = Dog("旺财")
       │
       ├── 属性  (dog.name = "旺财")
       └── 方法  (dog.bark())
"""
# 函数
def add(a, b):      # 独立函数
    return a + b
print(add(2, 3))    # 5

# 类和方法
class Dog:
    def __init__(self, name):  # 方法
        self.name = name      # 属性
    def bark(self):            # 方法
        print(self.name, "汪汪")
# 实例化对象
dog = Dog("旺财")  
dog.bark()                   # 调用对象的方法
print(dog.name)              # 访问对象的属性  

## 输出
5
旺财 汪汪
旺财
```
