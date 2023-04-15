- Content-Disposition

  - [表示返回的文件是以内联的形式返回还是以附加文件的形式返回](https://crayon-shin-chan.blog.csdn.net/article/details/122903087?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7EPayColumn-1-122903087-blog-88120353.pc_relevant_multi_platform_whitelistv4&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7EPayColumn-1-122903087-blog-88120353.pc_relevant_multi_platform_whitelistv4&utm_relevant_index=1)

    ```python
    response["Content-Disposition"] = 'attachment; filename={0}'.format(file_name)
    ```

- Access-Control-Expose-Headers

  - http协议只有几种默认的头部会暴露出来，设置该head来使得对应的头部属性暴露出来

    ```python
    response["Access-Control-Expose-Headers"] = "Content-Disposition"  # 表示哪些内容可以暴露给外部 (为了使前端获取到Content-Disposition属性)
    ```

    

- Content-Type

  - [设置http的内容类型](https://blog.csdn.net/qq_42582773/article/details/121807555?ops_request_misc=&request_id=&biz_id=102&utm_term=application/octet-stream&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-0-121807555.article_score_rank_blog&spm=1018.2226.3001.4450)

    ```python
    response["Content-Type"] = "application/octet-stream" # 返回内容的类型
    
    # 在某些下载文件的场景中，服务端可能会返回文件流，并在返回头中带上Content-Type:application/octetstream，告知浏览器这是一个字节流，浏览器处理字节流的默认方式就是下载
    ```

- Content-Type 实际上是给游览器看的，**任何数据对后端来说都是字节**，可以根据字符串比较判断来对不同的类型做出处理