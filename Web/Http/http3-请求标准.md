- [restful 标准](https://blog.csdn.net/weixin_44149821/article/details/110399488?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167937975816800182771680%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167937975816800182771680&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-110399488-null-null.142^v74^insert_down3,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=restful%20%E8%A7%84%E8%8C%83&spm=1018.2226.3001.4187)



# 二、Get 请求的传参方式

- Get请求一般不支持使用Json传参，一般的形式为 `key_1=value_1&key_2=value2`
- 如若需要传递参数，可以先压缩成字节，然后k=“字节数值”的方式进行传递，然后在服务端进行解析即可