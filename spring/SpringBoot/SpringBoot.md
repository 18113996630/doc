[一起来学SpringBoot | 第二十八篇：JDK8 日期格式化](https://blog.battcn.com/2018/10/01/springboot/v2-localdatetime/)

数据验证

[轻松搞定数据验证（一）](https://blog.battcn.com/2018/06/05/springboot/v2-other-validate1/)
[轻松搞定数据验证（二）](https://blog.battcn.com/2018/06/06/springboot/v2-other-validate2/)
[轻松搞定数据验证（三）](https://blog.battcn.com/2018/06/07/springboot/v2-other-validate3/)

[轻松搞定全局异常](https://blog.battcn.com/2018/06/01/springboot/v2-other-exception/)

[轻松搞定文件上传](https://blog.battcn.com/2018/05/31/springboot/v2-other-upload/)

[定时任务详解](https://blog.battcn.com/2018/05/29/springboot/v2-other-scheduling/)

[服务监控与管理](https://blog.battcn.com/2018/05/24/springboot/v2-actuator-introduce/)
[actuator与spring-boot-admin](https://blog.battcn.com/2018/05/24/springboot/v2-actuator-monitor/)

[集成Swagger在线调试](https://blog.battcn.com/2018/05/16/springboot/v2-config-swagger/)

[使用Spring Cache集成Redis](https://blog.battcn.com/2018/05/13/springboot/v2-cache-redis/)

[整合Mybatis](https://blog.battcn.com/2018/05/09/springboot/v2-orm-mybatis/)
[通用Mapper与分页插件的集成](https://blog.battcn.com/2018/05/10/springboot/v2-orm-mybatis-plugin/)
[整合SpringDataJpa](https://blog.battcn.com/2018/05/08/springboot/v2-orm-jpa/)
[使用JdbcTemplate访问数据库](https://blog.battcn.com/2018/05/07/springboot/v2-orm-jdbc/)

[整合Thymeleaf模板](https://blog.battcn.com/2018/04/28/springboot/v2-web-thymeleaf/)

[SpringBoot日志配置](https://blog.battcn.com/2018/04/23/springboot/v2-config-logs/)

[SpringBoot配置详解](https://blog.battcn.com/2018/04/22/springboot/v2-config-properties/)

[Spring Boot配置多个DataSource](https://www.liaoxuefeng.com/article/001484212576147b1f07dc0ab9147a1a97662a0bd270c20000)

[Spring Boot应用迁移到Java最新版（Java 11）](https://mp.weixin.qq.com/s/avhIEa0mSzj4qepai-hJcA)


---------------------------
```
spring:
    # jackson时间格式化
    jackson:****
        time-zone: GMT+8
        date-format: yyyy-MM-dd HH:mm:ss
```        

```
spring.mvc.view.suffix=.jsp
# jsp放在 webapp/WEB-INF/jsp/
spring.mvc.view.prefix=/WEB-INF/jsp/
```

添加jsp支持
```
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>tomcat-jsp-api</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```

[SpringBoot配置jsp](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-samples/spring-boot-sample-web-jsp)

---------------------------