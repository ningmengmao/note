# Spring MVC

#### 流程

![](./img/791227-20161125140338768-995727439.png)



#### DispatcherServlet

![](./img/mvc-context-hierarchy.png)

下表列出了`DispatcherHandler`检测到的特殊bean:

| Bean 类型                                 | 说明                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `HandlerMapping`                          | 将请求映射到处理程序以及用于预处理和后处理的[拦截器](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-handlermapping-interceptor)列表。 其映射规则基于某些标准，其细节因`HandlerMapping`实现而异。两个主要的`HandlerMapping`实现是`RequestMappingHandlerMapping`（它支持`@RequestMapping`带注解的方法） 和`SimpleUrlHandlerMapping` （它维护对处理程序的URI路径模式的显式注册）。 |
| `HandlerAdapter`                          | 无论实际调用处理程序如何，都可以帮助`DispatcherServlet` 调用映射到请求的处理程序。 例如，调用带有注解的控制器，需要从注解中解析一些信息。 `HandlerAdapter`的主要目的是保护`DispatcherServlet`不受此类细节的影响。 |
| `HandlerExceptionResolver`                | 解决异常的策略，他可以将捕获到的异常映射到处理程序，HTML错误视图或其他目标。 请参阅Exceptions |
| `ViewResolver`                            | 将从处理程序返回的逻辑基于`String`的视图名称解析为用于呈现给响应的实际`View`。 请参阅 View Resolution and View Technologies。 |
| `LocaleResolver`, `LocaleContextResolver` | 解析客户端正在使用的 `Locale`以及可能的时区，以便能够提供国际化视图。 请参阅Locale。 |
| `ThemeResolver`                           | 解决Web应用程序可以使用的主题 - 例如，提供个性化布局。 见Themes。 |
| `MultipartResolver`                       | 解析multi-part的请求（例如：浏览器表单文件上载）。请参阅Multipart Resolver。 |
| `FlashMapManager`                         | 存储和检索“input” 和“output”`FlashMap`，可用于将属性从一个请求传递到另一个请求，通常是通过重定向。 请参阅Flash Attributes。 |



#### Interception

Spring的处理器映射机制包含了处理器拦截器，可以实现 `HandlerMapping`，所有 `HandlerMapping`实现都支持处理拦截器，这些拦截器在需要为特定类型的请求应用一些功能时可能很有用非常有用， 例如，检查用户身份等，`org.springframework.web.servlet`包中的 `HandlerInterceptor`实现了三种方法，提供足够的灵活性来执行各种预处理和后处理：

- `preHandle(..)`: 在执行实际处理程序之前
- `postHandle(..)`: 在执行实际处理程序之后
- `afterCompletion(..)`: 完成请求后

请注意，在HandlerAdapter和postHandle之前，响应被写入并提交。 `postHandle`对于`@ResponseBody`和ResponseEntity方法不太有用， 这意味着对响应进行任何更改都为时已晚，例如添加额外的header。 对于此类方案，您可以实现 `ResponseBodyAdvice`并将其声明为`Controller Advice`或直接在`RequestMappingHandlerAdapter`上进行配置。



#### Exception

如果在请求映射期间发生异常或从请求处理程序（例如 `@Controller`）抛出异常， 则`DispatcherServlet`委托给 `HandlerExceptionResolver` bean来处理并解决异常，这通常是错误响应。

| `HandlerExceptionResolver`          | Description                                                  |
| ----------------------------------- | ------------------------------------------------------------ |
| `SimpleMappingExceptionResolver`    | 异常类名称和错误视图名称之间的映射。 用于在浏览器应用程序中呈现错误页面。 |
| `DefaultHandlerExceptionResolver`   | 解决Spring MVC引发的异常并将它们映射到HTTP状态代码。 另请参阅备用`ResponseEntityExceptionHandler`和REST API exceptions异常。 |
| `ResponseStatusExceptionResolver`   | 使用`@ResponseStatus`注解解析异常，并根据注解中的值将它们映射到HTTP状态代码。 |
| `ExceptionHandlerExceptionResolver` | 通过在`@Controller`或 `@ControllerAdvice`类中调用 `@ExceptionHandler`方法来解决异常。 请参阅@ExceptionHandler methods方法。 |



#### 视图解析

Spring MVC定义了 `ViewResolver`和 `View`接口，使您可以在浏览器中呈现模型，而无需将您与特定的视图技术联系起来。 `ViewResolver`提供视图名称和实际视图之间的映射。 `View`接口负责准备请求，并将请求的渲染交给某种具体的视图技术实现。

