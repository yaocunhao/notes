# 一、with的作用

- 在写代码过程中，我们一般会有这样的问题，如文件描述符打开，关闭，锁的加锁，解锁，一旦缺少任何一环都会对系统造成严重影响，如果不能正确关闭描述符，就会导致可用的描述符数量急剧下降，如果不能正常解锁，会导致严重的死锁问题
- python中存在一个用法：with，可以帮助我们自动的进行资源的获取和释放，从而避免上述问题

# 二、原理

- with在执行的时候，会执行紧随其后的代码，并调用该对象的__enter__方法， 执行相关操作，如果出现异常，或者代码执行结束，则会自动调用__exit__方法，进行资源的清理
- 基本思想是
  - with所求值的对象必须有一个enter()方法，一个exit()方法
  - enter方法最先被执行，返回的值将会被赋给as后面的变量
  - `:` 后面的代码执行完毕后会调用exit()方法

# 三、用法

```python
class TBase:
    def __enter__(self):
        print("---enter----")

    def __exit__(self, exc_type, exc_val, exc_tb):  # 参数为异常信息
        print("---exit----", exc_type, exc_val, exc_tb)

    def func(self):
        print("--notic func---", self)

        
# 不写as
with TBase():
    print("----action----")


---enter----
----action----
---exit---- None None None

# 写as
class TBase:
    def __enter__(self):
        print("---enter----")
        return self  # 返回一个对象

    def __exit__(self, exc_type, exc_val, exc_tb):  # 参数为异常信息
        print("---exit----", exc_type, exc_val, exc_tb)

    def func(self):
        print("--notic func---")


with TBase() as tb:
    print("----action----")
    tb.func()
    
# ---enter----
# ----action----
# --notic func---
# ---exit---- None None None

```

