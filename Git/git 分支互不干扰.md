- [参考链接](https://blog.csdn.net/qq_37140632/article/details/85786089?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-85786089-blog-110071195.pc_relevant_antiscanv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-85786089-blog-110071195.pc_relevant_antiscanv3&utm_relevant_index=2)

- 想要分支之间互不干扰，有两种方式
  - git add、git commit 然后进行切换
  -  `git stash`隐藏当前工作现场，这个时候用 git status 查看工作区是干净的，所以就可以放心地去其他分支了。用 `git stash list`可以查看隐藏起来的工作现场

```
"guangzhou_anpei_close_field": {
            "id": 59,
            "name": "guangzhou_anpei_close_field",
            "tiles_id": 376,
            "order_index": 99,
            "status": 1,
            "region_id": 59,
            "utm_zone": "49Q",
            "gift_path_key": "guangzhou_anpei_close_field_V2",
            "max_zoom": 14,
            "native_zoom": 14,
            "top_zoom": 9,
            "zone_min_x": -983000,
            "zone_min_y": -1169000,
            "zone_max_x": 1098999,
            "zone_max_y": 1074999,
            "fs_module": "",
            "fs_name": "",
            "fs_version": 0,
            "summary": "",
            "region_min_x": 113000,
            "region_min_y": 333000,
            "region_max_x": 118999,
            "region_max_y": 340999,
        }
```

