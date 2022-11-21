# 前言

- tcp使用的数据传输接口为send，和recv
- recvfrom、 sendto 一般只用于udp通信，因为udp需要手动带上自己和对方的地址，所以不能使用tcp的接口。但是这个接口tcp也可以使用
- recvmsg、sendmsg。tcp，udp通用接口，遇到时再回来细看即可

# 一、接口

- ```c++
  #include <sys/types.h>
  #include <sys/socket.h>
  
  ssize_t recv(int sockfd, void *buf, size_t len, int flags);
  ssize_t send(int sockfd, const void *buf, size_t len, int flags);
  ```

  

# 二、socket 选项

