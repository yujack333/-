## 函数（进阶介绍）：
  - 在 Python 中万物皆为对象，函数也不例外，函数作为对象可以**赋值给一个变量**、可以作为**元素添加到集合对象中**、可作为**参数值传递给其它函数**，还可以当做函数的**返回值**，这些特性就是**第一类对象**所特有的。函数接受一个或多个函数作为输入或者函数输出（返回）的值是函数时，我们称这样的函数为**高阶函数**。
  
  Python内置函数中，典型的高阶函数是 map 函数，map 接受一个函数和一个迭代对象作为参数，调用 map 时，依次迭代把迭代对象的元素作为参数调用该函数。
  ```python
    map(foo, ["the","zen","of","python"])
    lens = map(foo, ["the","zen","of","python"])
    list(lens)
    >>> [3, 3, 2, 6]
  ```
  
  实现了 _call_ 的类也可以作为函数:
  ```python
  class Add:
    def __init__(self, n):
         self.n = n
    def __call__(self, x):
        return self.n + x
 
>>> add = Add(1)
>>> add(4)
>>> 5
  ```
  
  嵌套函数是指函数的嵌套定义，而不是嵌套的调用，即函数中可以定义函数。
  
- 在介绍了函数具有第一对象的特点之后，我们可以来介绍一下装饰器（decorator）。
  **装饰器的作用**：在不改变被装饰函数源代码且不改变其调用方式的前提下，添加被装饰函数的功能。[写得通俗易懂的一个blog](https://www.jianshu.com/p/7a77f3f1ebc8)
  
  ```python
  import time

  def timer(parameter): # 这个函数是为了接收parameter

      def outer_wrapper(func): #这个函数是为了接收函数

          def wrapper(*args, **kwargs): #这个函数是为了接收函数的参数， task=func=wrapper
              if parameter == 'task1':
                  start = time.time()
                  res = func(*args, **kwargs)
                  stop = time.time()
                  print("the task1 run time is :", stop - start)
              elif parameter == 'task2':
                  start = time.time()
                  res = func(*args, **kwargs)
                  stop = time.time()
                  print("the task2 run time is :", stop - start)
              return res
          return wrapper

      return outer_wrapper

  @timer(parameter='task1')
  def task1():
      time.sleep(2)
      print("in the task1")
      return '123'

  @timer(parameter='task2')
  def task2():
      time.sleep(2)
      print("in the task2")
      return '345'

  t1 = task1()
  t2 = task2()
  ```
  @perporty

1.将成员函数变为属性

2.并且能赋值和检查赋的值是否符合要求

![](/pic/python_property.png)

![](/pic/python_property_2.png)

