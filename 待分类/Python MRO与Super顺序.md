# Python MRO与Super顺序.md
python中的类型都有一个__mro__属性，借此实现了super的调用顺序
```python
class A(object):
    def __init__(self):
        super().__init__()
        print('Init A')

class B(object):
    def __init__(self):
        super().__init__()
        print('Init B')
        

class C(A):
    def __init__(self):
        super().__init__()
        print("Init C")
        
class D(B):
    def __init__(self):
        super().__init__()
        print("Init D")

class E(C, D):
    def __init__(self):
        super().__init__()
        print("Init E")

if __name__ == "__main__":
    E()
    print(E.__mro__)
```

```python
# 打印结果
# Init B
# Init D
# Init A
# Init C
# Init E
# (<class '__main__.E'>, <class '__main__.C'>, <class '__main__.A'>, <class '__main__.D'>, <class '__main__.B'>, <class 'object'>)
```

从结果可以看到，mro的顺序是E，C，A，D，B

代码中在每个类的__init__中都是先调用super.__init__，然后才是打印当前类名，简单的说就是super+print

执行顺序：
1. E() -> E中super+print
2. E中super：根据mro显示，E的super是C -> C中super+print
3. C中super：根据mro显示，C的super是A -> A中super+print
4. A中super：根据mro显示，A的super是D -> D中super+print
5. D中super：根据mro显示，D的super是B -> B中super+print
6. B中super：根绝mro显示，B的super是object -> 空，object的init方法是一个空方法
7. B执行完super，开始执行print -> 'Init B'
9. D执行完super，开始执行print -> 'Init D'
10. A执行完super，开始执行print -> 'Init A'
11. C执行完super，开始执行print -> 'Init C'
12. E执行完super，开始执行print -> 'Init E'

所以最终打印顺序是B D A C E

- 可能有些人想问，为什么A中调用super会是D能。不是因为是object吗？
    - super在寻找时，并不是按照超类进行寻找的。而是根据mro进行寻找的。每个类有自己的mro，针对E而言，在mro中A的super就是D。而针对A自身而言，mro中自己的super就是object。
    - 这段代码是从E开始寻找，所以mro采用的是E的mro。
    - 可以单独对应A的mro
        ```python
        # (<class '__main__.A'>, <class 'object'>)
        ```
    - 还可以尝试，把A中的super去掉，然后再运行
        ```python
        # 结果是
        # Init A
        # Init C
        # Init E
        ```
        原因就是到A的时候，不再需要向mro上级查询了，直接print了，所以就没有打印BD了。


