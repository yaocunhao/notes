# 一、几何之间的关系

```python
def t_linearRing():
  """测试linearRing"""
  ring_1 = LinearRing([(0, 0), (2, 0), (2, 2), (0, 2)])
  ring_2 = LinearRing([(1, 0), (1.5, 0), (2, 2), (1.2, 1)])
  ring_3 = LinearRing([(1, 1), (1.5, 1), (1.5, 1.5), (1, 1.5)])
  point = Point(2, 2)
  line = LineString([(1.5, 1.5), (2, 2)])

  # 2、contains： 构成other的点是否能够在当前对象中都能够找到，并且要求other至少有一个点是内部点，即不能是线段的边界点
  # 线段的边界属于线段的boundary而不是内部
  #print(ring_1.contains(point)) # True
  #print(line.contains(point))   # False

  # 3、covers：和constains不同的地方在于可以是边界点
  #print(line.covers(point)) # True

  # 4、crosses：两个图形相交串过,但是不会包含(交集&&不是属于关系)
  # 直线不会与属于它的点相交
  #print(line.crosses(point))  # false
  #print(ring_3.crosses(ring_2))  # true

  # 5、disjoint： 不相交返回true
  # print(ring_1.disjoint(ring_3))

  # 6、intersects： 有交点就相交，并不需要想crosses一样两个对象相互穿过
  # print(line.intersects(point)) # True

  # overlaps：部分重叠
  # 1、必须是相同的类型才可以重叠
  # 2、重叠的部分的类型必须和原来的类型是一样的
  #  a、比如闭合线与闭合线重叠的部分必须是闭合线
  #  b、区域与区域重叠的部分必须是区域
  # print(ring_2.overlaps(ring_3))
```



# 二、自身属性判断

- is_ring
  -  如果特征是一个封闭的简单的' LineString '则返回' True '
  -  此属性适用于LineString和LinearRing实例，但对其他实例没有意义
- is_simple
  -  如果特性本身没有交叉，则返回True
  - 简单性测试只对linestring和LinearRings有意义