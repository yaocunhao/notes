- [总体描述参考链接](http://www.uml.org.cn/oobject/201609092.asp)

- UML中有九种建模的图标，即：用例图、类图、对象图、顺序图、协作图、状态图、活动图、组件图、配置图

  ![image-20230214141417333](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141414378.png)

# 一、用例图

- 用例图Use case diagrams描述了作为一个外部的观察者的视角对系统的印象。强调这个系统是什么而不是这个系统怎么工作

  ![image-20230214141144344](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141411683.png)

# 二、类图

- [参考链接](https://blog.csdn.net/Czhenya/article/details/117266283?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167635282716800217061660%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167635282716800217061660&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-117266283-null-null.142^v73^insert_down3,201^v4^add_ask,239^v1^insert_chatgpt&utm_term=uml%E7%B1%BB%E5%9B%BE&spm=1018.2226.3001.4187)
- [link](https://www.jianshu.com/p/1ca6059b55fd)
- UML类的符号是一个被划分成三块的方框：类名，属性，和操作。抽象类的名字

## 2.1 关系

- 泛化（Generalization）：即继承extends， 子类 ━━▷ 父类

-  实现（Realization）：即实现implements，实现类 ‑‑‑▷ 接口

- 依赖（Dependency）：弱关联，一个类是另一个类的方法的参数， 主类 ‑‑‑＞参数类

- 单向关联（Association）：一个类是另一个类的成员，个体间的**从属关系**， 主类 ──＞从类

- 双向关联（Association）：互相为成员，个体类 ── 个体类

- 聚合（Aggregation）：一个类是另一个类的成员，可分的**包含关系**，整体类 ◇──＞部分类

- 组合（Composition）：强聚合，不可分的包含关系，整体类 ◆──＞部分类

- **聚合和单向关联的区别**：

  - 大雁和雁群之间就是聚合的关系

  - 订单和商品价格就是单向关联关系(订单除了包含商品加个还包含了其它的信息)

## 2.2 辅助概念

- 箭头方向：箭尾表示主类，箭头表示子类

- 关联有两个尾端。每个尾端可以有一个角色名来说明关联的作用

  ![image-20230214150340782](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141503921.png)

![image-20230214150620050](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141506106.png)

# 三、对象图

- [link](https://blog.csdn.net/yao940622/article/details/54670388?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1-54670388-blog-52987488.pc_relevant_multi_platform_whitelistv3&spm=1001.2101.3001.4242.2&utm_relevant_index=4)

- 对象图（ObjectDiagram）描述的是参与交互的各个对象在交互过程中某一时刻的状态。对象图可以看做是类图在某一时刻的实例

- 在UML中，对象图的使用是与类图相同的符号和关系，因为对象就是类的实例。由**于对象存在生命周期，因此对象图只能在系统某一个时间段内存在**

- UML中实例名带有下划线。只要意思清楚，类或实例名可以在对象图中被省略

  ![image-20230214152523719](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141525791.png)

​		![image-20230214152635840](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141526908.png)

# 四、包图

- 包图是 UML 一种用以显示包和包之间的依赖的结构性图表，**UML包图通常用于描述系统的逻辑架构——层、子系统、包等**

- <font color=yellow>包图之间的关系主要用两个字概括：依赖</font>

- 如果另一个的包B改变可能会导致一个包A改变，则包A依赖包B。箭头指向"更小"的一方，即指向B

  ![image-20230214155746442](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302141557518.png)

# 五、顺序图

- [参考链接](https://blog.csdn.net/xiaopangcame/article/details/128103212?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EAD_ESQUERY%7Eyljh-1-128103212-blog-77929860.pc_relevant_aa2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EAD_ESQUERY%7Eyljh-1-128103212-blog-77929860.pc_relevant_aa2&utm_relevant_index=2)

## 5.1 基本概念

- 我们在画时序图时会涉及7种元素：角色(Actor)、对象(Object)、生命线(LifeLine)、控制焦点(Activation)、消息(Message)、自关联消息、组合片段
- 角色：系统角色，可以是人或者其他系统，子系统。以一个小人图标表示
- 对象：对象位于时序图的顶部,以一个矩形表示。对象的命名方式一般有三种
  - 对象名和类名。例如：华为手机:手机、loginServiceObject:LoginService
  - 只显示类名。不显示对象，即为一个匿名类。例如：手机、:LoginSservice
  - 只显示对象名，不显示对象，即为一个匿名类。例如:手机、:LoginSservice
- 生命线：时序图中每个对象和底部中心都有一条垂直的虚线，这就是对象的生命线(对象的时间线)。以一条垂直的虚线表示
- 控制焦点：控制焦点代表时序图中在对象时间线上某段时期执行的操作。以一个很窄的矩形表示
- 消息：表现代表对象之间发送的信息。消息分为三种类型
  - 同步消息:消息的发送者把控制传递给消息的接收者，然后停止活动，等待消息的接收者放弃或者返回控制。用来表示同步的意义。以一条**实线+实心箭头表示**
  - 异步消息:发送者通过消息把信号传递给消息的接收者，然后继续自己的活动，不等待接受者返回消息或者控制。异步消息的接收者和发送者是并发工作的。**以一条实线+大于号表示**
  - 返回消息:返回消息表示从过程调用返回。**以小于号+虚线表示**
- 自关联消息:表示方法的自身调用或者一个对象内的一个方法调用另外一个方法。以一个**半闭合的长方形+下方实心剪头表示**

## 5.2 组合片段

- 组合片段用来解决交互执行的条件和方式，它允许在[序列图](https://so.csdn.net/so/search?q=序列图&spm=1001.2101.3001.7020)中直接表示逻辑组件，用于通过指定条件或子进程的应用区域，为任何生命线的任何部分定义特殊条件和子进程。组合片段共有13种，名称及含义如下![image-20230215084451415](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302150844492.png)

# 六、协作图

- [参考链接](https://blog.csdn.net/neusoft2016/article/details/116195191?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167642224916800225537442%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167642224916800225537442&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-116195191-null-null.142^v73^insert_down3,201^v4^add_ask,239^v1^insert_chatgpt&utm_term=Uml%20%E5%8D%8F%E4%BD%9C%E5%9B%BE&spm=1018.2226.3001.4187)

- 协作图(Collaboration Diagram /Communication Diagram，也叫合作图、通信图)是另一种交互图（interaction diagram）。它强调的是对象之间的交互关系。表示了协作中各元素所处的位置。在一个协作中，描述了该协作中所有对象组成的网络结构以及相互发送消息的整体行为。协作图**虽然也表明了消息的先后顺序，但是相比序列图而言，没有序列图清晰一目了然**

- 协作图有三种基本元素：对象，消息，链![image-20230215085806731](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302150858831.png)

  

# 七、状态图

- [参考链接](https://blog.csdn.net/neusoft2016/article/details/116902865?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167642281016800184150732%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167642281016800184150732&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-116902865-null-null.142^v73^insert_down3,201^v4^add_ask,239^v1^insert_chatgpt&utm_term=%E7%8A%B6%E6%80%81%E5%9B%BE&spm=1018.2226.3001.4187)
- 状态图更适用于审批流程观看![image-20230215093858382](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302150938487.png)

# 八、活动图

- [参考链接](https://stronger.blog.csdn.net/article/details/117412480?spm=1001.2014.3001.5502)
- 活动图(Activity Diagram)是描述满足用例要求所要进行的活动以及活动间的约束关系，有利于识别并行活动。它**对于系统的功能建模特别重要**，强调对象间的控制流程，**活动图在本质上是一种流程图**
- 活动图主要应用于不同系统之间的合作(不同的系统的描述在各自的泳道)![image-20230215094019503](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302150940565.png)

# 九、组件图

- 组件图又称为构件图(Component Diagram) 。组件图中通常包括组件、接口，以及各种关系。组件图显示组件以及它们之间的依赖关系，它可以用来显示程序代码如何分解成模块或组件。一般来说，组件就是一个实际文件，可以有以下几种类型：
  - 源代码组件：一个源代码文件或者与一个包对应的若干个源代码文件
  - 二进制组件：一个目标码文件，一个静态的或者动态的库文件
  - 可执行组件：在一台处理器上可运行的一个可执行的程序单位，即所谓可执行程序![image-20230215092612832](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302150926901.png)

# 十、配置图(部署图)

- [link](https://blog.csdn.net/litianxiang_kaola/article/details/53969021?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167642358116800222868448%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167642358116800222868448&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-2-53969021-null-null.142^v73^insert_down3,201^v4^add_ask,239^v1^insert_chatgpt&utm_term=%E9%83%A8%E7%BD%B2%E5%9B%BE&spm=1018.2226.3001.4187)

- 在UML中，部署图**显示了系统的硬件和安装在硬件上的软件**，以及用于连接异构计算机之间的中间件。**部署图通常被认为是一个网络图或者物理架构图**![image-20230215091753375](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202302150917455.png)

# 十一、比较关系

## 11.1 组件图和部署图

- 部署图和组件图是**面向对象系统**的建模的两种图
- 组件图是描述说明**组件(文件)之间的关系**，而部署图是描述系统**硬件的物理拓扑结构**及在此结构上执行的软件
