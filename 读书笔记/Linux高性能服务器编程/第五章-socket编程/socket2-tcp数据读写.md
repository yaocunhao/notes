# 一、接口

- ```c++
  #include <sys/types.h>
  #include <sys/socket.h>
  
  ssize_t recv(int sockfd, void *buf, size_t len, int flags);
  ssize_t send(int sockfd, const void *buf, size_t len, int flags);
  ```

  