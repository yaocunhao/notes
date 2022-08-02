- 会自动读取。env内容
- 验证会链接不上







# 权限问题

- 在使用docker-compose 启动mongo时，如果如下进行配置文件设置，那么在容器启动的同时会设置一个名为root的超级用户

  ```python
    mongo:
      image: mongo:4.4
      container_name: authority_mongo
      restart: always
      command: --config /etc/mongo/mongod.conf
      ports:
        - "3232:3232"
      networks:
        - default
      volumes:
        - ./mongod.conf:/etc/mongo/mongod.conf
        - ${DATA_DIR_1}/mongodb:/data/db/
        - ${LOG_DIR_1}/mongodb:/var/log/mongodb
          #environment:
              #MONGO_INITDB_ROOT_USERNAME: root  #设置的环境变量
              #MONGO_INITDB_ROOT_PASSWORD: didi1234
  
  ```

- 因此，当外界使用超级用户的名称和密码来进行连接时，只有操作admin库的权限，而没有操控其它库的权限