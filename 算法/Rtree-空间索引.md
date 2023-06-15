# 一、资料收集

- [python使用链接](https://blog.csdn.net/junqing_wu/article/details/106605158)
  - [rtree官方文档](https://rtree.readthedocs.io/en/latest/tutorial.html)
  - [可能发生的问题](https://blog.csdn.net/a289237642/article/details/83386255?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-83386255-blog-106605158.235^v36^pc_relevant_default_base3&spm=1001.2101.3001.4242.1&utm_relevant_index=3)
    - 　　天啊！一样的代码，为何结果不一样
    - 　　创建索引的时候传入了文件名，因此会以追加的形式打开，而不是创建一个新的索引文件
- [github](https://github.com/Toblerity/rtree)
- [什么是R树-概念](https://blog.csdn.net/zhongkeyuanchongqing/article/details/118229271?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168492868316782427485008%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168492868316782427485008&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-118229271-null-null.142^v87^control_2,239^v2^insert_chatgpt&utm_term=%E4%BB%80%E4%B9%88%E6%98%AFr%E6%A0%91&spm=1018.2226.3001.4187)
- [什么是R树-C++代码](https://blog.csdn.net/shenmingxueIT/article/details/122752700?ops_request_misc=&request_id=&biz_id=102&utm_term=r%E6%A0%91%E7%9A%84%E4%B8%80%E4%BA%9B%E5%88%92%E5%88%86%E8%A7%84%E5%88%99&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-1-122752700.nonecase&spm=1018.2226.3001.4450)
- 最终参考链接
  - [总体概括](https://www.cnblogs.com/chentianwei/p/11712079.html)
  - [深度分析探索](https://www.cnblogs.com/cmi-sh-love/p/kong-jian-shud-ju-suo-yinRTree-wan-quan-jie-xi-jiJa.html) 
  - [优先看_1](https://blog.csdn.net/xiaofengcanyuexj/article/details/41912169)
  - [优先看_2](https://www.likecs.com/show-308434176.html)
- [B树和Mysql](https://juejin.cn/post/7117516433386373133)
  - 每一层都只获取一个节点，所以读一次即可
  - 局部性原理与磁盘预读，预读的长度一般为页（page）的整倍数（根据操作系统的不同，操作系统有的页大小为4k，有的为16k）。**其中MySQL B+树中的 叶/非叶节点 都是以MySQL的页为单位（大小通常也为16k），存放完整行记录**
  - 数据库系统巧妙利用了磁盘预读原理，将一个节点大小设为操作系统内存页的整数倍（4倍），这样每个节点只需要一次I/O就可以完全载入
  - 猜测Mysql设计：每个节点的数据都有一个文件对应的索引，然后保存在磁盘之中。也就是说多少个节点就多少个文件。每次加载一个文件，然后根据索引去找对应的子节点所在的文件即可


# 二、python使用

- pip install RTree
  - 最近邻搜索 
  - 十字路口的搜索 
  - 多维索引 
  - 聚集索引(直接用索引项存储Python pickle) 
  - 批量加载
  -  删除 
  - 磁盘序列化 
  - 自定义存储实现(例如，在ZODB中实现空间索引)
- 代码演示

```python
from rtree import index
# 主要使用 rtree.index.Index 和 rtree.index.Property . 用户操纵这些类与索引交互。

# 构建 RTree 实例对象
idx = index.Index()

# 插入索引 insert(id, coordinates, obj=None):
# 其中coordinates为坐标顺序,按照 [xmin, xmax, ymin, ymax，…,……、kmin kmax]
left, bottom, right, top = (0.0, 0.0, 1.0, 1.0)
idx.insert(100, (left, bottom, right, top))

# 查询索引
# 查询索引有三种主要方法。:
# 1. 交叉  rtree.index.Index.intersection() 给定一个窗口，返回包含该窗口的ID
data = list(idx.intersection((1.0, 1.0, 2.0, 2.0)))
print(data)

# # 2. 最近邻
# # 给定界限最近的1个项。如果多个项目与边界的距离相等，则返回两个项目，自定义想要寻找的最近邻个数
data2 = list(idx.nearest((1.0000001, 1.0000001, 2.0, 2.0), 1))
print(data2)

```

- 构造函数

  ```python
  # 1、采用默认的构造函数
  idx = index.Index() 
  # 2、索引将存储到xx或者从xx读取索引
  idx = index.Index('x')
  ```

- 属性更改

  ```python
  # rtree.index.Property.idx_extension 
  ```

- 杂记

  ```python
  1、index.index() 相当于定义了一颗介数为M的树(库中默认为100，阶数表示一个节点拥有的最多索引数量)
  
  2、index.insert() 其实就是往节点之中插入关键字(R树之中称关键字为条目)，一开始一个节点，随着插入不断分裂变多
  
  3、这里节点的分裂与合并，由原来的比较某个值的大小，变成比较空间范围的大小
  ```

  - rtree使用的就是B+树，只是将其称之为B树

# 三、杂记

## 3.1 B- 和 B+ 树、聚族和非聚族

- B+ 树叶子节点才命中。B-树 非叶子节点命中
- 聚族：树的节点中存储着数据。非聚族：数的节点中存储的是地址，根据地址再去进行数据的读取

## 3.2 一棵树的阶定义多少合适

- 首先需要知道为什么需要B树： 如果采用普通的二叉树，高度会急剧增加，每一层需要访问一次磁盘进行数据的读取，这样就会增加IO的次数。因此，B树的出现使得二叉树的高度更低，IO的次数更少，速度上自然更高。而一层的数量太多，如果数据量非常大的话，对内存也是比较高的消耗，因此M一般定义在比较合适的数量级

- 在一个典型的B树应用中，要处理的硬盘数据量很大，因此无法一次全部装入内存。因此我们会对B树进行调整，**使得B树的阶数（或结点的元素）与硬盘存储的页面大小相匹配**
  - <font color=yellow>假设数据库中一行的最大值为max，那么一个节点最多拥有n个max元素，max的大小为size(所以Mysql会限制索引的大小)，最恰当的是 n×max×size=硬盘页面大小，这样每次正好读一个页面(详情看链接收集：B树和Mysql)</font>。每一层只需要读一个节点就可以判断下一层是读取哪一个节点>
  - 所以，Mysql限制索引的最大值，然后根据硬盘的页，反推出一颗树的阶树是多少[推荐文章](https://juejin.cn/post/6844903582731337742)

## 3.3 节点存储的是什么

- [查找过程参考](https://blog.csdn.net/xiaofengcanyuexj/article/details/41912169)

![image-20230526005229322](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202305260052669.png)

![image-20230526005813885](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202305260058978.png)

## 3.4 引申用法

- 查找一个点周围的元素
  - 以该点为圆心，R为半径，画一个圆圈，即可得到索引范围(具体待详细研究。 Mysql官档之中搜索Geometry即可)
