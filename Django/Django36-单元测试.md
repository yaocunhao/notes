- [gd](https://docs.djangoproject.com/zh-hans/3.2/topics/testing/overview/)

  

  # 一、Django 单元测试的使用

  ## 1.1 测试数据库的自动创建

  - 继承`django.test.TestCase` 会自动创建测试数据库，并且在单元测试运行前后自动删除掉数据库。继承的python的`TestCase` 将不会创建

    - 由于历史原因，继承后会发生些问题无法创建历史数据库，因此要加上下面这行

    ```python
    class SimpleTest(TestCase):
      databases = '__all__' // 解决无法创建的问题
    	def test_finc(self):
        pass
    ```

  ## 1.2 Django 单元测试工具

  - [模拟游览器行为](https://docs.djangoproject.com/en/5.0/topics/testing/tools/#exceptions)

  ```python
  >>> from django.test import Client
  >>> c = Client()
  >>> response = c.post("/login/", {"username": "john", "password": "smith"})
  >>> response.status_code
  200
  >>> response = c.get("/customer/details/")
  >>> response.content
  b'<!DOCTYPE html...'
  ```

  - [请求工厂](https://docs.djangoproject.com/en/5.0/topics/testing/advanced/#the-request-factory)
    - 非模拟 游览器行为，而是直接从代码中抽取某个函数接口，像本地调用一样运行。和python 提供的单元测试相比，可以创建、读取数据库

  ## 1.3 数据库保存，多个测试联动

  ## 1.4 执行顺序

  - 需要重写Django的测试类
  
    ```python
    在Django中，测试用例的执行顺序是由测试运行器控制的。默认情况下，Django使用的是django.test.runner.DiscoverRunner作为测试运行器。该运行器会按照测试用例的名称的字母顺序来执行测试。
    
    如果你想自定义测试用例的执行顺序，可以通过创建一个自定义的测试运行器来实现。你可以继承django.test.runner.DiscoverRunner类，并重写get_tests方法来指定测试用例的执行顺序。
    
    下面是一个示例，展示了如何自定义测试用例的执行顺序：
    
    from django.test.runner import DiscoverRunner
    
    class CustomTestRunner(DiscoverRunner):
        def get_tests(self, test_labels, **kwargs):
            # 获取所有的测试用例
            suite = super().get_tests(test_labels, **kwargs)
            
            # 对测试用例进行排序
            sorted_suite = sorted(suite, key=lambda x: x.__class__.__name__)
            
            return sorted_suite
    在上面的示例中，我们创建了一个名为CustomTestRunner的自定义测试运行器，并重写了get_tests方法。在该方法中，我们首先调用了父类的get_tests方法来获取所有的测试用例，然后使用sorted函数对测试用例进行排序，排序的依据是测试用例的类名。最后，我们返回排序后的测试用例。
    
    要使用自定义的测试运行器，你需要在settings.py文件中将TEST_RUNNER设置为自定义运行器的路径，例如：
    
    TEST_RUNNER = 'myapp.testrunner.CustomTestRunner'
    
    ```
  
    
  
  