| ViewResolver                     | Description                                                  |
| -------------------------------- | ------------------------------------------------------------ |
| `AbstractCachingViewResolver`    | `AbstractCachingViewResolver` 的子类缓存它们解析的视图实例。 缓存可提高某些视图技术的性能。 您可以通过将`cache`属性设置为 `false`.来关闭缓存。 此外，如果必须在运行时刷新某个视图（例如，修改FreeMarker模板时），则可以使用`removeFromCache(String viewName, Locale loc)` 方法。 |
| `XmlViewResolver`                | 实现`ViewResolver`，它必须和Spring的XML bean工厂有相同的DTD以。 默认配置文件是`/WEB-INF/views.xml`。 |
| `ResourceBundleViewResolver`     | `ViewResolver`的实现，它使用由bundle根路径指定的`ResourceBundle`中的bean定义作为配置。 对于它应该解析的每个视图，它使用属性`[viewname].(class)`的值作为视图类， 并使用属性 `[viewname].url` 的值作为视图URL。 您可以在 [视图技术](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-view)一章中找到示例。 |
| `UrlBasedViewResolver`           | `ViewResolver`接口的简单实现，它不需要其他任何显式的映射说明，而直接使用URL来解析到逻辑视图名。 如果您的逻辑名称与真正的视图资源的名称匹配，则不需要任何映射。 |
| `InternalResourceViewResolver`   | `UrlBasedViewResolver`的便捷子类，支持 `InternalResourceView`（实际上是Servlet和JSP）和子类，如`JstlView` 和 `TilesView`。 您可以使用`setViewClass(..)`为此解析程序生成的所有视图指定视图类。 有关详细信息，请参阅[`UrlBasedViewResolver`](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/web/reactive/result/view/UrlBasedViewResolver.html)javadoc。 |
| `FreeMarkerViewResolver`         | `UrlBasedViewResolver` 的便捷子类，支持 `FreeMarkerView`及其自定义子类。 |
| `ContentNegotiatingViewResolver` | 实现 `ViewResolver` 接口，该接口根据请求文件名或Accept头解析视图。 请参阅 [Content Negotiation](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-multiple-representations)。 |



##### 重定向

您可以在视图中使用`redirect:`前缀来执行重定向。`UrlBasedViewResolver`（及其子类）将此识别为需要重定向的指令。 视图名称的其余部分是重定向URL。

控制器本身可以根据逻辑视图名称进行操作。 逻辑视图名称（例如`redirect:/myapp/some/resource`）相对于当前Servlet上下文重定向，而名称如`redirect:http://myhost.com/some/arbitrary/path`重定向到绝对URL。

请注意，如果使用`@ResponseStatus`注解控制器方法，则注解值优先于 `RedirectView`设置的响应状态。



##### 转发

你也可以在视图名称中使用`forward:`前缀，来作为 `UrlBasedViewResolver`和其子类最终解析的视图名称。 这将创建一个 `InternalResourceView`，它执行`RequestDispatcher.forward()`。 因此，此前缀对于 `InternalResourceViewResolver`和`InternalResourceView`（对于JSP）没有用，但如果您使用其他视图技术时仍希望强制Servlet/JSP引擎处理资源的转发，则它可能会有所帮助。 请注意，您也可以链接多个视图解析器。



#### 请求处理

##### Request Mapping

`@RequestMapping`注解用于将请求映射到控制器方法。它具有各种属性，可以通过URL、HTTP方法、请求参数、请求头参数（headers）和媒体类型进行匹配。 可以在类级别使用它来表示共享映射，或在方法级别上用于缩小到特定的端点映射范围。

还有`@RequestMapping`的HTTP方法特定的缩写变量:

- `@GetMapping`
- `@PostMapping`
- `@PutMapping`
- `@DeleteMapping`
- `@PatchMapping`

##### URL模式匹配

你可以使用glob模式和通配符来映射请求:

- `?` 匹配一个字符
- `*` 匹配路径段一个或多个字符
- `**` 匹配0个或多个路径段

您还可以使用 `@PathVariable`声明URI变量并访问它们的值，如以下示例所示:

```java
@GetMapping("/owners/{ownerId}/pets/{petId}")
public Pet findPet(@PathVariable Long ownerId, @PathVariable Long petId) {
    // ...
}
```

语法`{varName:regex}`声明一个具有正则表达式的URI变量，其语法为`{varName:regex}`。例如，给定URL`"/spring-web-3.0.5 .jar"`，以下方法提取名称，版本和文件扩展名:

```java
@GetMapping("/{name:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{ext:\\.[a-z]+}")
public void handle(@PathVariable String version, @PathVariable String ext) {
    // ...
}
```



