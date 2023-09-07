- buffer

  - 生成包含当前元素的buffer

    ```python
    def buffer(self, distance, resolution=16, quadsegs=None,
                   cap_style=CAP_STYLE.round, join_style=JOIN_STYLE.round,
                   mitre_limit=5.0, single_sided=False):
      
      distance: 包含当前元素的距离
      resolution: 顶点分辨率，使用默认值即可
      quadsegs：即将弃用
      cap_style：边界类型(圆/直线， 默认圆)
      join_style：连接类型
      
    ```

    