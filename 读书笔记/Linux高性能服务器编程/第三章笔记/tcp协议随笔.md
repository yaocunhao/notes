# 一、tcp服务的特点

- 面向链接： 双方要进行连接
- 面向字节流： 数据是以字节流的形式进行交互，因此写和读的次数不一定一致
- 可靠传输：通过超时重传机制、序列号等等



# 二、半关闭状态

- tcp是全双工的，假如客户端调用close关闭链接之后，说明客户端不再发送数据，但是可以继续进行数据的接收，直到对方对方也发送结束报文用以关闭链接







# 一、疑问点

## 1.1tcp会丢包么

- [link](https://blog.csdn.net/m0_71777195/article/details/126121761?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168131775116800182719572%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=168131775116800182719572&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-126121761-null-null.142^v83^insert_down1,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=tcp%E4%B8%80%E5%AE%9A%E4%B8%8D%E4%BC%9A%E4%B8%A2%E5%A4%B1%E6%95%B0%E6%8D%AE%E5%90%97&spm=1018.2226.3001.4187)

- 比如我全部写入到缓冲区，，服务器怎么知道我写了多少？丢包了怎么处理？
  - tcp提供了应答、超时重传、序列号等机制来保证数据一定能够接收到
- TCP作为流式传输协议，只能保证发送和接收的数据，顺序是一致的，而无法保证send和[recv](https://so.csdn.net/so/search?q=recv&spm=1001.2101.3001.7020)时返回的长度是一致的，send的数据有可能是多条数据的合并，也可能是大数据的分包，那么如何解决这个问题？
  - tcp能够保证数据安全的到达服务器，但是这个数据是否完整则需要应用层来进行处理。比如常见的http协议
- 如果一直不读tcp缓存区中的数据会发生什么
  - 缓存区满了之后，内核会采取一定的处理策略来释放空间
  - [link](https://blog.csdn.net/qq_29757283/article/details/85053014?ops_request_misc=&request_id=&biz_id=102&utm_term=%E5%86%85%E6%A0%B8%E7%BC%93%E5%AD%98%E5%8C%BA%E6%BB%A1%E4%BA%86&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-85053014.142^v83^insert_down1,201^v4^add_ask,239^v2^insert_chatgpt&spm=1018.2226.3001.4187)

