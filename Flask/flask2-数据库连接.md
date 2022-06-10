# 报错处理

- ModuleNotFoundError: No module named 'MySQLdb'
  - [处理方式](https://blog.csdn.net/qq_25046261/article/details/78991442?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165459354016782248563180%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165459354016782248563180&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-78991442-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=flask+ModuleNotFoundError%3A+No+module+named+MySQLdb&spm=1018.2226.3001.4187)

- 映射关系

  - **SQLAlchemy**的**Session**对象管理**ORM**对象的所有持久性操作。

    以下session方法执行CRUD操作：

    - **db.session.add** (模型对象) - 将记录插入到映射表中

    - **db.session.delete** (模型对象) - 从表中删除记录

    - **model.Query.all()** - 从表中检索所有记录（对应于SELECT查询）

      - 您可以通过使用filter属性将过滤器应用于检索到的记录集。例如，要在学生表中检索**city ='Hyderabad'**的记录，请使用以下语句：

        ```python
        Students.query.filter_by(city = ’Hyderabad’).all()
        ```

        

