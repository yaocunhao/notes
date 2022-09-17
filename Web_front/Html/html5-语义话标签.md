# 一、没有义的标签

- div标签： 一行只显示一个（独占一行）
- span标签：一行可以显示多个
- 这两个标签的作用是做网页布局的,没有任何效果

```html
  <div>123</div>
  <div>456</div>

  <span>22222222</span>
  <span>22222222cccc</span>
```

![image-20220917211323881](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172113945.png)



#   二、有语义的布局标签(了解，手机端使用)

- 在HTML5 之中，退出了一些有语义的布局标签供开发者使用

| 标签名  | 语义       |
| ------- | ---------- |
| header  | 网页头部   |
| nav     | 网页导航   |
| footer  | 网页底部   |
| aslde   | 网页侧边栏 |
| section | 网页区块   |
| article | 网页文章   |

- 上面的标签显示特点和div一致(就是普通显示，没有任何效果)，但是比div多了不同的语义

```html
<header> 网页头部 </header>
  <nav> 网页导航 </nav>
  <footer>网页底部</footer>
  <aside>网页侧边栏</aside>
  <section>网页区块</section>
  <article>网页文章</article>
```

![image-20220917211834765](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172118852.png)