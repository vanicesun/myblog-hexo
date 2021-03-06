---
title: SpringBoot开发案例之奇技淫巧
date: 2019-11-07 16:01:58
categories: SpringBoot
tags: SpringBoot
---

> 程序员都有着一种天生的好奇心，这种好奇心引导着我们的编程生涯。写几行代码，装载到计算机里，让它按照你的思路工作，这是非常有趣的事情。但随着开发的东西越来越多，我们变的越来越忙，这种好奇心会慢慢的减退。我们应该时不时的用一些新思路挑战自己，让自己的思想保持锋锐和专注，提醒自己为什么当初选择码农这条道路。

### 版本标注
小伙伴们可能会发现pom.xml中很多是没有版本号的比如：
```
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
</dependency>
```
其实，在头部我们加了以下配置：
```
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.2.RELEASE</version>
        <relativePath/>
</parent>
```
**spring-boot-starter-parent包含了大量配置好的依赖管理，在自己项目添加这些依赖的时候不需要写<version>版本号**

### 注解说明

@SpringBootApplication : 是Spring Boot 项目的核心注解 主要目的是开启自动配置
@SpringBootApplication注解是一个组合注解,主要组合了@Configuration .+@EnableAutoConfiguration.+@ComponentScan
@Value : 属性注入,读取properties或者 Yml 文件中的属性
@ConfigurationProperties : 将properties属性和一个Bean及其属性关联,从而实现类型安全的配置
@ConfigurationProperties(prefix = "author",locations = {"classpath:config/author.properties"})
通过@ConfigurationProperties加载配置,通过prefix属性指定配置前缀,通过location指定配置文件位置
@EnableAutoConfiguration 注解:作用在于让 Spring Boot 根据应用所声明的依赖来对 Spring 框架进行自动配置
这个注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。由于spring-boot-starter-web添加了Tomcat和Spring MVC，所以auto-configuration将假定你正在开发一个web应用并相应地对Spring进行设置。
@ Configuration @EnableAutoConfiguration (exclude={xxxx.class}) 禁用特定的自动配置
@SpringBootApplication 注解等价于以默认属性使用 @Configuration，@EnableAutoConfiguration和 @ComponentScan。

### 热部署
方法1 添加springloaded依赖
```
<dependency>
     <groupId>org.springframework</groupId>
     <artifactId>springloaded</artifactId>
     <version>1.2.5.RELEASE</version>
</dependency>
```
原理：基于ASM实现动态生成类或者增强既有类，每次类的修改会被检测到，然后重新生成新的类并加载。如果不懂什么是ASM可以百度JAVA-ASM。

方法2 添加spring-boot-devtools依赖
```
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
</dependency>
```
原理：spring-boot-devtools 是一个为开发者服务的一个模块，其中最重要的功能就是自动应用代码更改到最新的App上面去。原理是在发现代码有更改之后，重新启动应用，但是速度比手动停止后再启动还要更快，更快指的不是节省出来的手工操作的时间。其深层原理是使用了两个ClassLoader，一个Classloader加载那些不会改变的类（第三方Jar包），另一个ClassLoader加载会更改的类，称为 restart ClassLoader,这样在有代码更改的时候，原来的restart ClassLoader 被丢弃，重新创建一个restart ClassLoader，由于需要加载的类相比较少，所以实现了较快的重启时间（5秒以内）。

### 配置文件
在 spring boot 中，有两种方式实现文件配置，application.properties 和 application.yml。大家可能对properties 比较熟悉，而另一种yml是基于YAML实现的，YAML 是一种比JSON（json多层次与 会被搞晕的）更直观的表现形式，展示上更易查错和关系描述。因为不需要一个专业工具就可以排查正确性。

下面，我们以server为例展示下两者的不同。

**application.properties**
```
server.context-path=/springboot
server.port=8080
server.session-timeout=60
server.address=192.168.1.66

server.tomcat.max-threads=300
server.tomcat.uri-encoding=UTF-8
```
**application.yml**
```
server:
    context-path: /springboot
    port: 8080
    session-timeout: 60
    address: 192.168.1.66
    tomcat:
      max-threads: 300
      uri-encoding: UTF-8
logging:
    level:
      root: INFO
```
yml天然的树状结构，一目了然，层次感强，有没有亮瞎你。当然使用yml要注意，层次间隔必须是空格不能是TAB，并且属性名的值和冒号中间必须有空格。

### 部署环境
**开发环境(development)**
application-dev.properties

**测试环境(test)**
application-test.properties

**生产环境(production)**
application-prod.properties

那么如何定义使用哪种配置文件呢？
　　在主配置文件application.yml中配置如下：
```
spring:
  profiles:
    active: dev
```

