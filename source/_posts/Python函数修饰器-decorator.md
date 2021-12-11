---
title: Python 函数修饰器 (decorator)
date: 2019-2-23
categories:
- Code
- python
tags:
- decorator
---

函数修饰器修改其他函数的功能的函数，装饰器可以让你在一个函数的前后去执行指定代码

```python
def decorator_func(fn):
    def inner(*args, **kwargs):
        print("="*20)
        fn(*args, **kwargs)
        print("="*20)
    return inner
@decorator_func
def test_func(*args, **kwargs):
    print("Hello world!")
test_func()
```

<!-- more -->

上面代码的运行结果如下

```bash
====================
Hello world!
====================
```
