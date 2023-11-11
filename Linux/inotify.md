- [详细解释link](https://www.markjour.com/article/cannot-add-inotify-watch.html)

  ```
  查看 inotify 的配置
  $ sysctl fs.inotify
  fs.inotify.max_queued_events = 16384
  fs.inotify.max_user_instances = 128
  fs.inotify.max_user_watches = 65536
  
  临时解决问题（关机之后又是原来的设置）：
  sudo sysctl -w fs.inotify.max_user_watches=524288
  ```

  

- [临时设置](https://blog.csdn.net/taxuebufeng/article/details/132101224)

  - 虽然524,288是可以观看的最大文件数，但如果您处于特别受内存限制的环境中，您可能希望降低该数量。每个文件监视占用540字节（32位）或1kB（64位），因此假设所有524,288个[句柄](https://so.csdn.net/so/search?q=句柄&spm=1001.2101.3001.7020)都被消耗，上限约为256MB（32位）或512MB（64位）。