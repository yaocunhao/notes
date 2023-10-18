- [参考链接](https://blog.csdn.net/weixin_30333885/article/details/98934524?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-98934524-blog-106607698.pc_relevant_3mothn_strategy_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-98934524-blog-106607698.pc_relevant_3mothn_strategy_recovery&utm_relevant_index=9)

  - 设置日志等级 `db.setLogLevel(-1, "query")`。 这里表示设置与查询相关的日志

- [gd](https://www.mongodb.com/docs/manual/reference/log-messages/)

  - 正常情况之下看不到详细的命令日志，需要将对应的日志等级设置为调试级别才可以看到命令(正常情况下为-1，即继承父类等级)
  - 调试等级为 1-5， 如果设置日志等级为5则表示获得最详细的日志

- 日志格式

  ```json
  {
    // 日期
    "t": {
      "$date": "2020-05-01T15:16:17.180+00:00"
    },
    
    // 日志等级
    "s": "I",
    // 表示日志所属类别
    "c": "NETWORK",
    "id": 12345,
    "ctx": "listener",
    "msg": "Listening on",
    "attr": {
      "address": "127.0.0.1"
    }
  }
  ```

  - 设置日志等级

    ```shell
    # 1、第一个参数表示设置的等级
    db.setLogLevel(-1, "query")
    
    # 2、第二个参数表示设置那种属性的日志
    
    ```

    