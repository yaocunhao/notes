- [link](https://www.runoob.com/css/css-margin.html)
- `margin:0 auto` 无效
  - 给父元素设置这类margin 需要子元素是[块级元素](https://so.csdn.net/so/search?q=块级元素&spm=1001.2101.3001.7020) displayinline/inlineblock都不行
  - 子元素如果是[块元素](https://so.csdn.net/so/search?q=块元素&spm=1001.2101.3001.7020) 则可能是没有设置宽度 块级元素默认宽度是屏幕宽度 所以没有宽度会导致无效
- **margin:auto为什么不垂直居中**
  - margin:auto是具有强烈计算意味的关键字，用来计算元素对应方向上应该获得的剩余空间大小
  - [行内元素](https://so.csdn.net/so/search?q=行内元素&spm=1001.2101.3001.7020)margin:auto; 不能水平居中在一行的中央位置（行内元素不独占一行)
  - [块级元素](https://so.csdn.net/so/search?q=块级元素&spm=1001.2101.3001.7020)设置宽度后仍占据一行空间，因此margin:auto;会将这一行的剩余空间平均分配给左右外边距
  - margin:auto 能使块级元素水平居中，但是不能[垂直居中](https://so.csdn.net/so/search?q=垂直居中&spm=1001.2101.3001.7020)，因为垂直方向上默认没有剩余的空间

