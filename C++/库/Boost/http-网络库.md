# 零、参考

- [link](https://zhuanlan.zhihu.com/p/678342488)

# 一、http::respons， 和 http::response_parse 的区别和用法示例

- `http::response` 是一个模板类，用于表示 HTTP 响应消息。这个类包含状态码、消息头和消息体。它是用来构建或存储一个 HTTP 响应的数据结构，允许你访问响应的不同部分，例如状态码（如 200 OK、404 Not Found 等）和响应正文

- `http::response_parser` 是用于解析 HTTP 响应的解析器。它可以从原始字节流中读取数据，并将其解析为 `http::response` 对象。这个类主要用于客户端接收和解析来自服务器的响应

  ```c++
  #include <boost/beast/core.hpp>
  #include <boost/beast/http.hpp>
  #include <boost/asio/connect.hpp>
  #include <boost/asio/ip/tcp.hpp>
  #include <iostream>
  
  namespace beast = boost::beast; // from <boost/beast.hpp>
  namespace http = beast::http; // from <boost/beast/http.hpp>
  namespace net = boost::asio; // from <boost/asio.hpp>
  using tcp = net::ip::tcp; // from <boost/asio/ip/tcp.hpp>
  
  int main() {
      try {
          // 设置服务器和端口
          std::string const host = "example.com";
          auto const port = "80";
          auto const target = "/index.html";
  
          // I/O上下文
          net::io_context ioc;
  
          // 解析服务器地址
          tcp::resolver resolver{ioc};
          auto const results = resolver.resolve(host, port);
  
          // 连接到服务器
          tcp::socket socket{ioc};
          net::connect(socket, results.begin(), results.end());
  
          // 设置请求
          http::request<http::string_body> req{http::verb::get, target, 11};
          req.set(http::field::host, host);
          req.set(http::field::user_agent, "Boost.Beast");
  
          // 发送请求
          http::write(socket, req);
  
          // 为响应读取分配缓冲区
          beast::flat_buffer buffer;
  
          // 声明响应解析器
          http::response_parser<http::dynamic_body> parser;
  
          // 从socket读取并解析响应
          http::read(socket, buffer, parser);
  
          // 获取解析后的响应
          http::response<http::dynamic_body> response = parser.release();
  
          // 输出响应的正文
          std::cout << beast::buffers_to_string(response.body().data()) << std::endl;
  
          // 关闭socket
          beast::error_code ec;
          socket.shutdown(tcp::socket::shutdown_both, ec);
  
          if(ec && ec != beast::errc::not_connected)
              throw beast::system_error{ec};
      }
      catch(std::exception const& e) {
          std::cerr << "Error: " << e.what() << std::endl;
      }
  
      return 0;
  }
  
  ```

  - 上述太过繁琐，可以只接用`http::resonse` 对象去进行解析

    ```c++
    #include <boost/beast/core.hpp>
    #include <boost/beast/http.hpp>
    #include <boost/asio/connect.hpp>
    #include <boost/asio/ip/tcp.hpp>
    #include <iostream>
    
    namespace beast = boost::beast; // from <boost/beast.hpp>
    namespace http = beast::http; // from <boost/beast/http.hpp>
    namespace net = boost::asio; // from <boost/asio.hpp>
    using tcp = net::ip::tcp; // from <boost/asio/ip/tcp.hpp>
    
    int main() {
        try {
            // 设置服务器和端口
            std::string const host = "example.com";
            auto const port = "80";
            auto const target = "/index.html";
    
            // I/O上下文
            net::io_context ioc;
    
            // 解析服务器地址
            tcp::resolver resolver{ioc};
            auto const results = resolver.resolve(host, port);
    
            // 连接到服务器
            tcp::socket socket{ioc};
            net::connect(socket, results.begin(), results.end());
    
            // 设置请求
            http::request<http::string_body> req{http::verb::get, target, 11};
            req.set(http::field::host, host);
            req.set(http::field::user_agent, "Boost.Beast");
    
            // 发送请求
            http::write(socket, req);
    
            // 为响应读取分配缓冲区
            beast::flat_buffer buffer;
    
            // 声明响应对象
            http::response<http::dynamic_body> res;
    
            // 从socket读取并解析响应
            http::read(socket, buffer, res);
    
            // 输出响应的正文
            std::cout << beast::buffers_to_string(res.body().data()) << std::endl;
    
            // 关闭socket
            beast::error_code ec;
            socket.shutdown(tcp::socket::shutdown_both, ec);
    
            if(ec && ec != beast::errc::not_connected)
                throw beast::system_error{ec};
        }
        catch(std::exception const& e) {
            std::cerr << "Error: " << e.what() << std::endl;
        }
    
        return 0;
    }
    // 这个代码段展示了一个更直接的方式来读取和解析HTTP响应，这在很多情况下是更方便的。然而，需要注意的是，使用这种方法可能会在某些复杂场景下减少一些灵活性和控制，比如在需要对响应解析过程有更细粒度控制的情况下，或者在处理非标准的或不规则的HTTP响应时。在这些情况下，使用 http::response_parser 可能会提供更多的选项和更细致的控制
    ```

    - http::response<http::dynamic_body> res; 这里的作用是

      ```c++
      //在使用 http::response<http::dynamic_body> 声明 res 的代码行中，res 的作用是作为一个容器来存储和表示从 HTTP 服务器接收到的响应数据。这里的 http::response 是 Boost.Beast 库中的一个模板类，用于表示 HTTP 响应消息，而 <http::dynamic_body> 指定了响应正文（body）的类型。
      
      //http::dynamic_body 是一种灵活的正文类型，可以存储任意大小和类型的数据。它使用动态内存分配来适应正文内容的大小，这使得它能够处理各种不同类型和大小的 HTTP 响应，无论响应正文是文本、JSON、HTML，还是二进制数据等。使用 http::dynamic_body，你不需要提前知道响应正文的具体大小或类型，它会自动适应接收到的数据。
      
      //总的来说，http::response<http::dynamic_body> res; 这行代码的目的是：
      定义一个用于接收 HTTP 响应的对象 res。
      使用 http::dynamic_body 作为响应正文的类型，以便灵活处理任何类型和大小的响应内容。
      res 对象在使用 http::read 函数读取和解析网络流中的 HTTP 响应数据时被填充。这包括状态码、响应头部（headers）和响应正文（body）。
      之后，可以通过 res 对象访问和处理接收到的 HTTP 响应数据，例如输出响应正文或根据状态码执行不同的逻辑。
      ```

      



- res.body().data() ， res.body() 是什么意思，res.body().data 是什么意思

  ```c++
  在使用Boost.Beast库进行HTTP操作时，res.body()和res.body().data()是处理HTTP响应的一部分，用于访问和操作响应体（正文）的内容。
  
  res.body(): 在Boost.Beast的HTTP响应对象res中，res.body()返回的是响应正文的对象或容器。这个对象的具体类型取决于响应对象模板的定义。例如，如果响应被定义为http::response<http::dynamic_body>，那么res.body()返回的将是一个可以动态存储和操作数据的容器。这个容器支持读取响应正文的内容，允许进一步的操作如转换数据格式、解析JSON等。
  
  res.body().data(): 对于Boost.Beast库中的某些响应体类型（如http::string_body或http::dynamic_body），res.body().data()通常用于访问响应正文数据的实际存储表示。这通常返回一个指向正文数据的指针或者一个能够以某种形式访问数据的接口。具体返回什么类型的数据，取决于res.body()的类型和实现。在http::dynamic_body的情况下，data()方法可能返回一个表示正文数据的多个缓冲区的视图，可以用于读取或输出响应正文的内容。
  
  简而言之，res.body()提供对响应正文的高级访问，而res.body().data()则用于访问响应正文的底层数据表示，允许直接读取或操作这些数据。这些功能使得在处理HTTP响应时能够灵活地访问和使用正文内容
  ```

  

# 二、读取响应

- 读取方式

  ```c++
  // 读取返回值，res.body().date() 可能返回多个缓冲区，因此需要进行合并
  beast::flat_buffer buffer;
  http::response<http::dynamic_body> res;
  http::read(stream, buffer, res);
  
  // 1、直接转成字符串
  std::string body = boost::beast::buffers_to_string(res.body().data());
  
  // 2、二进制的方式写入文件
  // 遍历响应体中的每个缓冲区并写入文件
  for (auto const& buffer : beast::buffers_range(res.body().data())) {
    fp.write(static_cast<const char*>(buffer.data()), buffer.size());
  }
  ```

- res、buffer 的关系

  ```c++
  // buffer 被用来存储读取的原始字节流数据，而 res 则被用来存储解析后的 HTTP 响应对象。
  // 因此，调用 http::read(stream, buffer, res); 的过程中，buffer 会被填充上从 stream 中读取的原始字节流数据，而 res 会被填充上解析后的 HTTP 响应对象。换句话说，buffer 用于存储原始数据，而 res 用于存储解析后的高级数据结构，它们之间存在着一种“生产者-消费者”的关系。
  
  // 需要注意的是，在实际调用这段代码时，buffer 中的数据会被读取和填充，而 res 中的数据也会被填充，因此在函数调用完成后，这两个变量中将包含函数执行后的结果
  ```

  

- 正由上述多个缓冲区的存在，数据量很大时存在延迟读取，因此 res和buffer的声明周期需要保持一致

  - 不确定，测试结果看是不需要的，但是boost文档注释中说需要一致保持

