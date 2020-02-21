# Spring Boot

#### 配置文件

##### 多环境配置

```yaml
server:
  address: 192.168.1.100
spring:
  profiles:
    active: dev
---
spring:
  profiles: development
server:
  address: 127.0.0.1
---
spring:
  profiles: production & eu-central
server:
  address: 192.168.1.120
```



##### 注入属性

无法使用 `@PropertySource` 注解加载 YAML 文件

```java
@Configuration
@EnableConfigurationProperties(AcmeProperties.class)
public class MyConfiguration {
}

@Component
@ConfigurationProperties(prefix="acme")
public class AcmeProperties {

	// ... see the preceding example

}
```

```yaml
# application.yml

acme:
	remote-address: 192.168.1.1
	security:
		username: admin
		roles:
		  - USER
		  - ADMIN

# additional configuration as required
```

| 功能                                                         | `@ConfigurationProperties` | `@Value` |
| ------------------------------------------------------------ | -------------------------- | -------- |
| [宽松绑定](https://github.com/DocsHome/springboot/blob/master/pages/spring-boot-features.md#boot-features-external-config-relaxed-binding) | 是                         | 否       |
| [元数据支持](https://github.com/DocsHome/springboot/blob/master/pages/spring-boot-features.md#configuration-metadata) | 是                         | 否       |
| `SpEL` 表达式                                                | 否                         | 是       |

##### 日志

```properties
logging.level.root=WARN
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR

logging.group.tomcat=org.apache.catalina, org.apache.coyote, org.apache.tomcat

```

###### 预定义的日志记录组

| 名称 | 日志记录器                                                   |
| ---- | ------------------------------------------------------------ |
| web  | `org.springframework.core.codec`、`org.springframework.http`、`org.springframework.web` |
| sql  | `org.springframework.jdbc.core`、`org.hibernate.SQL`         |

###### 自定义日志配置文件

| 日志记录系统             | 文件                                                         |
| ------------------------ | ------------------------------------------------------------ |
| Logback                  | `logback-spring.xml`、`logback-spring.groovy`、`logback.xml` 或者 `logback.groovy` |
| Log4j2                   | `log4j2-spring.xml` 或者 `log4j2.xml`                        |
| JDK（Java Util Logging） | `logging.properties`                                         |



#### Spring MVC自动配置

自动配置在 Spring 默认功能上添加了以下功能：

- 引入 `ContentNegotiatingViewResolver` 和 `BeanNameViewResolver` bean。
- 支持服务静态资源，包括对 WebJar 的支持。
- 自动注册 `Converter`、`GenericConverter` 和 `Formatter` bean。
- 支持 `HttpMessageConverter`。
- 自动注册 `MessageCodesResolver`。
- 支持静态 index.html。
- 支持自定义 Favicon 。
- 自动使用 `ConfigurableWebBindingInitializer` bean。

如果您想保留 Spring Boot MVC 的功能，并且需要添加其他 [MVC 配置](https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#mvc)（interceptor、formatter 和视图控制器等），可以添加自己的 `WebMvcConfigurer` 类型的 `@Configuration` 类，但**不能**带 `@EnableWebMvc` 注解。

##### HttpMessageConverters

Spring MVC 使用 `HttpMessageConverter` 接口来转换 HTTP 的请求和响应。开箱即用功能包含了合适的默认值，比如对象可以自动转换为 JSON（使用 Jackson 库）或者 XML（优先使用 Jackson XML 扩展，其次为 JAXB）。字符串默认使用 `UTF-8` 编码。

自定义HttpMessageConverters

```java
@Configuration
public class MyConfiguration {
	@Bean
	public HttpMessageConverters customConverters() {
		HttpMessageConverter<?> additional = ...
		HttpMessageConverter<?> another = ...
		return new HttpMessageConverters(additional, another);
	}
}
```

##### 静态资源

默认情况下，Spring Boot 将在 classpath 或者 `ServletContext` 根目录下从名为 `/static` （`/public`、`/resources` 或 `/META-INF/resources`）目录中服务静态内容。它使用了 Spring MVC 的 `ResourceHttpRequestHandler`，因此您可以通过添加自己的 `WebMvcConfigurer` 并重写 `addResourceHandlers` 方法来修改此行为。

默认情况下，资源被映射到 `/**`，但可以通过 `spring.mvc.static-path-pattern` 属性调整。比如，将所有资源重定位到 `/resources/**`：

```properties
spring.mvc.static-path-pattern=/resources/**
```

##### 错误处理

默认情况下，Spring Boot 提供了一个使用了比较合理的方式来处理所有错误的 `/error` 映射，其在 servlet 容器中注册了一个**全局**错误页面。

要完全替换默认行为，您可以实现 `ErrorController` 并注册该类型的 bean，或者简单地添加一个类型为 `ErrorAttributes` 的 bean 来替换内容，但继续使用现用机制。

##### CORS

您可在 Spring Boot 应用程序中使用 [`@CrossOrigin`](https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#controller-method-cors-configuration) 注解配置[控制器方法启用 CORS](https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#controller-method-cors-configuration)。还可以通过注册一个 WebMvcConfigurer bean 并自定义 `addCorsMappings(CorsRegistry)` 方法来定义[全局 CORS 配置](https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#global-cors-configuration)：

```java
@Configuration
public class MyConfiguration {

	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/api/**");
			}
		};
	}
}
```



#### Servlet容器

##### 自定义Servlet容器

可以使用 Spring `Environment` 属性来配置通用的 servlet 容器设置。通常，您可以在 `application.properties` 文件中定义这些属性。

通用的服务器设置包括：

- 网络设置：监听 HTTP 请求的端口（`server.port`），绑定接口地址到 `server.address` 等。
- 会话设置：是否持久会话（`server.session.persistence`）、session 超时（`server.session.timeout`）、会话数据存放位置（`server.session.store-dir`）和 session-cookie 配置（`server.session.cookie.*`）。
- 错误管理：错误页面位置（`server.error.path`）等。
- SSL
- HTTP 压缩

```java
@Bean
public ConfigurableServletWebServerFactory webServerFactory() {
	TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
	factory.setPort(9000);
	factory.setSessionTimeout(10, TimeUnit.MINUTES);
	factory.addErrorPages(new ErrorPage(HttpStatus.NOT_FOUND, "/notfound.html"));
	return factory;
}
```



#### SQL

##### 配置

通过 `spring.datasource.type` 属性指定要使用的连接池。

```properties
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver   #可选
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
```

##### JdbcTemplate

Spring 的 `JdbcTemplate` 和 `NamedParameterJdbcTemplate` 类是自动配置的，您可以使用 `@Autowire` 将它们直接注入您的 bean

可以使用 `spring.jdbc.template.*` 属性来自定义一些 template 的属性



#### NoSQL

##### Redis

自动配置了 `RedisConnectionFactory`、`StringRedisTemplate` 和普通的 `RedisTemplate` 实例

##### MongoDB

自动配置了 `org.springframework.data.mongodb.MongoDbFactory` 来访问 Mongo 数据库

[Spring Data Mongo](https://projects.spring.io/spring-data-mongodb/) 提供了一个 [`MongoTemplate`](https://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/core/MongoTemplate.html) 类，它的设计与 Spring 的 `JdbcTemplate` 非常相似。与 `JdbcTemplate` 一样，Spring Boot 会自动配置一个 bean，以便您能注入模板



#### Cache

```java
@Bean
public CacheManagerCustomizer<ConcurrentMapCacheManager> cacheManagerCustomizer() {
	return new CacheManagerCustomizer<ConcurrentMapCacheManager>() {
		@Override
		public void customize(ConcurrentMapCacheManager cacheManager) {
			cacheManager.setAllowNullValues(false);
		}
	};
}
```