```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {

	String name() default "";

	@AliasFor("path")
	String[] value() default {};

	@AliasFor("value")
	String[] path() default {};

	RequestMethod[] method() default {};

	String[] params() default {};

	String[] headers() default {};

	String[] consumes() default {};

	String[] produces() default {};
}
```



##### 方法参数

| 控制器方法参数                                               | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `WebRequest`, `NativeWebRequest`                             | 无需直接使用Servlet API即可访问请求参数以及request和session属性。 |
| `javax.servlet.ServletRequest`, `javax.servlet.ServletResponse` | 选择任何特定的请求或响应类型 - 例如，`ServletRequest`, `HttpServletRequest`或Spring的`MultipartRequest`, `MultipartHttpServletRequest`。 |
| `javax.servlet.http.HttpSession`                             | 强制进行会话。 因此，此类参数永远不可能为`null`. 请注意，会话访问不是线程安全的。 如果允许多个请求同时访问会话，请考虑将`RequestMappingHandlerAdapter`实例的`synchronizeOnSession`标志设置为true。 |
| `javax.servlet.http.PushBuilder`                             | Spring4.0 push生成器API用于编程HTTP/2资源推送， 请注意，根据Servlet规范，如果客户端不支持该HTTP/2功能，则注入的`PushBuilder`实例可以为null。 |
| `java.security.Principal`                                    | 当前经过身份验证的用户 - 如果已知，可能是特定的`Principal`实现类。 |
| `HttpMethod`                                                 | 请求的HTTP方法。                                             |
| `java.util.Locale`                                           | 当前请求区域设置，由最可用的`LocaleResolver`（实际上是已配置的 `LocaleResolver`或`LocaleContextResolver`）确定。 |
| `java.util.TimeZone` + `java.time.ZoneId`                    | 与当前请求关联的时区，由`LocaleContextResolver`确定。        |
| `java.io.InputStream`, `java.io.Reader`                      | 用于访问Servlet API公开的原始请求主体。                      |
| `java.io.OutputStream`, `java.io.Writer`                     | 用于访问Servlet API公开的原始响应主体。                      |
| `@PathVariable`                                              | 用于访问URI模板变量。 请参阅[URI模式](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestmapping-uri-templates)。 |
| `@MatrixVariable`                                            | 用于访问URI路径段中的名称 - 值对。 请参见[矩阵变量](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-matrix-variables)。 |
| `@RequestParam`                                              | 用于访问Servlet请求参数，包括多部分文件。 参数值将转换为声明的方法参数类型。 请参阅[`@RequestParam`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestparam)以及[Multipart](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-multipart-forms)。 请注意，对于简单的参数值，使用`@RequestParam`是可选的。 请参阅本表末尾的“任何其他参数”。 |
| `@RequestHeader`                                             | 用于访问请求头。 头的值将转换为声明的方法参数类型。 请参阅[`@RequestHeader`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestheader)。 |
| `@CookieValue`                                               | 用于访问cookie。 Cookie值将转换为声明的方法参数类型。 请参阅[`@CookieValue`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-cookievalue)。 |
| `@RequestBody`                                               | 用于访问HTTP请求正文。 通过使用`HttpMessageConverter`实现将正文内容转换为声明的方法参数类型。 请参阅 [`@RequestBody`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestbody)。 |
| `HttpEntity`                                                 | 用于访问请求标头和正文。 使用`HttpMessageConverter`转换正文。 见[HttpEntity](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-httpentity)。 |
| `@RequestPart`                                               | 要访问`multipart/form-data`请求中的部件，请使用`HttpMessageConverter`转换部件的主体。 见[Multipart](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-multipart-forms)。 |
| `java.util.Map`, `org.springframework.ui.Model`, `org.springframework.ui.ModelMap` | 用于访问HTML控制器中使用的模型，并将其作为视图呈现的一部分暴露给模板。 |
| `RedirectAttributes`                                         | 指定在重定向（即，要附加到查询字符串）时使用的属性，以及临时存储的flash属性，直到重定向后的请求为止。 请参阅 [重定向属性](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-redirecting-passing-data)和[Flash属性](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-flash-attributes)。 |
| `@ModelAttribute`                                            | 用于访问模型中的现有属性（如果不存在则实例化），并应用数据绑定和验证。 请参阅[`@ModelAttribute`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-modelattrib-method-args) 以及[Model](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-modelattrib-methods)和[`DataBinder`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-initbinder)。请注意，使用`@ModelAttribute` 是可选的（例如，设置其属性）。 请参阅本表末尾的“任何其他参数”。 |
| `Errors`, `BindingResult`                                    | 用于访问来自命令对象的验证和数据绑定的错误（即 `@ModelAttribute`参数）或来自验证 `@RequestBody`或 `@RequestPart` 参数的错误。 您必须在经过验证的方法参数后立即声明`Errors`或`BindingResult`参数。 |
| `SessionStatus` + class-level `@SessionAttributes`           | 用于标记表单处理完成，从而触发通过类级别`@SessionAttributes`注解声明的会话属性的清除。 有关更多详细信息，请参阅[`@SessionAttributes`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-sessionattributes)。 |
| `UriComponentsBuilder`                                       | 用于准备相对于当前请求的主机，端口，方案，上下文路径和servlet映射的文字部分的URL。 请参阅[URI Links](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-uri-building)。 |
| `@SessionAttribute`                                          | 用于访问任何会话属性，与由于类级别`@SessionAttributes`声明的结束形成对比。 有关更多详细信息，请参阅 [`@SessionAttribute`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-sessionattribute)。 |
| `@RequestAttribute`                                          | 用于访问请求属性。 有关更多详细信息，请参阅[`@RequestAttribute`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestattrib)。 |
| Any other argument                                           | 如果方法参数与此表中的任何值不匹配，并且它是一个简单类型（由 [BeanUtils#isSimpleProperty](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-)确定， 则它被解析为 `@RequestParam`。否则，它将被解析为`@ModelAttribute`。 |



##### 响应结果

| 控制器方法返回值                                             | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `@ResponseBody`                                              | 返回值通过`HttpMessageConverter`实现转换并写入响应。 请参阅[`@ResponseBody`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-responsebody)。 |
| `HttpEntity`, `ResponseEntity`                               | 指定完整响应（包括HTTP头和主体）的返回值将通过 `HttpMessageConverter`实现转换并写入响应。 请参阅[ResponseEntity](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-responseentity)。 |
| `HttpHeaders`                                                | 用于返回带头部信息且没有正文的响应。                         |
| `String`                                                     | 要使用`ViewResolver`实现解析的视图名称，并与隐式模型一起使用 - 通过命令对象和 `@ModelAttribute`方法确定。 处理程序方法还可以通过声明 `Model`参数以编程方式丰富模型（请参阅 [显式注册](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestmapping-registration)）。 |
| `View`                                                       | 用于与隐式模型一起呈现的`View`实例 - 通过命令对象和`@ModelAttribute`方法确定。 处理程序方法还可以通过声明`Model`参数以编程方式丰富模型（请参阅 [显式注册](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestmapping-registration)）。 |
| `java.util.Map`, `org.springframework.ui.Model`              | 要添加到隐式模型的属性，通过`RequestToViewNameTranslator`隐式确定视图名称。 |
| `@ModelAttribute`                                            | 要添加到模型的属性，通过`RequestToViewNameTranslator`隐式确定视图名称。请注意，`@ModelAttribute`是可选的。 请参阅本表末尾的“任何其他返回值”。 |
| `ModelAndView` object                                        | 要使用的视图和模型属性，以及（可选）响应状态。               |
| `void`                                                       | 如果具有`void`返回类型（或返回值为 `null` ）的方法，如果它还具有`ServletResponse`，`OutputStream`参数或`@ResponseStatus`注解， 则认为已完全处理该响应。 如果控制器已进行正`ETag`或`lastModified` 时间戳检查，则也是如此（有关详细信息，请参阅[Controllers](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-caching-etag-lastmodified)）。如果以上都不是真的，则`void`返回类型也可以指示REST控制器的“无响应主体”或HTML控制器的默认视图名称选择。 |
| `DeferredResult`                                             | 从任何线程异步生成任何前面的返回值 - 例如，由于某些事件或回调。 请参阅[Asynchronous Requests](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async) 和 [`DeferredResult`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async-deferredresult). |
| `Callable`                                                   | 在Spring MVC管理的线程中异步生成上述任何返回值。 请参阅 [Asynchronous Requests](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async) 和 [`Callable`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async-callable). |
| `ListenableFuture`, `java.util.concurrent.CompletionStage`, `java.util.concurrent.CompletableFuture` | 作为替代`DeferredResult`的便捷操作（例如，当底层服务返回其中一个时）。 |
| `ResponseBodyEmitter`, `SseEmitter`                          | 使用`HttpMessageConverter`实现以异步方式发送对象流以写入响应。 还支持`ResponseEntity`的主体。 请参阅[Asynchronous Requests](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async) and [HTTP Streaming](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async-http-streaming). |
| `StreamingResponseBody`                                      | 异步写入响应`OutputStream`。 还支持`ResponseEntity`的主体。 请参阅[Asynchronous Requests](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async) and [HTTP Streaming](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async-http-streaming). |
| Reactive types — Reactor, RxJava, or others through `ReactiveAdapterRegistry` | 使用multi-value流（例如，`Flux`, `Observable`）替代`DeferredResult`收集到`List`中。对于流式场景(例如, `text/event-stream`, `application/json+stream`), `SseEmitter` 和 `ResponseBodyEmitter` 使用的是在Spring MVC 管理的线程上执行`ServletOutputStream`阻塞I/O，这是 针对每一个Write的。请参阅 [Asynchronous Requests](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async) 和 [Reactive Types](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-async-reactive-types). |
| Any other return value                                       | 任何与此表中任何早期值不匹配且返回值为`String` 或`void`的返回值都被视为视图名称（通过`RequestToViewNameTranslator`应用默认视图名称选择）， 前提是它不是简单类型，由 [BeanUtils#isSimpleProperty](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-)确定，简单类型的值仍未解决。 |



##### 类型转换

可以通过`WebDataBinder`（请参阅[`DataBinder`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-initbinder)）或使用`FormattingConversionService`注册`Formatters`来自定义类型转换。 请参见 [Spring Field Formatting](https://github.com/DocsHome/spring-docs/blob/master/pages/web/core.html#format)。



##### 矩阵变量

[RFC 3986](https://tools.ietf.org/html/rfc3986#section-3.3)讨论了路径段中的携带键值对。 在Spring MVC中，我们将那些基于Tim Berners-Lee的 [“old post”](https://www.w3.org/DesignIssues/MatrixURIs.html) 称为“矩阵变量”，但它们也可以称为URI路径参数。

矩阵变量可以在任意路径段落中出现，每对矩阵变量之间使用分号隔开，多个值可以用逗号隔开（例如，`/cars;color=red,green;year=2012`）， 也可以通过重复的变量名称指定多个值（例如，`color=red;color=green;color=blue`）。

如果URL有可能会包含矩阵变量，那么在请求路径的映射配置上就需要使用URI模板来体现。这样才能确保请求可以被正确地映射，而不管矩阵变量在URI中是否出现、出现的次序是怎样的等。以下示例使用矩阵变量：

```java
// GET /pets/42;q=11;r=22

@GetMapping("/pets/{petId}")
public void findPet(@PathVariable String petId, @MatrixVariable int q) {

    // petId == 42
    // q == 11
}
```

由于任意路径段落中都可以含有矩阵变量，在某些场景下，开发者需要用更精确的信息来指定矩阵变量的位置。以下示例说明如何执行此操作：

```java
// GET /owners/42;q=11/pets/21;q=22

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable(name="q", pathVar="ownerId") int q1,
        @MatrixVariable(name="q", pathVar="petId") int q2) {

    // q1 == 11
    // q2 == 22
}
```

矩阵变量可以定义为可选，并指定默认值，如以下示例所示：

```java
// GET /pets/42

@GetMapping("/pets/{petId}")
public void findPet(@MatrixVariable(required=false, defaultValue="1") int q) {

    // q == 1
}
```

要获取所有矩阵变量，可以使用`MultiValueMap`，如以下示例所示:

```java
// GET /owners/42;q=11;r=12/pets/21;q=22;s=23

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable MultiValueMap<String, String> matrixVars,
        @MatrixVariable(pathVar="petId") MultiValueMap<String, String> petMatrixVars) {

    // matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]
    // petMatrixVars: ["q" : 22, "s" : 23]
}
```

请注意，您需要启用矩阵变量的使用。 在MVC Java配置中，您需要通过 [路径匹配](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-config-path-matching)将`removeSemicolonContent=false` 设置为`UrlPathHelper`。 在MVC XML命名空间中，您可以设置``。



##### @RequestParam

您可以使用`@RequestParam`注解将Servlet请求参数（即查询参数或表单数据）绑定到控制器中的方法参数。

以下示例显示了如何执行此操作：

```java
@Controller
@RequestMapping("/pets")
public class EditPetForm {
    @GetMapping
    public String setupForm(@RequestParam("petId") int petId, Model model) { (1)
        Pet pet = this.clinic.loadPet(petId);
        model.addAttribute("pet", pet);
        return "petForm";
    }
}
```

##### @RequestHeader

```java
@GetMapping("/demo")
public void handle(
        @RequestHeader("Accept-Encoding") String encoding, 
        @RequestHeader("Keep-Alive") long keepAlive) { 
    //...
}
```

##### @CookieValue

```java
@GetMapping("/demo")
public void handle(@CookieValue("JSESSIONID") String cookie) { (1)
    //...
}
```

##### @SessionAttribute

如果需要访问已存在的被全局session属性，例如在控制器之外（如通过过滤器）的（可有可无），请在方法参数上使用`@SessionAttribute`注解：

```java
@RequestMapping("/")
public String handle(@SessionAttribute User user) { 
    // ...
}
```

##### @Multipart

启用`MultipartResolver`后，将解析具有`multipart/form-data`的POST请求的内容，并将其作为常规请求参数进行访问。 以下示例访问一个常规表单字段和一个上载文件：

```java
@Controller
public class FileUploadController {

    @PostMapping("/form")
    public String handleFormUpload(@RequestParam("name") String name,
            @RequestParam("file") MultipartFile file) {

        if (!file.isEmpty()) {
            byte[] bytes = file.getBytes();
            // store the bytes somewhere
            return "redirect:uploadSuccess";
        }
        return "redirect:uploadFailure";
    }
}
```

将参数类型声明为`List<MultipartFile>`允许为同一参数名称解析多个文件。

##### @RequestBody

您可以使用`@RequestBody`注解通过[`HttpMessageConverter`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/integration.html#rest-message-conversion)将请求主体读取并反序列化为`Object`。 以下示例使用`@RequestBody`参数:

```java
@PostMapping("/accounts")
public void handle(@RequestBody Account account) {
    // ...
}
```

##### @ResponseBody

可以在方法上使用`@ResponseBody`注解，以通过[HttpMessageConverter](https://github.com/DocsHome/spring-docs/blob/master/pages/web/integration.html#rest-message-conversion)将返回**序列化**到响应主体。 以下清单显示了一个示例:

```java
@GetMapping("/accounts/{id}")
@ResponseBody
public Account handle() {
    // ...
}
```



#### 数据绑定

`@Controller`或`@ControllerAdvice`类可以使用`@InitBinder` 方法初始化`WebDataBinder`的实例，而这些方法又可以：

- 将请求参数（即表单或查询数据）绑定到模型对象。
- 将基于字符串的请求值（例如请求参数，路径变量，请求头，cookie等）转换为目标类型的控制器方法参数。
- 在呈现HTML表单时将模型对象值格式化为`String`值。

`@InitBinder`方法可以注册特定于控制器的`java.bean.PropertyEditor`或Spring `Converter`和`Formatter`组件。 此外，您可以使用[MVC config](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-config-conversion)在全局共享的`FormattingConversionService`中注册`Converter`和`Formatter`类型。

`@InitBinder` 方法支持许多与`@RequestMapping`方法相同的参数，但`@ModelAttribute`（命令对象）参数除外。 通常，它们使用`WebDataBinder`参数（用于注册）和void返回值进行声明。 以下清单显示了一个示例:

```java
@Controller
public class FormController {
    @InitBinder 
    public void initBinder(WebDataBinder binder) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
        dateFormat.setLenient(false);
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
    }
    // ...
}
```



#### 异常

`@Controller` 和 [@ControllerAdvice](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-controller-advice)可以使用`@ExceptionHandler`方法来处理来自控制器方法的异常，如下例所示：

```java
@Controller
public class SimpleController {

    // ...

    @ExceptionHandler({FileSystemException.class, RemoteException.class})
    public ResponseEntity<String> handle(Exception ex) {
        // ...
    }
}
```

@ExceptionHandler的方法参数如下

| 方法 参数                                                    | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Exception type                                               | 用于访问引发的异常。                                         |
| `HandlerMethod`                                              | 访问控制器方法引发的异常                                     |
| `WebRequest`, `NativeWebRequest`                             | 无需直接使用Servlet API即可访问请求参数以及请求和会话属性。  |
| `javax.servlet.ServletRequest`, `javax.servlet.ServletResponse` | 选择任何特定的请求或响应类型（例如，`ServletRequest` or `HttpServletRequest` or Spring `MultipartRequest` or `MultipartHttpServletRequest`). |
| `javax.servlet.http.HttpSession`                             | 强制进行会话。 因此，这样的结果永远不会是`null`的。请注意，会话访问不是线程安全的。 如果允许多个请求同时访问会话，请考虑将`RequestMappingHandlerAdapter`实例的`synchronizeOnSession`标志设置为`true`。 |
| `java.security.Principal`                                    | 当前经过身份验证的用户 - 如果已知，可能是特定的 `Principal`实现类。 |
| `HttpMethod`                                                 | 请求的HTTP方法。                                             |
| `java.util.Locale`                                           | 当前请求区域设置，由最可用的 `LocaleResolver`（实际上是已配置的 `LocaleResolver`或`LocaleContextResolver`）确定。 |
| `java.util.TimeZone`, `java.time.ZoneId`                     | 与当前请求关联的时区，由`LocaleContextResolver`确定。        |
| `java.io.OutputStream`, `java.io.Writer`                     | 用于访问Servlet API公开的原始响应主体。                      |
| `java.util.Map`, `org.springframework.ui.Model`, `org.springframework.ui.ModelMap` | 用于访问模型以获取错误响应。 总是为空.                       |
| `RedirectAttributes`                                         | 指定在重定向的情况下使用的属性 - （将附加到查询字符串）和临时存储的flash属性，直到重定向后的请求为止。 请参阅[Redirect 属性](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-redirecting-passing-data)和[Flash 属性](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-flash-attributes)。 |
| `@SessionAttribute`                                          | 用于访问任何会话属性，与由于类级别`@SessionAttributes`声明的结束形成对比。 有关更多详细信息，请参阅[`@SessionAttribute`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-sessionattribute)。 |
| `@RequestAttribute`                                          | 用于访问请求属性。 有关更多详细信息，请参阅[`@RequestAttribute`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestattrib)。 |

`@ExceptionHandler`方法支持以下返回值:

| 返回值                                          | 描述                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| `@ResponseBody`                                 | 返回值通过`HttpMessageConverter`实现转换并写入响应。 请参阅[`@ResponseBody`](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-responsebody)。 |
| `HttpEntity`, `ResponseEntity`                  | 指定完整响应（包括HTTP头和主体）的返回值将通过 `HttpMessageConverter`实现转换并写入响应。 请参阅[ResponseEntity](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-responseentity)。 |
| `String`                                        | 要使用`ViewResolver`实现解析的视图名称，并与隐式模型一起使用 - 通过命令对象和 `@ModelAttribute`方法确定。 处理程序方法还可以通过声明 `Model`参数以编程方式丰富模型（请参阅 [显式注册](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestmapping-registration)）。 |
| `View`                                          | 用于与隐式模型一起呈现的`View`实例 - 通过命令对象和`@ModelAttribute`方法确定。 处理程序方法还可以通过声明`Model`参数以编程方式丰富模型（请参阅 [显式注册](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-ann-requestmapping-registration)）。 |
| `java.util.Map`, `org.springframework.ui.Model` | 要添加到隐式模型的属性，通过`RequestToViewNameTranslator`隐式确定视图名称。 |
| `@ModelAttribute`                               | 要添加到模型的属性，通过隐式确定视图名称。请注意，`@ModelAttribute`是可选的。 请参阅本表末尾的“任何其他返回值”。 |
| `ModelAndView` object                           | 要使用的视图和模型属性，以及（可选）响应状态。               |
| `void`                                          | 如果具有`void`返回类型（或返回值为 `null` ）的方法，如果它还具有`ServletResponse`，`OutputStream`参数或`@ResponseStatus`注解， 则认为已完全处理该响应。 如果控制器已进行正`ETag`或`lastModified` 时间戳检查，则也是如此（有关详细信息，请参阅[Controllers](https://github.com/DocsHome/spring-docs/blob/master/pages/web/mvc.md#mvc-caching-etag-lastmodified)）。 如果以上都不是真的，则`void`返回类型也可以指示REST控制器的“无响应主体”或HTML控制器的默认视图名称选择。 |
| Any other return value                          | 任何与此表中任何早期值不匹配且返回值为`String` 或`void`的返回值都被视为视图名称（通过`RequestToViewNameTranslator`应用默认视图名称选择）， 前提是它不是简单类型，由 [BeanUtils#isSimpleProperty](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-)确定，简单类型的值仍未解决。 |



##### REST API异常

REST服务的一个常见要求是在响应正文中包含错误详细信息。 Spring Framework不会自动执行此操作，因为响应正文中的错误详细信息的表示是特定于应用程序的。 但是，`@RestController`可以使用带有ResponseEntity返回值的`@ExceptionHandler`方法来设置响应的状态和正文。 这些方法也可以在`@ControllerAdvice`类中声明，以全局应用它们。

在响应主体中实现具有错误详细信息的全局异常处理的应用程序应考虑扩展[`ResponseEntityExceptionHandler`](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/ResponseEntityExceptionHandler.html)， 它提供对Spring MVC引发的异常的处理，并提供钩子来定制响应主体。要使用它，请创建`ResponseEntityExceptionHandler`的子类，使用`@ControllerAdvice`注解它，覆盖必要的方法，并将其声明为Spring bean。



#### Controller Advice

@ControllerAdvice, @RestControllerAdvice

默认情况下，`@ControllerAdvice`方法适用于每个请求（即所有控制器），但您可以通过使用注解上的属性将其缩小到控制器的子集，如以下示例所示：

```java
// Target all Controllers annotated with @RestController
@ControllerAdvice(annotations = RestController.class)
public class ExampleAdvice1 {}

// Target all Controllers within specific packages
@ControllerAdvice("org.example.controllers")
public class ExampleAdvice2 {}

// Target all Controllers assignable to specific classes
@ControllerAdvice(assignableTypes = {ControllerInterface.class, AbstractController.class})
public class ExampleAdvice3 {}
```



#### CORS

##### @CrossOrigin

在带注解的控制器方法上使用[`@CrossOrigin`](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/web/bind/annotation/CrossOrigin.html)注解启用跨源请求，如以下示例所示：

```java
@RestController
@RequestMapping("/account")
public class AccountController {

    @CrossOrigin
    @GetMapping("/{id}")
    public Account retrieve(@PathVariable Long id) {
        // ...
    }

    @DeleteMapping("/{id}")
    public void remove(@PathVariable Long id) {
        // ...
    }
}
```

默认情况下，`@CrossOrigin`允许：

- All origins.
- All headers.
- All HTTP methods（可以映射到控制器的方法）

`@CrossOrigin`在**类级**别也受支持，并且由所有方法继承

您可以在类级别和方法级别使用`@CrossOrigin` ，如以下示例所示:

```java
@CrossOrigin(origins = "http://domain2.com", maxAge = 3600)
@RestController
@RequestMapping("/account")
public class AccountController {

    @CrossOrigin("http://domain3.com")
    @GetMapping("/{id}")
    public Account retrieve(@PathVariable Long id) {
        // ...
    }

    @DeleteMapping("/{id}")
    public void remove(@PathVariable Long id) {
        // ...
    }
}
```



##### 全局配置

默认情况下，全局配置启用以下内容：

- All origins.
- All headers.
- `GET`, `HEAD`, and `POST` methods.

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {

        registry.addMapping("/api/**")
            .allowedOrigins("http://domain2.com")
            .allowedMethods("PUT", "DELETE")
            .allowedHeaders("header1", "header2", "header3")
            .exposedHeaders("header1", "header2")
            .allowCredentials(true).maxAge(3600);

        // Add more mappings...
    }
}
```

##### CORS过滤器

通过内置的 [`CorsFilter`](https://docs.spring.io/spring-framework/docs/5.1.3.RELEASE/javadoc-api/org/springframework/web/filter/CorsFilter.html)应用CORS支持。

要配置过滤器，请将`CorsConfigurationSource`传递给其构造函数，如以下示例所示:

```java
CorsConfiguration config = new CorsConfiguration();

// Possibly...
// config.applyPermitDefaultValues()

config.setAllowCredentials(true);
config.addAllowedOrigin("http://domain1.com");
config.addAllowedHeader("*");
config.addAllowedMethod("*");

UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
source.registerCorsConfiguration("/**", config);

CorsFilter filter = new CorsFilter(source);
```



#### MVC配置

##### 启用MVC配置

```
@Configuration
@EnableWebMvc
public class WebConfig {
}
```

##### MVC配置API

在Java配置中，您可以实现`WebMvcConfigurer`接口，如以下示例所示:

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    // Implement configuration methods...
}
```

##### 拦截器

在Java配置中，注册拦截器应用于传入的请求。如以下示例所示：

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LocaleChangeInterceptor());
        registry.addInterceptor(new ThemeChangeInterceptor()).addPathPatterns("/**").excludePathPatterns("/admin/**");
        registry.addInterceptor(new SecurityInterceptor()).addPathPatterns("/secure/*");
    }
}
```

##### Validator

默认情况下，如果类路径上存在[Bean Validation](https://github.com/DocsHome/spring-docs/blob/master/pages/web/core.html#validation-beanvalidation-overview)(例如Hibernate Validator），则 `LocalValidatorFactoryBean`将注册为全局[Validator](https://github.com/DocsHome/spring-docs/blob/master/pages/web/core.html#validator) 。 以便与 `@Valid` 和 `Validated` 一起使用并在控制器方法参数上进行验证。

在Java配置中，您可以自定义全局`Validator`实例，如以下示例所示：

```
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public Validator getValidator(); {
        // ...
    }
}
```