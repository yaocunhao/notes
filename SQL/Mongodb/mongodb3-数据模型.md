# 一、数据模型设计

- MongoDB 提供了两种数据模型，分别是嵌入式数据模型和规范化数据模型，您可以根据需要使用其中的任何一种

- 在 MongoDB 中模型设计需要注意以下几点：

  - 要根据具体的项目需求来选择合适的设计模式

  - 如果是要同时使用的数据，您可以将它们合并到一个文档中，否则可以选择将它们分成若干个文档

  - 可以有适当的数据冗余，因为与计算时间相比，磁盘空间更便宜

  - 针对最常用的用例优化您的模型

  - 写入时执行连接，而不是读取时执行连接

  - 在模式中执行复杂聚合

# 二、嵌入式数据模型

- **嵌入式数据模型也称为非规范化数据模型**，在该模型中您可以将所有相关的数据存储到一个文档中，例如我们在三个不同的文档中分别存储了一个员工的个人信息、联系方式和地址等信息，您还可以将这些信息整合到一个文档中，如下所示

  ```sql
  {
      _id: ObjectId("601f4be6e646844cd045c8a4"), # objectId 对象id用于创建模型
      Emp_ID: "10025AE336",
      Personal_details:{
          First_Name: "Radhika",
          Last_Name: "Sharma",
          Date_Of_Birth: "1995-09-26"
      },
      Contact: {
          e-mail: "biancheng.net@gmail.com",
          phone: "9848022338"
      },
      Address: {
          city: "Hyderabad",
          Area: "Madapur",
          State: "Telangana"
      }
  }
  ```

  

# 三、规范化数据模型

- 在规范化数据模型中，您可以通过**引用来将原始文档与子文档关联起来**，例如您可以将上面的文档信息以规范化数据模型重写为以下几个文档

  - 语法

    ```shell
    db.mt.insert({old:"637484f61c3a08a2e69d075a",name:123,sex:"girl",age:31231})
    
    old:这个字段的名称是随意取的
    ```
  
    
  
  - Employee
  
    ```
    {
        _id: ObjectId("601f4be6e646844cd045c8a4"),
        Emp_ID: "10025AE336"
    }
    ```
  
  - Personal_details

    ```sql
    {
        _id: ObjectId("601f50bae646844cd045c8a5"),
        empDocID: ObjectId("601f4be6e646844cd045c8a4"),# 引用文档
        First_Name: "Radhika",
        Last_Name: "Sharma",
        Date_Of_Birth: "1995-09-26"
    }
    ```
  
  - Contact
  
    ```
    {
        _id: ObjectId("601f50bae646844cd045c8a6"),
        empDocID: ObjectId("601f4be6e646844cd045c8a4"),
        e-mail: "biancheng.net@gmail.com",
        phone: "9848022338"
    }
    ```
  
  - Address
  
    ```
    {
        _id: ObjectId("601f50bae646844cd045c8a7"),
        empDocID: ObjectId("601f4be6e646844cd045c8a4"),
        city: "Hyderabad",
        Area: "Madapur",
        State: "Telangana"
    }
    ```