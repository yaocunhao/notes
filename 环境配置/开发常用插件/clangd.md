# 一、centos7 使用clangd

- centos7 无法使用clangd(ld版本太老)。
- [给clangd 打补丁](https://www.zhihu.com/search?type=content&q=centos7%20%E5%86%99C%2B%2B%E6%9C%89%E4%BB%80%E4%B9%88%E6%AF%94%E8%BE%83%E5%A5%BD%E7%9A%84lsp%E4%B9%88)

\# 下载并编译安装 glibc，假设安装到 /opt/glibc-2.18 # 略 # 安装 patchelf: GitHub - NixOS/[patchelf](https://www.zhihu.com/search?q=patchelf&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2564104885"}): A small utility to modify the dynamic linker and RPATH of ELF executables yum install patchelf # 为 clangd 打补丁，使其使用最新的 glibc patchelf --set-interpreter /opt/glibc-2.18/lib/ld-linux-x86-64.[so.2 --set-rpath](https://www.zhihu.com/search?q=so.2 --set-rpath&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2564104885"}) /opt/glibc-2.18/lib/ <path-to-clangd>/clangd







# 二、centos8 llvm 相关插件

- [clangd](https://github.com/clangd/clangd/tags) 直接使用二进制文件,只和libc 版本相关

- [clang-format](https://pypi.org/project/clang-format/) 使用pip 安装使用

  ```shell
  安装失败请升级Python 环境 
  
  sudo pip3 install --upgrade setuptools
  sudo python3  -m pip install --upgrade pip
  pip3 install clang-format
  ```

  