### 属性配置
如何在代码中获取配置文件中的属性呢？spring-boot为我们提供了这样一种方式，只需要使用@Value注解即可。
```
@Value("${spring.mail.username}")
public String USER_NAME;
```
### thymeleaf模版
默认配置下，thymeleaf对html的内容要求很严格，比如，如果少最后的标签封闭符号/，就会报错而转到错误页。

### 忽略thymeleaf严格的校验
spring.thymeleaf.mode=LEGACYHTML5

### 开发阶段设置为false方便调试
```
spring.devtools.livereload.enabled=true
spring.thymeleaf.cache=false
spring.thymeleaf.cache-period=0
spring.thymeleaf.template.cache=false
```

### 静态资源
Spring Boot中静态资源（JS, 图片）等应该放在什么位置？

Spring Boot能大大简化WEB应用开发的原因， 最重要的就是遵循“约定优于配置”这一基本原则。Spring Boot的关于静态资源的默认配置已经完全满足绝大部分WEB应用的需求。没必要去弄手续繁杂的自定义，用Spring Boot的约定就好了。

在Maven 工程目录下，所有静态资源都放在src/main/resource目录下，结构如下：
```
src/main/resource
          |__________static
                        |_________js
                        |_________images
                        |_________css
                 .....
```
比如，我们引入以下css:
```
<link rel="stylesheet" th:href="@{css/alipay.css}" />
```
### 自定义静态资源
### 通过配置文件配置
在application.properties（或.yml）中配置
```
# 静态文件请求匹配方式
spring.mvc.static-path-pattern=/**
# 修改默认的静态寻址资源目录 多个使用逗号分隔
spring.resources.static-locations = classpath:/templates/,classpath:/resources/,classpath:/static/
```
### 通过代码配置
```
@EnableAutoConfiguration
@ComponentScan(basePackages = { "com.itstyle.modules" })
public class Application extends WebMvcConfigurerAdapter {
    private static final Logger logger = Logger.getLogger(Application.class);
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/cert/**").addResourceLocations(
                "classpath:/cert/");
        super.addResourceHandlers(registry);
        logger.info("自定义静态资源目录");
    }

    public static void main(String[] args) throws InterruptedException,
            IOException {
        SpringApplication.run(Application.class, args);
        logger.info("支付项目启动 ");
    }

}
```
### 文件操作
### 获取文件
```
File file = ResourceUtils.getFile("classpath:cert/readme.txt");
```
### 获取路径
```
ClassUtils.getDefaultClassLoader().getResource("cert").getPath()
```
### Controller和RestController的区别？

官方文档：
@RestController is a stereotype annotation that combines @ResponseBody and @Controller.
意思是：
@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。
1)如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
例如：本来应该到success.jsp页面的，则其显示success.
2)如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
3)如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。


### 部署到JavaEE容器
修改启动类，继承 SpringBootServletInitializer 并重写 configure 方法
```
public class Application extends SpringBootServletInitializer{

    private static final Logger logger = LoggerFactory.getLogger(SpringBootSampleApplication.class);

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(this.getClass());
    }

}
```
修改pom文件中jar 为 war
```
<!-- <packaging>jar</packaging> -->
<packaging>war</packaging>
```
修改pom，排除tomcat插件
```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
</dependency>
```
最后，打包部署到容器。

打包排除
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <configuration>
        <excludes>
            <exclude>**/static/**</exclude>
            <exclude>**/templates/**</exclude>
        </excludes>
    </configuration>
</plugin>
```
### 指定外部的配置文件
有些系统，关于一些数据库或其他第三方账户等信息，由于安全问题，其配置并不会提前配置在项目中暴露给开发人员。 
对于这种情况，我们在运行程序的时候，可以通过参数指定一个外部配置文件。
以 itstyle.jar 为例，方法如下：
```
java -jar itstyle.jar --spring.config.location=/opt/config/application.properties
```
### 创建脚本
为了在linux下方便启动，停止项目
start.sh：

#!/bin/sh
```
		nohup java -jar $1  --server.port=$2  --spring.profiles.active=prod &

		echo 'Start Success!'
		执行方式：start.sh itstyle.jar 8886
		$1和$2分别为传入的参数itstyle.jar 和 8886

		stop.sh：

		#!/bin/sh

		tpid=`ps -ef|grep $1|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ "${tpid}" ]; then
			echo 'Stop Process...'
			kill -15 $tpid
		fi
		sleep 3
		tpid=`ps -ef|grep $1|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ "${tpid}" ]; then
			echo 'Kill Process!'
			kill -9 $tpid
		else
			echo 'Stop Success!'
		fi
```
