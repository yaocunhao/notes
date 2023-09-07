- [link](https://blog.csdn.net/Superman_peng/article/details/114779320)

- 分析；如果目标元素的高度（**或**宽度）只由其内容、padding、border确定的情况下，则会存在失效情况。
- 解决方法：对目标元素设置固定高度（**或**宽度）
- 注：宽度或高度设置为百分比也会失效
- 场景：box-size 对h标签是会失效的，因此需要手动控制margi