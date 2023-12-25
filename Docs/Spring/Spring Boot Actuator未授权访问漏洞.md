# Spring Boot Actuator未授权访问



### 1.1、漏洞描述

Actuator 是 springboot 提供的用来对应用系统进行自省和监控的功能模块，借助于 Actuator 开发者可以很方便地对应用系统某些监控指标进行查看、统计。

Actuator提供了一组默认的端点路径，如/autoconfig、/env、/metrics等。如果这些端点路径可以被未经授权的用户或系统访问，且响应包含系统的敏感信息、配置信息、性能指标等,则存在未授权访问漏洞。

在 Actuator 启用的情况下，如果没有做好相关权限控制，非法用户可通过访问默认的执行器端点（endpoints）来获取应用系统中的监控信息，从而导致信息泄露甚至服务器被接管的事件发生。默认配置会出现接口未授权访问，部分接口会泄露网站流量信息和内存信息等，使用Jolokia库特性甚至可以远程执行任意代码，获取服务器权限。

**未授权判定标准：**

1、:panda_face:访问端点路径：Actuator提供了一组默认的端点路径，如/actuator、/env、/metrics等。如果这些端点路径可以被未经授权的用户或系统访问，且响应包含系统的敏感信息、配置信息、性能指标等,则存在未授权访问漏洞。

```bash
/actuator
/auditevents
/autoconfig
/beans
/caches
/conditions
/configprops
/docs
/dump
/env
/flyway
/health
/heapdump
/httptrace
/info
/intergrationgraph
/jolokia
/logfile
/loggers
/liquibase
/metrics
/mappings
/prometheus
/refresh
/scheduledtasks
/sessions
/shutdown
/trace
/threaddump
/actuator/auditevents
/actuator/beans
/actuator/health
/actuator/conditions
/actuator/configprops
/actuator/env
/actuator/info
/actuator/loggers
/actuator/heapdump
/actuator/threaddump
/actuator/metrics
/actuator/scheduledtasks
/actuator/httptrace
/actuator/mappings
/actuator/jolokia
/actuator/hystrix.stream
```

其提供的执行器端点分为两类：原生端点和用户自定义扩展端点，原生端点主要有：

![image-20231225103616080](./imgs/image-20231225103616080.png)

2、:panda_face:在访问Actuator端点后，可以执行一些可能的未授权操作，如重启应用程序、关闭数据库连接等。

3、:panda_face:访问 /jolokia/list 接口，查看是否存在 ch.qos.logback.classic.jmx.JMXConfigurator 和 reloadByURL 关键词。若存在，则有可能造成造成命令执行。



### 1.3、漏洞复现

#### 1、基础环境

Path：Vulhub/weblogic/CVE-2017-10271

---

启动测试环境：

```bash
sudo docker-compose up -d
```

访问`http://your-ip:{端口}/`即可看到



#### 2、漏洞发现

通常识别当前 web 应用使用的框架为 springboot 框架。主要有两个方法判断：

1、通过 web 应用程序网页标签的图标（favicon.ico）；如果 web 应用开发者没有修改 springboot web 应用的默认图标，那么进入应用首页后可以看到如下默认的绿色小图标

![image-20231225104055036](./imgs/image-20231225104055036.png)

2、通过 springboot 框架默认报错页面；如果 web 应用开发者没有修改 springboot web 应用的默认 4xx、5xx 报错页面，那么当 web 应用程序出现 4xx、5xx 错误时，会报错如下（此处仅以 404 报错页面为例）：访问一个随便构造的路径，比如：http:/xxxxxxx:8090/index，出现如下报错页面说明web网站使用了springboot框架（在实际中遇到的大多数都是此类情况）

![image-20231225103924725](./imgs/image-20231225103924725.png)

综合以上两个途径来判断当前 web 应用是否是 springboot 框架，就是通过访问不同的目录，看是否有小绿叶图标，然后就是想办法在不同目录下触发应用程序的 4xx 或 5xx 错误，看是否有 Whitelabel Error Page 报错。



### 1.4、修复建议

1.禁用所有接口，将配置改成：

```
endpoints.enabled = false
```

2.引入spring-boot-starter-security依赖：

```
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

3.开启security功能，配置访问权限验证，类似配置如下：

```
management.port=8099
management.security.enabled=true
security.user.name=xxxxx
security.user.password=xxxxx
```



