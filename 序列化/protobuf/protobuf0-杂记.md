# 一、[proto3和proto2区别](https://blog.csdn.net/weixin_34294649/article/details/92170258)

- 在第一行非空白非注释行，必须写：[syntax](https://so.csdn.net/so/search?q=syntax&spm=1001.2101.3001.7020) = “proto3”， 否则默认版本是proto2
- 字段规则**移除了 “required”**，并把 “optional” 改名为 “singular”
- “repeated”字段默认采用 packed 编码
  - 在 proto2 中，需要明确使用 [packed=true] 来为字段指定比较紧凑的 packed 编码方式

- 移除了 default 选项
  - 在 proto2 中，可以使用 default 选项为某一字段指定默认值。在 proto3 中，字段的默认值只能根据字段类型**由系统决定**。也就是说，**默认值全部是约定好的，而不再提供指定默认值的语法**
  - 在字段被设置为默认值的时候，该字段**不会被序列化**。这样可以节省空间，提高效率。但这样就无法区分某字段是根本没赋值，还是赋值了默认值。这在 proto3 中问题不大，但在 proto2 中会有问题
    - 比如，在更新协议的时候使用 default 选项为某个字段指定了一个与原来不同的默认值，旧代码获取到的该字段的值会与新代码不一样

- 枚举类型的第一个字段必须为 0，**这也是一个约定**
- 移除了对分组的支持
  - 分组的功能完全可以用消息嵌套的方式来实现，并且更清晰。在 proto2 中已经把分组语法标注为『过期』了。这次也算清理垃圾了

- 移除了对扩展的支持，新增了 Any 类型
- 增加了 JSON 映射特性



# 二、一些参考链接

- [_VarintBytes](https://www.jianshu.com/p/79a2d01a32db)
  - 序列化的一种方法

- [package](https://blog.csdn.net/u010900754/article/details/105984161/?ops_request_misc=&request_id=&biz_id=102&utm_term=protobuf%20%E6%96%87%E4%BB%B6%E4%B8%ADpackage%E7%9A%84%E4%BD%9C%E7%94%A8&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-105984161.142^v11^pc_search_result_control_group,157^v13^control&spm=1018.2226.3001.4187)
  - 一个包的标识
  - 比如pb文件A和pb文件B，在B文件中导入A文件的某一个消息类时，就需要带上这个包的标识作为前缀

- [变量后面的数字](https://blog.csdn.net/weixin_39862716/article/details/111774831?ops_request_misc=&request_id=&biz_id=102&utm_term=protobuf%20%E5%8F%98%E9%87%8F%E5%90%8E%E9%9D%A2%E7%9A%84%E6%95%B0%E5%AD%97&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-111774831.142^v11^pc_search_result_control_group,157^v13^control&spm=1018.2226.3001.4187)
  - 作用仅仅是标识位置而已
- [中文参考](https://blog.csdn.net/wuxintdrh/article/details/113623777?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165444563416782391859180%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165444563416782391859180&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-113623777-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=repeated+%E5%AD%97%E6%AE%B5%E8%A2%AB%E7%A7%BB%E9%99%A4%E4%BA%86%EF%BC%9F&spm=1018.2226.3001.4187)
- [文档翻译](https://zingzheng.github.io/post/protocol-buffers-python-generated-code/)
- [文档翻译](https://zhuanlan.zhihu.com/p/451390348)
- [演示文档](https://android.googlesource.com/platform/external/protobuf-javalite/+/da247c697d4164fd5ec84f56050890e6469d6edb/python/google/protobuf/internal/well_known_types_test.py)

