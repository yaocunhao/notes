- exists
  - 当 sql 语句中出现 in 或 not in 时， 用 exists 或 not exists 代替
  - exits 的用法是 **select 字段1，字段2 from TableA where exits (select 字段 from TableB where 条件)** 
  - exits 后面的语句返回的是一个 Boolean 类型的数据。如果exits 返回 true， 那么就从 TableA 查出符合条件的数据

- 一条SQL的查询结果作为另一条SQL的查询条件

  - 如果是单个值(这个的数量是只子查询的结果数量)

    ```
    where 字段 = 子查询
    ```

  - 如果是多个值

    ```
    where 字段 in (子查询)
    ```

  - 如果是多个字段

    ```
    where (字段1,字段2...字段n) = (子查询)
    ```

    

- 删除唯一键的时候找不到键名，是因为使用的是别名
  - 删除语法 alter table ` t_name` drop index `key_name`