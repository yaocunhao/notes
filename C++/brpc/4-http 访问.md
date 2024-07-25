- [link](https://brpc.apache.org/docs/server/serve-httph2/)

# 一、请求头信息

# 二、请求body

- 在Http 服务器中读取和设置都在 `Controller.request_attachment()、`Controller.response_attachment()` 之中

  - 注意，这种方式只适合在 <font color=yellow>http/2 搭建的服务器之中，如果是Protobuf 协议的服务器之中，请求体还是在接口的请求和响应指针之中的</font>。这是因为protobuf 服务器的body 就算是json也会自动转换成protobuf格式，如果格式不对则抛出异常，因此想要接受其它格式的数据必须定义对应的**http服务器**

  ```shell
  如何配置brpc以接收非protobuf数据
  为了让brpc服务器接收和正确处理非protobuf数据（例如，JSON），你需要在服务器端进行适当的配置和编码处理：
  
  解析HTTP请求：在服务实现中，可以使用brpc::Controller来访问原始HTTP请求数据，并根据Content-Type进行解析。这通常涉及到手动解析brpc::Controller提供的request_attachment()中的数据。
  
  内容类型处理：在服务逻辑中，根据请求的Content-Type，决定使用哪种解析器（例如，JSON解析器）来处理请求体。
  
  定义HTTP服务：虽然brpc以protobuf接口定义为中心，但你也可以定义HTTP服务来专门处理非protobuf格式的数据。这涉及到使用brpc提供的HTTP接口，而不是基于protobuf的RPC接口。
  ```

  

# 三、如何分配设置Url

- 在brpc::server 之中添加服务实例的时候分配的

# 四、模型

- brpc 使用的是  Proactor 模式的I/O模型，需要注意全局变量的使用(全局变量非线程安全)

# 五、日志

- brpc 内置日志和glog是冲突，如果想要使用glog，需要在编译的时候增加`DWITH_GLOG=ON` 选项
- 日志初始化代码如下

```c++
// log init
void log_init() {
  std::cout << "日志初始化" << std::endl;

  LOG(DEBUG) << "Init logInit logInit logInit logInit logInit logInit log";
  logging::LoggingSettings log_setting;
  log_setting.logging_dest = logging::LOG_TO_FILE;
  log_setting.log_file = "./log/log.txt"; // 目录需要已经存在
  log_setting.lock_log = logging::LOCK_LOG_FILE;
  log_setting.delete_old = logging::APPEND_TO_OLD_LOG_FILE;

  auto log_init_result = logging::InitLogging(log_setting);
  LOG(DEBUG) << "LOG INIT SUCESS=" << log_init_result;
}
```

- 设置日志等级

  ```c++
  #define LOG_IS_ON(severity)                                     \
      (::logging::BLOG_##severity >= ::logging::GetMinLogLevel())
  // BLOG_##  这里是进行字符串拼接
  
  // 宏的等级有(logging.h)
  typedef int LogSeverity;
  const LogSeverity BLOG_VERBOSE = -1;  // This is level 1 verbosity
  // Note: the log severities are used to index into the array of names,
  // see log_severity_names.
  const LogSeverity BLOG_INFO = 0;
  const LogSeverity BLOG_NOTICE = 1;
  const LogSeverity BLOG_WARNING = 2;
  const LogSeverity BLOG_ERROR = 3;
  const LogSeverity BLOG_FATAL = 4;
  const int LOG_NUM_SEVERITIES = 5;
    
  ```

  

- 自定义日志风格

- 日志待判断输出

  ```c
  // log output method
  // 1  LOG(INFO) << "Found " << num_cookies << " cookies";
  // 2  LOG_IF(INFO, num_cookies > 10) << "Got lots of cookies";
  ```

- 调试模式下的日志，不会被编译到代码之中

# 六、Other

- Brpc 的http 访问，服务器内部会自动转换Get和Post方法，`brpc::Controller` 之中记录的请求头对应的信息

  ```c++
      void Service(google::protobuf::RpcController *cntl_base,
                   const EchoRequest *request,
                   EchoResponse *response,
                   google::protobuf::Closure *done)
      {
        brpc::ClosureGuard done_guard(done);
        brpc::Controller *cntl =
            static_cast<brpc::Controller *>(cntl_base);
        if (cntl)
        {
          std::cout << "测试url" << std::endl;
         // 1、得到请求头信息
          auto http_header = cntl->http_request();  
         // 2、得到url 和 http 版本信息
          std::cout << http_header.uri() << ",,,," << http_header.major_version()<<",,,,"<< http_header.method() << std::endl;
        }
        else
        {
          std::cout << "cntl is empty" << std::endl;
        }
  ```




# 七、测试代码

```c++
#include <brpc/server.h>
#include <butil/logging.h>
#include <chrono>
#include <gflags/gflags.h>
#include <thread>
#include <unistd.h>
#include "echo.pb.h"

DEFINE_bool(echo_attachment, true, "Echo attachment as well");
DEFINE_int32(port, 8060, "TCP Port of this server");
DEFINE_int32(idle_timeout_s, -1,
             "Connection will be closed if there is no "
             "read/write operations during the last `idle_timeout_s'");
DEFINE_int32(logoff_ms, 2000,
             "Maximum duration of server's LOGOFF state "
             "(waiting for client to close connection before server stops)");

// Your implementation of example::EchoService
// Notice that implementing brpc::Describable grants the ability to put
// additional information in /status.
namespace example {
class EchoServiceImpl : public EchoService {
 public:
  EchoServiceImpl(){};
  virtual ~EchoServiceImpl(){};

  void Service(google::protobuf::RpcController *cntl_base,
               const EchoRequest *request, EchoResponse *response,
               google::protobuf::Closure *done) {
    brpc::ClosureGuard done_guard(done);
    brpc::Controller *cntl = static_cast<brpc::Controller *>(cntl_base);
    if (cntl) {
      std::cout << "测试url" << std::endl;

      // 得到请求头信息
      auto http_header = cntl->http_request();
      std::cout << http_header.uri() << "---" << http_header.unresolved_path()
                << "----" << http_header.major_version() << ",,,,"
                << http_header.method() << std::endl;
      // 获取content_type
      std::cout << "Content_type is :" << http_header.content_type()
                << std::endl;
      // 获取其它的值,注意，此方法不能替代content_type
      std::cout << "Other:" << *http_header.GetHeader("didi_log_id")
                << std::endl;

      // Use printed query string and body as the response.
      // butil::IOBufBuilder os;
      // os << "queries:";
      // os << "\nbody: " << cntl->request_attachment() << '\n';
      // os.move_to(cntl->response_attachment());

      butil::IOBuf &req_body = cntl->request_attachment();
      std::string req_body_str;
      req_body.copy_to(&req_body_str);
      std::cout << "请求体" << req_body_str << std::endl;

      butil::IOBufBuilder os;
      os << "queries:";
      os << "\nbody: " << cntl->request_attachment() << '\n';
      os.move_to(cntl->response_attachment());
    } else {
      std::cout << "cntl is empty" << std::endl;
    }
  }

  virtual void Echo(google::protobuf::RpcController *cntl_base,
                    const EchoRequest *request, EchoResponse *response,
                    google::protobuf::Closure *done) {
    // This object helps you to call done->Run() in RAII style. If you need
    // to process the request asynchronously, pass done_guard.release().

    brpc::ClosureGuard done_guard(done);

    brpc::Controller *cntl = static_cast<brpc::Controller *>(cntl_base);

    // The purpose of following logs is to help you to understand
    // how clients interact with servers more intuitively. You should
    // remove these logs in performance-sensitive servers.
    LOG(INFO) << "Received request[log_id=" << cntl->log_id() << "] from "
              << cntl->remote_side() << " to " << cntl->local_side() << ": "
              << request->message()
              << " (attached=" << cntl->request_attachment() << ")";

    // Fill response.

    std::cout << "Polygon Points:" << std::endl;
    for (int i = 0; i < request->polygon().polyline_size(); ++i) {
      const Point &point = request->polygon().polyline(i);
      std::cout << "Point(x=" << point.x() << ", y=" << point.y() << ")"
                << std::endl;
    }

    // todo:
    response->set_message("服务器回显消息");
    Point *point_1 = response->mutable_polygon()->add_polyline();
    point_1->set_x(1.0);
    point_1->set_y(2.0);

    Point *point2 = response->mutable_polygon()->add_polyline();
    point2->set_x(3.0);
    point2->set_y(4.0);

    // You can compress the response by setting Controller, but be aware
    // that compression may be costly, evaluate before turning on.
    // cntl->set_response_compress_type(brpc::COMPRESS_TYPE_GZIP);

    if (FLAGS_echo_attachment) {
      // Set attachment which is wired to network directly instead of
      // being serialized into protobuf messages.
      cntl->response_attachment().append(cntl->request_attachment());
    }
    // std::cout << "休眠11s" << std::endl;
    // std::this_thread::sleep_for(std::chrono::seconds(11));
  }
};
}  // namespace example

int main(int argc, char *argv[]) {
  // Parse gflags. We recommend you to use gflags as well.
  GFLAGS_NS::ParseCommandLineFlags(&argc, &argv, true);

  // Generally you only need one Server.
  brpc::Server server;

  // Instance of your service.
  example::EchoServiceImpl echo_service_impl;

  // Add the service into server. Notice the second parameter, because the
  // service is put on stack, we don't want server to delete it, otherwise
  // use brpc::SERVER_OWNS_SERVICE.
  // 对外提供服务
  if (server.AddService(&echo_service_impl, brpc::SERVER_DOESNT_OWN_SERVICE,
                        "/test/* =>Echo,"
                        "/test2/* => Service") != 0) {
    LOG(ERROR) << "Fail to add service";
    return -1;
  }

  // Start the server.
  brpc::ServerOptions options;
  options.idle_timeout_sec = FLAGS_idle_timeout_s;
  if (server.Start(FLAGS_port, &options) != 0) {
    // 此处有日志模块
    LOG(ERROR) << "Fail to start EchoServer";
    return -1;
  }
  // Wait until Ctrl-C is pressed, then Stop() and Join() the server.
  server.RunUntilAskedToQuit();
  return 0;
}

```

