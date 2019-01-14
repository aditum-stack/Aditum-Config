# Aditum Config 

> Apollo config server for Aditum. Aditum Apollo配置中心. 

使用Ctrip-Apollo配置中心，进行微服务的配置管理, 将Apollo部署在云服务器上， 通过网络进行配置访问获取. 
 
## [本地快速部署](local-quick-start/README.md) (用于本地开发和测试)

## [云服务器Docker部署☆](cloud-docker/README.md)

## [生产环境部署(官网教程)](https://github.com/kevinten10/apollo)

## 接入apollo客户端

1. 引入pom依赖

```xml
<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-client</artifactId>
    <version>1.2.0</version>
</dependency>
``` 

2. 启用客户端注解

@EnableApolloConfig

3. 设置配置项


    app.id信息是应用的信息，对一个应用而言是不会变的，所以放在META-INF/app.properties下面
    
    env信息是机器的属性，不是应用的属性，所以是放在机器上的server.properties或者是通过-D传入
    
    meta server信息是apollo-core.jar的配置属性，我们建议是直接打进包里，这样使用方使用的时候就不需要做额外配置了。apollo-env.properties也支持外置化，不过配置内容是dev.meta=xxx
    
    namespace是代码的一部分，这个就看你怎么弄了，写死在代码中也可以，放在application.yml中也可以
    
    
(1) 在resources下建立META-INF/app.properties文件，写入 app.id=AditumConfig

(2) 在server.properties中指定开发环境

    通过配置文件
    
    最后一个推荐的方式是通过配置文件来指定env=YOUR-ENVIRONMENT
    对于Mac/Linux，文件位置为/opt/settings/server.properties
    对于Windows，文件位置为C:\opt\settings\server.properties
    文件内容形如：
    
    env=DEV

(3) 在application.yml启动文件中写入apollo配置信息：

    apollo:
      # config server 地址(若外网访问会解析成内网地址，需使用-D参数跳过)
      meta: http://47.106.11.84:8080
      bootstrap:
        enabled: true
        # 使用公共命名空间和微服务私有命名空间
        namespaces: application
        
**注意：** 若远程访问云主机，则外网地址会被解析成内网地址无法访问，需使用JVM启动参数： 

[-Dapollo.configService=http://47.106.11.84:8080] 

避免进行内网解析

### 访问apollo配置参数

1. 配置文件：

通过 ${配置项key:获取失败的默认值} 进行使用，例如

    server:
      port: ${port:10001}
      
2. java程序：

使用 @Value('${配置项key:获取失败的默认值}')进行访问，例如

```java
/**
 * Apollo 配置获取客户端
 */
@Configuration
@EnableApolloConfig
public class ApolloConfigBean {

    @Value("${timeout:20}")
    private int timeout;

    public int getTimeout() {
        return timeout;
    }

    @Override
    public String toString() {
        return "ApolloConfigBean{" +
                "timeout=" + timeout +
                '}';
    }
}
```


