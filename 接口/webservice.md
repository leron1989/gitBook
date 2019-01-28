# webservice接口笔记

## cxf

### client

1. 生成客户端代码

   * 下载cxf包

   * 通过wsdl2java命令生成客户端代码

     > D:\JavaWork\apache-cxf-3.0.14\bin\wsdl2java -uri http://172.17.57.134:8080/cxf/tbglPlanJobInfo?wsdl -p com.huaxin -s -o D:\stub

- -ant 指定要生成一个ant的构建说明文档build.xml。
- -client 指定要生成一个测试客户端的“起点代码”（starting point code），其中包含了端口中所有方法的测试样例代码。
- -d 指定我们想要把生成的文件放到哪个目录下，默认是wsdl2Java命令所在的目录。
- -p 指定生成文件的包名；默认是根据WSDL文件中的命名空间相对应。
- -frontend 指定前端类型及版本；目前仅支持 JAXWS 前端，用 "jaxws21" 表明要生成JAX-WS 2.1 兼容的代码（Jre6自带的就是这个版本），使用该参数的原因[请参考教程(2)](http://blog.csdn.net/changpingchen/article/details/9048227)。