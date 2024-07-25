- yum 安装gcc、g++

  ```bash
  RUN yum install gcc -y \
  && yum install make -y \
  && yum install gcc-c++ -y \
  ```

- 安装依赖 ./contrib/download_prerequisites

- 前置处理 ./configure --prefix=/usr/local/gcc_install --enable-bootstrap --enable-checking=release --disable-multilib

- make -j、 make install

- 安装后环境变量处理

  ```
  export PATH=/usr/local/gcc/bin:$PATH
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/gcc/lib:/usr/local/gcc/lib64:/usr/local/gcc/libexec
  export INCLUDE=$INCLUDE:/usr/local/gcc/include/
  export CC=/usr/local/gcc/bin/gcc
  export CXX=/usr/local/gcc/bin/g++
  ```

- 与系统本身的库如何兼容存在？

  - 其它库基于此源码编译安装即可
