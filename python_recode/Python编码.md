## 编码的作用

- encode进行编码、decode进行解码，使用什么编码就是用什么解码

- bytes字节类型，只可用于字符串吗，这样就不需要进行编码和解码了

  ```python
  str = b"1234"
  ```

- **为什么需要编码**

  - 因为底层网络数据的传输都是用**字节**进行传输的，**所以需要编码成字节的方式进行传输**
  - 而接收来的数据的编码格式有多种，所以需要选择对应的编码格式进行解码
  - [参考链接](https://blog.csdn.net/qq_34784354/article/details/109718290?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-0.pc_relevant_paycolumn_v3&spm=1001.2101.3001.4242.1&utm_relevant_index=3)