- [link](https://blog.csdn.net/m0_57515995/article/details/125363673?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169762640916800211579012%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169762640916800211579012&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-125363673-null-null.142^v96^pc_search_result_base1&utm_term=linux%20ssh&spm=1018.2226.3001.4187)

- 如何免密登录

  ```
  1、在本地执行 ssh-keygen 生成公钥和私钥
  2、将公钥 id_rsa.pub 拷贝至目标服务器的 .ssh/authorized_keys 之中
  ```

  - 免密登录原理

    ```
    基于密钥的认证（publickey认证）：client产生一对公共密钥，将公钥保存到将要登录的server上的那个账号的家目录的.ssh/authorized_keys文件中。认证阶段：client首先将公钥传给server端。server端收到公钥后会与本地该账号家目录下的authorized_keys中的公钥进行对比，如果不相同，则认证失败；否则server生成一段随机字符串，并先后用client公钥和会话密钥对其加密，发送给client。client收到后将解密后的随机字符串用会话密钥发送给server。如果发回的字符串与server端之前生成的一样，则认证通过，否则，认证失败
    ```

- `/etc/ssh/` 下的配置文件

  ```shell
  在Linux系统中，/etc/ssh目录是用来存放SSH服务的配置文件的。其中包括了ssh的主配置文件sshd_config和ssh的客户端配置文件ssh_config。sshd_config文件是SSH服务端的配置文件，而ssh_config文件则是SSH客户端的配置文件。这些配置文件可以用来配置SSH服务的各种参数，例如端口号、认证方式、密钥等等。此外，/etc/ssh目录下还包含了一些其他的文件，例如moduli文件和ssh_host_*文件，这些文件也都是SSH服务所需要的
  ```

- 无法免密登录

  ```
  1、检查.ssh 之中authorized_keys文件权限： 700
  2、检查/etc/ssh/sshd_config文件
  # 去掉“#” ；yes --> no
  #StrictModes yes  --> StrictModes no
  #AuthorizedKeysFile .ssh/authorized_keys --> AuthorizedKeysFile .ssh/authorized_keys
  3、重启
  ```

- Docker 容器中修改配置后重启

  ```shell
  sudo /etc/init.d/ssh restart
  ```

- [容器无法登录](https://blog.csdn.net/qq_33259057/article/details/124737659?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171005469816800222831046%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171005469816800222831046&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-124737659-null-null.142^v99^pc_search_result_base5&utm_term=ssh%20%E8%BF%9E%E6%8E%A5docker%20%E5%AE%B9%E5%99%A8&spm=1018.2226.3001.4187)

  - 容器22端口需要进行挂载，比如挂载到主机2222端口 ` docker run -d -p 2222:22 your_image`
  
  - 进行安装`openssh-server`

  - 按照上述更改配置文件
  
  - 链接到容器：`ssh -p 2222 user@host`， user 是容器用户名称，host 是主机ip
  
    
  
  