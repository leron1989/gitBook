[TOC]
## 配置及优化

### 同一服务器部署多个tomcat时的端口号修改详情（同时启动两个tomcat）

同一服务器部署多个tomcat时，存在端口号冲突的问题，所以需要修改tomcat配置文件server.xml，以tomcat7为例。

```xml
<!-- 其中8080为HTTP端口，8443为HTTPS端口 -->
<Connector URIEncoding="UTF-8" connectionTimeout="60000" port="8080" protocol="HTTP/1.1" 
redirectPort="8443" useBodyEncodingForURI="true"/>

<!-- 8005为远程停服务端口 -->
<Server port="8005" shutdown="SHUTDOWN">

<!-- 8009为AJP端口，APACHE能过AJP协议访问TOMCAT的8009端口。 -->
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>
```

**部署多个tomcat主要修改以上三个端口**



### Tomcat启动慢解决方案

#### 原因

Tomcat启动很慢是session随机数问题导致的。Tomcat的Session ID通过SHA1算法计算得到的，计算Session ID的时候必须有1个秘钥，为了提高安全性Tomcat在启动的时候通过随机数生成秘钥。

日志如下（tomcat8）：

```
4-Mayr-2017  8:07:49 .623 INFO[localhost-startStop-1]org.apache.catalina.util.SessionIdGeneratorBase.createSecureRandom Creation of
 SecureRandominstance for session ID generation using [SHA1PRNG] took [55,507] milliseconds.
4-Mayr-2017  8:07:49 .653 INFO[localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectoryDeployment of web appli
cation directory/application/apache-tomcat-8.0.27/webapps/ROOT has finished in 165,935 ms
```

#### 处理及优化

1. 方法一：使用rngd 软件增大熵池 ***\******建议使用

   ```sh
   grep  rdrand /proc/cpuinfo #需要cpu支持 
   yum install rng-tools # 安装rngd服务（熵服务，增大熵池）
   systemctl start rngd  # 启动服务
   ```

2. 方法二：java环境下修改配置文件

   ```sh
   vim $JAVA_HOME/jre/lib/security/java.security
   securerandom.source=file:/dev/random
   改为
   securerandom.source=file:/dev/urandom
   ```

3. 方法三：可以通过配置JRE使用非阻塞的Entropy Source

   ```sh
   vim $TOMCAT_HOME/bin/catalina.sh
   if [[ "$JAVA_OPTS" !=*-Djava.security.egd=* ]]; then
      JAVA_OPTS="$JAVA_OPTS -Djava.security.egd=file:/dev/urandom"
   fi
   ##这个系统属性egd表示熵收集守护进程(entropy gathering daemon)
   ```

### 调整线程数

```xml
<Connector port="9090" protocol="org.apache.coyote.http11.Http11Protocol"
               connectionTimeout="20000"
               redirectPort="8443"
               server="hxkq"
               URIEncoding="UTF-8" 
               minSpareThreads="100"
               enableLookups="false"
               disableUploadTimeout="true" 
               acceptCount="1000"    
               maxThreads="1000"/>
```

