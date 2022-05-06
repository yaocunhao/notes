# 一、通用接口

- 连接

  - `redis.Redis`

- 查询所有key

  - keys('*')

- 查看键的类型

  - type('key')

- 判断键是否存在

  - exists('key')

- 键的删除

  - delete('key')

- 演示

  ```python
  import redis
  
  # 本地连接，创建数据库连接对象
  r = redis.Redis(host='127.0.0.1',port=6379,db=0)
  
  key_list = r.keys('*') # 获取所有的键,返回值list，里面的键的类型是 bytes
  for key in key_list:
    print(key.decode('utf8'))
  
  print(r.type('k')) # 查看k1键的类型 b'string'
  print(r.exists('k')) # 判断键是否存在 1
  r.delete('k') # 进行键的删除
  print(r.exists('k')) # 0
  ```

# 二、字符串

```python
r = redis.Redis(host='127.0.0.1',port=6379,db=0)
# 单个设置与读取
r.set('hx','chenghaonan')
print(r.get('hx'))

# 批量设置与读取
r.mset({'name':'tom','age':12})
print(r.mget('name','age'))

# 查看对应的值的长度
print(r.strlen('name'))

# 数值操作
r.set('num',10)

# 加减
r.incrby('num',1) # +1
r.decrby('num',2) # -2
# 自增自减
r.incr('num') # ++
r.decr('num') # --
# 浮点数据加减
r.incrbyfloat('num','1.22') # 浮点数加法

print(r.get('num').decode()) # 10.22

```

# 三、list

```python
r = redis.Redis(host='127.0.0.1',port=6379,db=0)
r.lpush('list',1,2,3,4,5,6) #头插
r.linsert('list','before',3,2.5) # 3元素前面插入2.5
print(r.llen('list')) # 获取长度:7
print(r.lrange('list',2,5)) # 获取范围内的元素:[b'4', b'2.5', b'3', b'2']
print(r.rpop('list')) # 从尾部弹出一个元素:b'1'

print(r.ltrim('list',0,4)) # 保留范围内的元素:True
print(r.lrange('list',0,r.llen('list')-1)) # 打印所有元素：[b'6', b'5', b'4', b'2.5', b'3']
while True:
  result = r.brpop('list',1) # 用于删除&&返回列表最后一个元素，timeout=1
  if result:
    print(result)
  else:
    break
  #(b'list', b'3')
  #(b'list', b'2.5')
  #(b'list', b'4')
  #(b'list', b'5')
  #(b'list', b'6')
```

# 四、散列

```python
r = redis.Redis(host='127.0.0.1',port=6379,db=0)

# 1、更新一条数据的value，若不存在时，则新建这条数据
r.hset('phk','id:1','zhangsan')
# 2、读取数据的指定字段属性，返回值为字符串类型
print(r.hget('phk','id:1'))
# 3、批量更新字段属性,参数mapping为字典类型
dic = {'id:2':'jim','id:3':'wangwu'}
r.hmset('phk',dic)
# 4、批量读取数据的字段属性
print(r.hmget('phk','id:1',"id:3"))
# 5、获取这条数据的所有属性字段和对应的值，返回值为字典类型
print(r.hgetall('phk'))
# 6、获取这条数据的所有属性字段，返回值为列表类型
print(r.hvals('phk'))
# 7、删除这条数据的指定字段
r.hdel('phk','id:1')
print(r.hkeys('phk'))
```

# 五、集合

```python
# 1、给name对应的集合中添加元素
r.sadd('name','zhangsan','lisi','wangwu')
r.sadd('name2','lisi','zhangsan','tianqi')
#2、获取name对应的集合的所有成员:{b'wangwu', b'lisi', b'zhangsan'}
print(r.smembers('name'))
#3、获取name对应的集合中的元素个数:3
print(r.scard('name'))
#4、检查value是否是name对应的集合内的元素，返回值为True或False:T、F
print(r.sismember('name','lisi'))
print(r.sismember('name','tianqi'))
#5、随机删除并返回指定集合的一个元素:zhangsan
print(r.spop('name'))
#6、删除集合中的某个元素:
r.srem('name','wangwu') 
#7、获取多个name对应集合的交集:{b'lisi'}
print(r.sinter('name','name2'))
#8、获取多个name对应的集合的并集:{b'tianqi', b'lisi', b'zhangsan'}
print(r.sunion('name','name2'))
```

