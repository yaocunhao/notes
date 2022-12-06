# 一、参考链接

- [坐标转换](https://www.jianshu.com/p/09386f3f3d92)
- [精度问题](https://blog.csdn.net/feinifi/article/details/120547127?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-0-120547127-blog-121388429.pc_relevant_landingrelevant&spm=1001.2101.3001.4242.1&utm_relevant_index=3)
  - 需要反复迭代多次计算得到一个很小的偏差值，但是过程会非常耗时



# 二、基本概念

-   WGS84是大地坐标系，这是一种国际通用的坐标，中国的大地坐标是CGCS2000，也是原始坐标系，一般的终端设备，如果自己有定位功能，比如车载系统，他们发出的gps坐标就是原始坐标，我们在一些地图应用中使用的定位，比如打车软件，其他自带地图的软件，他们一般都是火星坐标系GCJ02，需要进行一个坐标转换。而百度地图相关的应用，基本都是使用的百度坐标，这个是在火星坐标基础上进行的再次加密，所以它还是与火星坐标有关系

- 相互转换
  - `utm` 可以通过python包直接转成 `wgs84`
  - `wgs84` 可以和 `gcj02`进行相互转换