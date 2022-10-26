# 一、select 接口介绍

## 1.1 超时时间

- 用来设置select 函数的超时时间，使用指针是因为内核可以修改它，告诉应用程序等待了多久。但是不能完全相信这个值，**调用失败时，timeout的值是不确定的**
- 两个值都传递0，select函数立即返回，如果timeout传递NULL，select 将一直阻塞，知道某个文件描述符准备就绪

```c++
// __time_t 和 __suseconds_t 都是长整形long int 宏
struct timeval
  {
    __time_t tv_sec;		/* Seconds.  */
    __suseconds_t tv_usec;	/* Microseconds.  */
  };

```

## 1.2 fd_set 结构体

- 该结构体用来“装载”事件的集合

  ```c++
  // 下述代码根据内核抽简而来
  
  typedef long int __fd_mask;
  #define __FD_SETSIZE 1024
  #define __NFDBITS (8 * (int)sizeof(__fd_mask))
  
  typedef struct
  {
    __fd_mask fds_bits[__FD_SETSIZE / __NFDBITS];
  } fd_set;
  
  #define PRINT(str) printf(#str)
  
  int main()
  {
    printf("%d\n", __FD_SETSIZE / __NFDBITS); // 16
    printf("%d\n", 16 * 8 * 8); // 1024: 数组大小为16，长整形8字节，每个字节8比特位，所以最后位图的大小为1024
     return 0;
  }
  
  ```

- 位图的大小为1024，决定的一次性最多监听1024个文件描述符

- 对位图设置标识监听的文件描述符是什么，由于位图操作繁琐，所以提供了下列的宏函数API进行操作

  ```c++
  #include <sys/select.h>
  FD_ZERO(fd_set *fdset) // 清除fd_set 所有的位
  FD_SET(int fd, fd_set *fdset) // 设置fdset 的位fd
  FD_CLR(int fd, fd_set *fdset) // 清除位图的位fd 
  int FD_ISSET(int fd , fd_set *fdset) // 测试fdset的位fd是否被设置  
  ```

  

##  1.3 select 函数

- select 成功返回文件描述符总数，超时返回0，失败返回-1，并设置errno
- 如果select等待期间程序收到信号，select返回-1，并设置errno为EINTR

```c++

// nfds:被监听文件描述符总数，被监听的最大文件描述符+1
// __restrict 提高效率的宏
//__readfds：可读事件集合对应的文件描述符集合
// __writefds：可写事件集合
// __exceptfds： 异常集合
// __timeout： 超时事件设置

select (int __nfds, fd_set *__restrict __readfds,
		   fd_set *__restrict __writefds,
		   fd_set *__restrict __exceptfds,
		   struct timeval *__restrict __timeout);
```

