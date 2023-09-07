- [link](https://www.ngui.cc/article/show-1259337.html?action=onClick)
  - 自定义属性（CSS 变量）的格式为：以双减号（–）开头，后跟变量的名称，然后是一个冒号（:），最后是变量的值
  - 在使用自定义属性时，可以通过 `var()` 函数来引用它们
  - 使用自定义属性可以使你的样式表更加灵活和可维护。可以在需要的地方更改自定义属性的值，而无需修改每个使用该属性的样式规则

- 示例

  ```css
  /* :root 选择器表示文档的根元素，通常是 <html> 元素。在这个选择器下定义的变量可以在整个文档中使用 */
  :root {
    --line-border-fill: #3498db;
    --line-border-empty: #383838;
  
  }
  
  通过定义这些自定义属性，可以在其他 CSS 规则中使用它们，例如：
  .line {border: 1px solid var(--line-border-fill);/* 其他样式规则 */
  }
  
  ```

  