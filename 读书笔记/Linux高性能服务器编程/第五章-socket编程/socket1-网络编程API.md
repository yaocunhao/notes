# 一、API 所在位置

- Socket Api  主要所在位置是`sys/socket.h`
- Linux 实现了一套网络信息Api，以实现主机名和IP地址之间的转换，以及服务名称和端口号之间的转换。这些Api都定义在`netdb.h`之中

# 二、大小端转换

- 网络传输的数据需要转换成网络字节序(大端字节序)

- Linux主要提供了如下4个函数来完成主机字节序(小端)和网络字节序之间的转换

  ```
   #include <arpa/inet.h>
  
         uint32_t htonl(uint32_t hostlong);
  
         uint16_t htons(uint16_t hostshort);
  
         uint32_t ntohl(uint32_t netlong);
  
         uint16_t ntohs(uint16_t netshort);
  ```

  

# 三、socket 地址

- https://boardmix.cn/app/editor/eznQVCfWpzw1jk_Cy6NkGg



# 四、ip地址 转换函数

- 通常使用点分十进制的字符串来表示ipv4 地址，十六进制来表示ipv6地址。然而编程需要转换成整数(二进制)。下面三个函数用于点分十进制表示的ipv4 地址和网络字节整数表示地址之间的转换

  ```C++
  #include <sys/socket.h>
  #include <netinet/in.h>
  #include <arpa/inet.h>
  
  in_addr_t inet_addr(const char *cp);
  // 字符串-> 网络字节序整数表示的ipv4， 失败返回INADDR_NONE
  
  int inet_aton(const char *cp, struct in_addr *inp);
  // 和上述作用一样，只是将转换结果存储与inp指向的结构体之中，成功返回1，失败返回0
  
  
  char *inet_ntoa(struct in_addr in);
  // 用于将地址转换成点分十进制 
         
  ```

  - **注意：aton 由于函数内部使用一个静态变量存储转换结果，函数的返回值指向静态内存，因此不是线程安全的，因为多次调用会导致同一块内存被覆盖掉**

# 五、socket 创建

