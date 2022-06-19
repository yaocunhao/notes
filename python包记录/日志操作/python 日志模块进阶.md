# 一、日志库组件

- 日志库采用模块化方法，并提供几类组件：记录器、处理器、过滤器和格式器
  - 记录器暴露了应用程序代码直接使用的接口
  - 处理器将日志记录（由记录器创建）发送到适当的目标
  - 过滤器提供了更细粒度的功能，用于确定要输出的日志记录
  - 格式器指定最终输出中日志记录的样式

- 日志事件信息在 [`LogRecord`](https://docs.python.org/zh-cn/3/library/logging.html#logging.LogRecord) 实例中的记录器、处理器、过滤器和格式器之间传递



# 二、记录器

## 2.1 记录器的三种任务

- **记录信息**：向应用程序代码公开了几种方法，以便应用程序可以在运行时记录消息
- **过滤：**记录器对象根据严重性，过滤对象确定要处理的日志消息
- **传递：**记录器对象将相关的日志消息传递给所有感兴趣的日志处理器



## 2.2  配置和消息发送

- 记录器对象上使用最广泛的方法分为两类：配置和消息发送

### 2.2.1 常见的配置方法

- [`Logger.setLevel()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.setLevel) 
  - **指定记录器将处理的最低严重性日志消息**，其中 debug 是最低内置严重性级别， critical 是最高内置严重性级别。 例如，如果严重性级别为 INFO ，则记录器将仅处理 INFO 、 WARNING 、 ERROR 和 CRITICAL 消息，并将忽略 DEBUG 消息。
- [`Logger.addHandler()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.addHandler) 和 [`Logger.removeHandler()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.removeHandler) 
  - **从记录器对象中添加和删除处理器对象**
- [`Logger.addFilter()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.addFilter) 和 [`Logger.removeFilter()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.removeFilter) 
  - **可以添加或移除记录器对象中的过滤器**

### 2.2.2 创建日志消息方法

- [`Logger.debug()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.debug) 、 [`Logger.info()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.info) 、 [`Logger.warning()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.warning) 、 [`Logger.error()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.error) 和 [`Logger.critical()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.critical) 都创建日志记录**，包含消息和与其各自方法名称对应的级别**
- [`Logger.exception()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.exception) 创建与 [`Logger.error()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.error) 相似的日志信息。 不同之处是， [`Logger.exception()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.exception) 同时还记录当前的堆栈追踪。**仅从异常处理程序调用此方法**
- [`Logger.log()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.log) 将日志级别作为显式参数。对于记录消息而言，这比使用上面列出的日志级别便利方法更加冗长，但这是使用自定义日志级别的方法



## 2.3 记录器对象

### 2.3.1 对象的获取

- [`getLogger()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.getLogger) 返回对具有指定名称的记录器实例的引用（如果已提供），或者如果没有则返回 `root` (**即通过该函数，进行记录器对象的创建**)
- 名称是以句点分隔的层次结构。**多次调用** [`getLogger()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.getLogger) 具有相同的名称将**返回对同一记录器对象的引用**
- 在分层列表中较**低的记录器是列表中较高的记录器的子项**。例如，给定一个名为 `foo` 的记录器，名称为 `foo.bar` 、 `foo.bar.baz` 和 `foo.bam` 的记录器都是 `foo` 子项

### 2.3.2 *有效等级* 

- 记录器具有 *有效等级* 的概念
- 如果**未在记录器上显式设置级别**，**则使用其父记录器的级别**作为其有效级别。如果父记录器没有明确的级别设置，则检查 *其* 父级。依此类推，搜索所有上级元素，直到找到明确设置的级别。根记录器始终具有明确的级别配置（默认情况下为 `WARNING` ）。在决定是否处理事件时，记录器的有效级别用于确定事件是否传递给记录器相关的处理器
- **简而言之，子类没有设定找父类，还是没有使用默认**

### 2.3.3 传播

- 子记录器将消息传播到与其父级记录器关联的处理器。因此，不必为应用程序使用的所有记录器定义和配置处理器
- 一般**为顶级记录器配置处理器，**再根据需要创建子记录器就足够了。（但是，你可以通过将记录器的 *propagate* 属性设置为 `False` 来关闭传播）

# 三、处理器

- [`Handler`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler) 对象负责将适当的日志消息（基于日志消息的严重性）分派给处理器的指定目标
-  [`Logger`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger)记录器 对象可以使用 [`addHandler()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger.addHandler) 方法向自己添加零个或多个处理器对象
- 不同的处理器可以将日志消息发往不同的地方，**比如写到文件中，发送邮件，使用的就是两个不同的处理器**[常见处理器类型](https://docs.python.org/zh-cn/3/howto/logging.html#useful-handlers)

- 处理器中很少有方法可供应用程序开发人员使用。使用内置处理器对象（即不创建自定义处理器）的应用程序开发人员能用到的仅有以下配置方法：

  - `setLevel()` 方法，就像在记录器对象中一样，指定将被分派到适当目标的最低严重性。为什么有两个 `setLevel()` 方法？记录器中设置的级别确定将传递给其处理器的消息的严重性。每个处理器中设置的级别确定该处理器将发送哪些消息。

  - [`setFormatter()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler.setFormatter) 选择一个该处理器使用的 Formatter 对象。

  - [`addFilter()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler.addFilter) 和 [`removeFilter()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler.removeFilter) 分别在处理器上配置和取消配置过滤器对象。

- 应用程序代码不应直接实例化并使用 [`Handler`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler) 的实例。 相反， [`Handler`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler) 类是一个基类，它定义了所有处理器应该具有的接口，并建立了子类可以使用（或覆盖）的一些默认行为。

# 四、格式器

- 格式化器对象配置日志消息的最终顺序、结构和内容

- 与 [`logging.Handler`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Handler) 类不同，应用程序代码可以实例化格式器类，但如果应用程序需要特殊行为，则可能会对格式化器进行子类化定制。构造函数有三个可选参数 —— 消息格式字符串、日期格式字符串和样式指示符。

  - `logging.Formatter.``__init__`(*fmt=None*, *datefmt=None*, *style='%'*)

    

  如果没有消息格式字符串，则默认使用原始消息。如果没有日期格式字符串，则默认日期格式为：

  ```
  %Y-%m-%d %H:%M:%S
  ```

  最后加上毫秒数。 `style` 是 ％，'{ ' 或 '$' 之一。 如果未指定，则将使用 '％'。

  如果 `style` 是 '％'，则消息格式字符串使用 `%(<dictionary key>)s` 样式字符串替换；可能的键值在 [LogRecord 属性](https://docs.python.org/zh-cn/3/library/logging.html#logrecord-attributes) 中。 如果样式为 '{'，则假定消息格式字符串与 [`str.format()`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str.format) （使用关键字参数）兼容，而如果样式为 '$' ，则消息格式字符串应符合 [`string.Template.substitute()`](https://docs.python.org/zh-cn/3/library/string.html#string.Template.substitute) 。

  *在 3.2 版更改:* 添加 `style` 形参。

  以下消息格式字符串将以人类可读的格式记录时间、消息的严重性以及消息的内容，按此顺序:

  ```
  '%(asctime)s - %(levelname)s - %(message)s'
  ```

  格式器通过用户可配置的函数将记录的创建时间转换为元组。 默认情况下，使用 [`time.localtime()`](https://docs.python.org/zh-cn/3/library/time.html#time.localtime) ；要为特定格式器实例更改此项，请将实例的 `converter` 属性设置为与 [`time.localtime()`](https://docs.python.org/zh-cn/3/library/time.html#time.localtime) 或 [`time.gmtime()`](https://docs.python.org/zh-cn/3/library/time.html#time.gmtime) 具有相同签名的函数。 要为所有格式器更改它，例如，如果你希望所有记录时间都以 GMT 显示，请在格式器类中设置 `converter` 属性（对于 GMT 显示，设置为 `time.gmtime` ）。



# 五、日志配置方法

- 开发者可以通过三种方式配置日志记录：
  - 使用调用上面列出的配置方法的 Python 代码显式创建记录器、处理器和格式器
  - 创建日志配置文件并使用 [`fileConfig()`](https://docs.python.org/zh-cn/3/library/logging.config.html#logging.config.fileConfig) 函数读取它
  - 创建配置信息字典并将其传递给 [`dictConfig()`](https://docs.python.org/zh-cn/3/library/logging.config.html#logging.config.dictConfig) 函数