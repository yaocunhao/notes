[基本概念连接](https://blog.csdn.net/pangjunwei/article/details/106901927?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106901927-blog-81094378.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106901927-blog-81094378.pc_relevant_antiscanv2&utm_relevant_index=1)

- 创建一个用户，用户的权限对应的是数据库

- 用户验证 db.auth('user','passwd')

- [docker之中进行操作](https://blog.csdn.net/u012017645/article/details/104694493?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165771085816781667846564%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165771085816781667846564&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-104694493-null-null.142^v32^pc_rank_34,185^v2^control&utm_term=mongo%20%E6%9D%83%E9%99%90%E8%AE%A4%E8%AF%81&spm=1018.2226.3001.4187)

# 一、基本概念

## 1.1  认证的开启

- mongod数据库服务默认是不开启用户认证的，此时任何的client端都可以连接并访问mongo服务，只有是网络可以连通即可
- MongoDB存储所有的用户信息在admin 数据库的集合system.users中，保存用户名、密码和数据库信息。创建管理用户的的步骤就是先第一次没有认证启动服务，创建管理账号，第二次才认证启动服务。
- 如果需要支持用户认证的功能，则必须要先主动开启该功能，主要的方式就是在启动命令的时候添加一个--auth参数即可，如：
  `/usr/bin/mongod --config /etc/mongodb.conf --auth`
- 通过上述命令启动的mongo服务就会支持认证机制，client连接时如果没有带上正确的用户名和密码的话则会报错

## 1.2 认证的方式

- `mongo --host --port `登录之中再`db.auth()` 进行认证
- `mongo --host --port -u -p --authenticationDatabase` 直接验证到指定的数据库

## 1.3 角色和权限

- MongoDB使用的是基于**角色(也就是不同的权限)**的访问控制(Role-Based Access Control,RBAC)来管理用户对实例的访问。通过对用户授予一个或多个角色来控制用户访问数据库资源的权限和数据库操作的权限，在对用户分配角色之前，用户无法访问实例
- MongoDB存储所有的用户信息在admin 数据库的集合system.users中，保存用户名、密码和数据库信息。创建管理用户的的步骤就是先第一次没有认证启动服务，创建管理账号，第二次才认证启动服务
- 在实例启动时添加选项–auth或指定启动配置文件中添加选项auth=true

- 角色
  - mongodb之中通过**角色(也就是不同的权限名称)**对用户授予对应的数据库操作权限，每个角色中的权限可以显示的指定，也可以通过继承其它角色的权限，或者两者都存在的权限
  - 在角色定义时，可以包含一个或多个已存在的角色，新创建的角色会继承包含的角色的所有权限。在同一个数据中新创建的角色可以继承其它角色的权限，在admin数据库之中创建的角色可以继承在其它任意数据库中角色的权限
- 权限
  - 权限由指定的数据库资源(resource)以及允许在指定资源上进行的操作(action)组成
    - 资源包括：数据库、集合、部分集合、集群
    - 操作包括：对资源进行的增删查改

# 二、角色分类

[参考链接](https://blog.csdn.net/Xixo0628/article/details/107020003?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166036951416782350896256%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166036951416782350896256&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-1-107020003-null-null.142^v40^pc_rank_34_2,185^v2^control&utm_term=mongodb%20%E6%96%B0%E9%94%AE%E8%A7%92%E8%89%B2%E6%9D%83%E9%99%90%E7%BB%A7%E6%89%BF&spm=1018.2226.3001.4187)

- 用户和权限创建

  - 创建管理员需要切换到admin库

    ```python
    # 超级管理员创建
    use admin
    db.createUser({user:"ych", pwd:"1234",roles:["root"]})
    
    # 查看用户信息
    show users
    
    # 删除用户
    # db.dropUser("ych")
    ```

- 角色分类

  - 数据库用户角色

  | 权限名               | 描述                                                      |
  | -------------------- | --------------------------------------------------------- |
  | read                 | 允许用户读取指定数据库的数据                              |
  | readWrite            | 允许用户读写指定数据库的数据                              |
  | readAnyDatabase      | 读取任何数据库的数据(config和local除外)                   |
  | readWriteAnyDatabase | 读写任何数据库(config和local除外)                         |
  | userAdminAnyDatabase | 可以在指定数据库中创建和修改用户(config和local除外)       |
  | dbAdminAnyDatabase   | 可以操作任何数据库                                        |
  | dbAdmin              | 可以操作指定数据库                                        |
  | userAdmin            | 在指定数据库中创建和修改用户                              |
  | backup               | 备份MongoDb数据中的最小权限                               |
  | restore              | 从备份文件中还原恢复MongoDb数据(除了system.profile)的权限 |
  | root                 | 超级用户、超级权限                                        |

  - 系统内置角色
    - MongoDB内部提供了一系列内置的角色，这些角色是为了完成一些基本的数据库操作。每个内置角色提供了用户在角色数据库内数据库级别所有非系统类集合的访问权限，也提供了对集合级别所有系统集合的访问权限。MongoDB在每个数据库上都提供内置的数据库用户角色和数据库管理角色，但只在admin数据库中提供其它的内置角色
    - 内置角色主要包括以下几个类别：
      - 数据库用户角色(Database User Roles)
      - 数据库管理角色(Database Administration Roles)
      - 集群管理角色(Cluster Administration Roles)
      - 备份和恢复角色(Backup and Restoration Roles)
      - 全数据库级角色(All-Database Roles)
      - 超级用户角色(Superuser Roles)
      - 内部角色(Internal Role)
  - 用户自定义角色
    - 虽然MongoDB提供了一系列内置角色，但有时内置角色所包含的权限并不满足所有需求，所以MongoDB也提供了创建自定义角色的方法。当创建一个自定义角色时需要进入指定数据库进行操作，因为MongoDB通过数据库和角色名称对角色进行唯一标识。
      除了在admin数据库中创建的角色之外，在其它数据库中创建的自定义角色包含的权限只适用于角色所在的数据库，并且只能继承同数据库其它角色的权限。在admin数据库中创建的自定义角色则不受此限制
    - MongoDB将所有的角色信息存储在admin数据库的system.roles集合中，不建议直接访问此集合内容，而是通过角色管理命令来查看和编辑自定义角色。
      用户具有创建自定义角色、查看自定义角色、更新自定义角色、添加角色权限、删除角色权限、添加角色集成的角色、删除角色继承的角色、删除自定义角色等操作权限。具体可以参考[官方文档链接](https://docs.mongodb.com/manual/reference/command/nav-role-management/)
      

- 角色权限查看

  ```
  # 查询数据库中的角色权限
  > db.runCommand({rolesInfo:"read"})
  {
  	"roles" : [
  		{
  			"role" : "read",
  			"db" : "admin",
  			"isBuiltin" : true,
  			"roles" : [ ],
  			"inheritedRoles" : [ ]
  		}
  	],
  	"ok" : 1
  }
  
  # 查询其它数据库中的角色权限
   db.runCommand({ rolesInfo: { role: "<rolename>", db: "<database>" } }
  
  # 查新多个角色权限(仅用户自定义角色)
  db.runCommand({ rolesInfo: 1 })
  
  # 查询所有角色权限(包含内置角色)
  db.runCommand({ rolesInfo: 1, showBuiltinRoles: true })
   
  ```

  



