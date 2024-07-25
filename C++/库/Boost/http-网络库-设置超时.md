- boost 同步请求无法设置超时时间，因此需要通过异步的方式来进行设置

  - [为了可移植性，同步操作不支持超时设置](https://stackoverflow.com/questions/56828654/timeout-for-boostbeast-sync-http-client#:~:text=I/O%20%E4%B8%8D-,%E6%94%AF%E6%8C%81,-%E8%B6%85%E6%97%B6%E3%80%82%E7%94%B1%E4%BA%8E)
  - `boost::asio::spawn` 开启一个协程，返回值是void
  - `net::io_context` 的主要作用是：
  
    - 管理和调度所有异步操作。
    - 提供事件循环机制，确保异步操作在完成后正确处理。
    - 协程的挂起和恢复调度。
  
    通过 `io_context`，可以实现高效的异步 I/O 操作管理，适合用于网络编程和其他需要高并发处理的场景。
  - yield 将协程控制权交出去，下面这种写法就类似于阻塞了
  
  ```c++
  #include <boost/asio.hpp>
  #include <boost/asio/connect.hpp>
  #include <boost/asio/io_context.hpp>
  #include <boost/asio/ip/tcp.hpp>
  #include <boost/asio/spawn.hpp>
  #include <boost/asio/ssl/stream.hpp>
  #include <boost/beast/core.hpp>
  #include <boost/beast/http.hpp>
  #include <boost/beast/ssl.hpp>
  #include <boost/beast/version.hpp>
  #include <chrono>
  #include <fstream>
  #include <iostream>
  #include <string>
  #include "rapidjson/document.h"
  #include "rapidjson/prettywriter.h"
  #include "rapidjson/stringbuffer.h"
  #include "rapidjson/writer.h"
  
  namespace beast = boost::beast;               // from <boost/beast.hpp>
  namespace http = beast::http;                 // from <boost/beast/http.hpp>
  namespace net = boost::asio;                  // from <boost/asio.hpp>
  namespace ssl = boost::asio::ssl;             // from <boost/asio/ssl.hpp>
  using tcp = net::ip::tcp;                     // from <boost/asio/ip/tcp.hpp>
  using ssl_stream = ssl::stream<tcp::socket>;  // SSL stream
  
  /** Request an HTTP resource from a TLS host and return it as a string, with a
     timeout.
  
      This example uses fibers (stackful coroutines) and its own I/O context.
  */
  std::string https_get(boost::system::error_code& ec) {
    std::cout << "开始请求" << std::endl;
    sleep(1);
    // It is the responsibility of the algorithm to clear the error first.
    ec = {};
  
    // We use our own I/O context, to make this function blocking.
    net::io_context ioc;
  
    // This string will hold the body of the HTTP response, if any.
    std::string result;
  
    // Note that Networking TS does not come with spawn. This function
    // launches a "fiber" which is a coroutine that has its own separately
    // allocated stack.
  
    // 使用 Boost.Asio 的异步协程 boost::asio::spawn 来执行异步操作，并返回 HTTP
    // 响应的主体
    boost::asio::spawn(ioc, [&](boost::asio::yield_context yield) {
      beast::tcp_stream stream{ioc};
  
      // The resolver will be used to look up the IP addresses for the host name
      net::ip::tcp::resolver resolver(ioc);
  
      // First, look up the name. Networking has its own timeout for this.
      // The `yield` object is a CompletionToken which specializes the
      // `net::async_result` customization point to make the fiber work.
      //
      // This call will appear to "block" until the operation completes.
      // It isn't really blocking. Instead, the fiber implementation saves
      // the call stack and suspends the function until the asynchronous
      // operation is complete. Then it restores the call stack, and resumes
      // the function to the statement following the async_resolve. This
      // allows an asynchronous algorithm to be expressed synchronously.
  
      std::string url = "www.com";
  
      // async_resolve 函数的第三个参数是一个 resolver_base::flags
      // 类型，用于指定域名解析操作的选项。{}
      // 表示没有额外的解析选项，使用默认设置。具体来说，resolver_base::flags
      // 可以包括以下几种选项：
      // address_configured: 只返回已配置的地址。
      // canonical_name: 返回标准化的主机名。
      // numeric_host: 将主机名作为数值处理。
      // numeric_service: 将服务名作为数值处理。
      // v4_mapped: 返回 IPv4 映射的 IPv6 地址。
      auto const endpoints = resolver.async_resolve(url, "http", {}, yield[ec]);
      if (ec) return;
  
      // The function `get_lowest_layer` retrieves the "bottom most" object
      // in the stack of stream layers. In this case it will be the tcp_stream.
      // This timeout will apply to all subsequent operations collectively.
      // That is to say, they must all complete within the same 30 second
      // window.
  
      get_lowest_layer(stream).expires_after(std::chrono::milliseconds(120));
  
      // `tcp_stream` range connect algorithms are member functions, unlike
      // net::
      get_lowest_layer(stream).async_connect(endpoints, yield[ec]);
      if (ec) return;
  
      // Send an HTTP GET request for the target
      {
        std::string target = "xxx";
  
        http::request<http::empty_body> req;
        req.method(http::verb::get);
        req.target(target);
        req.version(11);
        // req.set(http::field::server, host);
        req.set(http::field::host, url);
        req.set(http::field::user_agent, "Beast");
        std::cout << "发送请求" << std::endl;
        http::async_write(stream, req, yield[ec]);
        if (ec) return;
      }
  
      // Now read the response
      beast::flat_buffer buffer;
      http::response<http::string_body> res;
      http::async_read(stream, buffer, res, yield[ec]);
      if (ec) return;
  
      // Set the string to return to the caller
      result = std::move(res.body());
    });
  
    // `run` will dispatch completion handlers, and block until there is
    // no more "work" remaining. When this call returns, the operations
    // are complete and we can give the caller the result.
    ioc.run();
  
    return result;
  }
  
  int main() {
    boost::system::error_code ec;
    std::string result = https_get(ec);
    std::cout << result << std::endl;
    std::cout << "run!!!!!" << std::endl;
  
    std::cout << "错误码:" << ec << std::endl;
    return 0;
  }
  ```
  
  