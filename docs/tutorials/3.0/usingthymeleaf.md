---
title: 'Tutorial: Using Thymeleaf'
author: The Thymeleaf Team
version: @documentVersion@
thymeleafVersion: @projectVersion@
---




1 Thymeleaf 简介
=======================



1.1 Thymeleaf 是什么？
----------------------

Thymeleaf是用于Web和独立环境的现代服务器端Java模板引擎。

Thymeleaf的主要目标是将优雅的自然模板带到您的开发工作流程中—HTML能够在浏览器中正确显示，并且可以作为静态原型，从而在开发团队中实现更强大的协作。Thymeleaf能够处理HTML，XML，JavaScript，CSS甚至纯文本。

Thymeleaf的主要目标是提供一个优雅和高度可维护的创建模板的方式。 为了实现这一点，它建立在 *自然模板* 的概念之上，以不影响模板作为设计原型的方式将其逻辑注入到模板文件中。 这改善了设计沟通，弥合了前端设计和开发人员之间的理解偏差。

Thymeleaf也是从一开始就设计(特别是 **HTML5**)允许创建完全验证的模板。


1.2 Thymeleaf 可以处理什么样的模板？
-------------------------------------------------

开箱即用，Thymeleaf 可以处理六种模板，每种模板都称为 **模板模式**:

 * HTML
 * XML
 * TEXT
 * JAVASCRIPT
 * CSS
 * RAW

有两种 *标记* 模板模式(`HTML` 和 `XML`)，三种 *文本* 模板模式(`TEXT`，`JAVASCRIPT` 和 `CSS`)和一种 *无操作* 模板模式(`RAW`)。

**HTML** 模板模式将允许任何类型的 HTML 输入，包括 HTML5，HTML4 和 XHTML。 将不会执行验证或格式检查，并且在输出中尽可能地遵守模板代码/结构。

**XML** 模板模式将允许 XML 输入。 在这种情况下，代码应该是格式良好的 - 没有未封闭的标签，没有未加引号的属性等等，如果发现格式错误，解析器将会抛出异常。 请注意，将不会执行验证(针对 DTD 或 XML 模式)。

**TEXT** 模板模式将允许对非标记性质的模板使用特殊语法。 这种模板的例子可能是文本电子邮件或模板文档。 请注意，HTML 或 XML 模板也可以作为 TEXT 处理，在这种情况下，它们不会被解析为标记，而每个标记，DOCTYPE，注释等都将被视为纯文本。

**JAVASCRIPT** 模板模式将允许处理 Thymeleaf 应用程序中的 JavaScript 文件。这意味着能够像在 HTML 文件中一样使用 JavaScript 文件中的模型数据，但是使用特定于 JavaScript 的集成(例如专门转义或自然脚本)。 JAVASCRIPT 模板模式被认为是文本模式，因此使用与 TEXT 模板模式相同的特殊语法。

**CSS** 模板模式将允许处理 Thymeleaf 应用程序中涉及的 CSS 文件。类似于 JAVASCRIPT 模式，CSS 模板模式也是文本模式，并使用 TEXT 模板模式中的特殊处理语法。

**RAW** 模板模式根本不会处理模板。它意味着用于将未触及的资源(文件，URL响应等)插入正在处理的模板中。例如，可以将 HTML 格式的外部非受控资源包含在应用程序模板中，从而安全地知道这些资源可能包含的任何Thymeleaf代码都不会被执行。


1.3 方言：标准方言
----------------------------------
Thymeleaf 是一个非常容易扩展的模板引擎(事实上，它可以被称为模板引擎框架)，允许您定义和定制模板将被处理到一个很好的细节水平的方式。

一个将某些逻辑应用于标记工件(标签，一些文本，注释或者仅仅是模板不是标记的占位符)的对象被称为 _处理器_，而这些处理器的集合(可能还有一些额外的工件)是方言组成。 开箱即用，Thymeleaf 的核心库提供了一种称为 **标准方言** 的 **方言**，对大多数用户来说应该是足够的。

> 请注意，方言实际上可以没有处理器，并且完全由其组成
> 但是处理器绝对是最常见的用例。

本教程涵盖了标准方言。 您将在后面的页面中了解的每个属性和语法功能均由该方言定义，即使未明确提及。

当然，如果用户想利用库的高级功能定义自己的处理逻辑，用户可以创建自己的方言(甚至扩展标准方言)。Thymeleaf 也可以配置为一次使用几种方言。

> 官方的 thymeleaf-spring3 和 thymeleaf-spring4 集成包都定义一个称为 “SpringStandard 方言”的方言
> 它大部分和标准方言是一样的,但为了更好地利用 Spring 框架的一些特性(例如,通过使用 Spring 表达式语言或 SpringEL 代替 OGNL 展示出)进行了一些小调整。
> 因此，如果您是 Spring MVC 用户，您就不会浪费时间，因为您在这里学到的几乎所有东西都将在 Spring 应用程序中使用。

标准方言的大多数处理器是 _属性处理器_。 这允许浏览器甚至在被处理之前正确显示HTML模板文件，因为它们将简单地忽略附加属性。 例如，虽然使用标记库的 JSP 可能包含不能直接由浏览器显示的代码片段，例如:

```html
<form:inputText name="userName" value="${user.name}" />
```

...Thymeleaf标准方言将允许我们实现相同的功能：

```html
<input type="text" name="userName" value="James Carrot" th:value="${user.name}" />
```
浏览器不仅可以正确显示这些信息，⽽且还可以（可选地）在浏览器中静态
打开时显示⼀个默认的值（可选地），在本列中为“James Carrot”），
在模板处理期间由 `${user.name}` 的值代替 value 的真实值。

这有助于您的设计师和开发⼈员处理相同的模板⽂件，并减少将静态原型
转换为⼯作模板⽂件所需的⼯作量。具备这种能⼒的模版我们称为 _⾃然模板_。


2 The Good Thymes Virtual Grocery
=================================

本指南后面章节以及这个示例的源码都可以在 [Good Thymes Virtual Grocery GitHub repository](https://github.com/thymeleaf/thymeleafexamples-gtvg) 找到。




2.1 一个杂货店的网站
---------------------------

为了更好地解释使⽤ Thymeleaf 模板所涉及的概念，本教程将通过⼀个示
例程序来说明，该示列程序可以从站点下载。

这个示例程序是⼀个假想的虚拟杂货店的⽹站，它将为我们提供许多场景
来展示 Thymeleaf 的许多功能。

在开始介绍示例之前，我们先简单的分析⼀下示例程序的实体模型：客户
（`Customers`）通过创建订单（`Orders`）来购买的产品（`Products`）。系
统还提供了对产品评论（`Comments`）的管理：

![Example application model](images/usingthymeleaf/gtvg-model.png)


我们的应⽤程序也将有⼀个⾮常简单的服务层（service），由 `Service` 对象组成，包
含以下⽅法：


```java
public class ProductService {

    ...

    public List<Product> findAll() {
        return ProductRepository.getInstance().findAll();
    }

    public Product findById(Integer id) {
        return ProductRepository.getInstance().findById(id);
    }

}
```

在web层，我们的应用程序将拥有一个过滤器，它将根据请求 URL 委托执行启用 thymeleaf 的命令:

```java
private boolean process(HttpServletRequest request, HttpServletResponse response)
        throws ServletException {

    try {

        // This prevents triggering engine executions for resource URLs
        if (request.getRequestURI().startsWith("/css") ||
                request.getRequestURI().startsWith("/images") ||
                request.getRequestURI().startsWith("/favicon")) {
            return false;
        }


        /*
         * Query controller/URL mapping and obtain the controller
         * that will process the request. If no controller is available,
         * return false and let other filters/servlets process the request.
         */
        IGTVGController controller = this.application.resolveControllerForRequest(request);
        if (controller == null) {
            return false;
        }

        /*
         * Obtain the TemplateEngine instance.
         */
        ITemplateEngine templateEngine = this.application.getTemplateEngine();

        /*
         * Write the response headers
         */
        response.setContentType("text/html;charset=UTF-8");
        response.setHeader("Pragma", "no-cache");
        response.setHeader("Cache-Control", "no-cache");
        response.setDateHeader("Expires", 0);

        /*
         * Execute the controller and process view template,
         * writing the results to the response writer.
         */
        controller.process(
                request, response, this.servletContext, templateEngine);

        return true;

    } catch (Exception e) {
        try {
            response.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
        } catch (final IOException ignored) {
            // Just ignore this
        }
        throw new ServletException(e);
    }

}
```

下面是 `IGTVGController` 接口:

```java
public interface IGTVGController {

    public void process(
            HttpServletRequest request, HttpServletResponse response,
            ServletContext servletContext, ITemplateEngine templateEngine);    

}
```

我们现在要做的就是创建 `IGTVGController` 接⼝的实现，并且从服务层中
获取数据然后⽤ `ITemplateEngine` 对象处理模板并完成数据渲染。

最后程序运⾏结果如下所示：

![Example application home page](images/usingthymeleaf/gtvg-view.png)

⾸先我们先看⼀下模版引擎是如何初始化的。



2.2 创建和配置模版引擎
------------------------------------------------

在过滤器的 _process(...)_ 方法中包含如下一行代码:

```java
ITemplateEngine templateEngine = this.application.getTemplateEngine();
```

这意味着  _GTVGApplication_ 类负责创建和配置 Thymeleaf 应用程序中的最重要的对象之一：`TemplateEngine` 实例（`ITemplateEngine` 接口的实现）。


`org.thymeleaf.TemplateEngine` 对象被初始化如下：

```java
public class GTVGApplication {


    ...
    private final TemplateEngine templateEngine;
    ...


    public GTVGApplication(final ServletContext servletContext) {

        super();

        ServletContextTemplateResolver templateResolver =
                new ServletContextTemplateResolver(servletContext);

        // HTML is the default mode, but we set it anyway for better understanding of code
        templateResolver.setTemplateMode(TemplateMode.HTML);
        // This will convert "home" to "/WEB-INF/templates/home.html"
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        // Template cache TTL=1h. If not set, entries would be cached until expelled
        templateResolver.setCacheTTLMs(Long.valueOf(3600000L));

        // Cache is set to true by default. Set to false if you want templates to
        // be automatically updated when modified.
        templateResolver.setCacheable(true);

        this.templateEngine = new TemplateEngine();
        this.templateEngine.setTemplateResolver(templateResolver);

        ...

    }

}
```

有许多种配置 `TemplateEngine` 对象的方式，但本示例程序中的这⼏
⾏配置模版引擎代码将⾜够教给我们所需的步骤。


### 模板解析器

我们从模板解析器开始：

```java
ServletContextTemplateResolver templateResolver =
        new ServletContextTemplateResolver(servletContext);
```

模板解析器是一组对象，一组实现了 Thymeleaf 的 `org.thymeleaf.templateresolver.ITemplateResolver` 接口的对象

```java
public interface ITemplateResolver {

    ...

    /*
     * Templates are resolved by their name (or content) and also (optionally) their
     * owner template in case we are trying to resolve a fragment for another template.
     * Will return null if template cannot be handled by this template resolver.
     */
    public TemplateResolution resolveTemplate(
            final IEngineConfiguration configuration,
            final String ownerTemplate, final String template,
            final Map<String, Object> templateResolutionAttributes);
}
```

These objects are in charge of determining how our templates will be accessed,
and in this GTVG application, the `org.thymeleaf.templateresolver.ServletContextTemplateResolver`
means that we are going to retrieve our template files as resources from the
_Servlet Context_: an application-wide `javax.servlet.ServletContext` object
that exists in every Java web application, and that resolves resources from the
web application root.

But that's not all we can say about the template resolver, because we can set
some configuration parameters on it. First, the template mode:

```java
templateResolver.setTemplateMode(TemplateMode.HTML);
```

HTML is the default template mode for `ServletContextTemplateResolver`, but it
is good practice to establish it anyway so that our code documents clearly what
is going on.

```java
templateResolver.setPrefix("/WEB-INF/templates/");
templateResolver.setSuffix(".html");
```

The _prefix_ and _suffix_ modify the template names that we will be passing to
the engine for obtaining the real resource names to be used.

Using this configuration, the template name _"product/list"_ would correspond to:

```java
servletContext.getResourceAsStream("/WEB-INF/templates/product/list.html")
```

Optionally, the amount of time that a parsed template can live in the cache is
configured at the Template Resolver by means of the _cacheTTLMs_ property:

```java
templateResolver.setCacheTTLMs(3600000L);
```

A template can still be expelled from cache before that TTL is reached if the
max cache size is reached and it is the oldest entry currently cached.

> Cache behaviour and sizes can be defined by the user by implementing the `ICacheManager`
> interface or by modifying the `StandardCacheManager` object to manage the
> default cache.

There is much more to learn about template resolvers, but for now let's have a
look at the creation of our Template Engine object.


### The Template Engine

Template Engine objects are implementations of the `org.thymeleaf.ITemplateEngine`
interface. One of these implementations is offered by the Thymeleaf core:
`org.thymeleaf.TemplateEngine`, and we create an instance of it here:

```java
templateEngine = new TemplateEngine();
templateEngine.setTemplateResolver(templateResolver);
```

Rather simple, isn't it? All we need is to create an instance and set the
Template Resolver to it.

A template resolver is the only *required* parameter a `TemplateEngine` needs,
although there are many others that will be covered later (message resolvers,
cache sizes, etc). For now, this is all we need.

Our Template Engine is now ready and we can start creating our pages using
Thymeleaf.




3 使⽤⽂本
=============



3.1 多语言欢迎页
----------------------------

我们的第⼀个任务是为我们的杂货店创建⼀个主⻚。

该⻚⾯的第⼀个版本将⾮常简单：只需⼀个标题和⼀个欢迎信息。
这是我们的 `/WEB-INF/templates/home.html` ⽂件：

```html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
  </head>

  <body>

    <p th:text="#{home.welcome}">Welcome to our grocery store!</p>

  </body>

</html>
```

您会注意到的第⼀件事是，该⽂件是 HTML5，可以由任何浏览器正确显
示，因为它不包括任何⾮ HTML 标签（浏览器忽略他们不明⽩的所有属
性，如：`th:text`）。

但是您也可能会注意到，这个模板并不是⼀个真正有效的HTML5⽂档，
因为 HTML5 规范不允许在 `th:*` 形式中使⽤这些⾮标准属性。 事实上，我
们甚⾄在我们的 `<html>` 标签中添加了⼀个 `xmlns:th` 属性，
这绝对是⾮ HTML5 标准：

```html
<html xmlns:th="http://www.thymeleaf.org">
```

...这在模板处理中根本没有任何影响，但作为⼀种编程惯例，我们通常
会阻⽌IDE提示 `th:*` 属性缺少命名空间定义的警告。

那么如何使这个 HTML5 模板有效呢？ 很简单：切换到 Thymeleaf 的数据属
性语法，使⽤ `data-` 属性名称和连字符（ `-` ）分隔符的数据前缀⽽不是分号
（`:`)：


```html
<!DOCTYPE html>

<html>

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="../../css/gtvg.css" data-th-href="@{/css/gtvg.css}" />
  </head>

  <body>

    <p data-th-text="#{home.welcome}">Welcome to our grocery store!</p>

  </body>

</html>
```

HTML5 规范允许⽤户⾃定义以 `data-` 前缀开头的属性，因此，使⽤上⾯的代
码，我们的模板将是⼀个有效的 HTML5 ⽂档。

> 这两个符号是完全等价的，可互换的
> 但是为了代码示例的简单性和紧凑性，本教程将使用 *namespace表示法*  (`th:*`)。
> 此外，`th:*` 表示法更通用，并且在每个 Thymeleaf 模板模式(`XML`, `TEXT`...)中都可以使用，
> 而 `data-` 表示法只在 `HTML` 模式中使用。


### 使用 th:text 和外部文本

外部⽂本是从模板⽂件中提取模板代码的⽚段，以便它们可以保存在单
独的⽂件（通常为 `.properties` ⽂件）中，并且可以轻松地替换为使⽤其他
语⾔编写的等效⽂本（称为国际化或简单的 _i18n_）。 ⽂本的外部⽚段通
常称为 *"messages"*。

消息总是具有标识它们的键，⽽ Thymeleaf 允许您使⽤ `#{...}` 语法表达式指
定对应的特定消息：

```html
<p th:text="#{home.welcome}">Welcome to our grocery store!</p>
```

我们在这⾥看到的其实是 Thymeleaf 标准⽅⾔的两个不同特征：

 * `th:text` 属性，它计算其值表达式并将结果设置为标签的标签体，
  有效地替换了代码中我们看到的 “Welcome to our grocery store!” 这段⽂本。
 * 使用 _标准表达式语法_ 指定的 `#{home.welcome}` 表达式，
   其指示由 `th:text` 属性使⽤的⽂本应该是 `home.welcome` 键对应于我们
   正在处理模板的地区设置的消息。

现在，这个外部⽂本在哪⾥？

Thymeleaf 中外部⽂本的位置是完全可配置的，它将取决于正在使⽤的
具体的 `org.thymeleaf.messageresolver.IMessageResolver` 实现。
通常，将使⽤基于 `.properties` ⽂件的实现，但是如果我们想要（例如）从数据库
获取消息，我们可以创建⾃⼰的实现。

但是，我们在初始化期间尚未为模板引擎指定消息解析器，这意味着我们的应⽤程序将使⽤由 `org.thymeleaf.messageresolver.StandardMessageResolver` 实现的标准消息解析器。

标准消息解析器期望在与模板 `/WEB-INF/templates/home.html` 相同的⽂件夹中的 
相同名称的 properties ⽂件中的消息，例如：

 * `/WEB-INF/templates/home_en.properties` 提供 English 语言的文本.
 * `/WEB-INF/templates/home_es.properties` 提供 Spanish 语言的文本.
 * `/WEB-INF/templates/home_pt_BR.properties` 提供 Portuguese (Brazil) 语言的文本.
 * `/WEB-INF/templates/home.properties` 提供默认文本（如果没有匹配的区域）

我们来看看我们的 `home_es.properties` ⽂件：

```
home.welcome=¡Bienvenido a nuestra tienda de comestibles!
```

这就是 Thymeleaf 处理模板所需要的全部。然后，让我们创建我们的 Home controller。


### Contexts

为了处理模板，我们将创建一个实现 `IGTVGController` 的 `HomeController` 类：

```java
public class HomeController implements IGTVGController {

    public void process(
            final HttpServletRequest request, final HttpServletResponse response,
            final ServletContext servletContext, final ITemplateEngine templateEngine)
            throws Exception {

        WebContext ctx =
                new WebContext(request, response, servletContext, request.getLocale());

        templateEngine.process("home", ctx, response.getWriter());

    }

}
```
我们⾸先看到的是创建⼀个 *context*。 Thymeleaf 上下⽂是实现
`org.thymeleaf.context.IContext` 接⼝的对象。
上下⽂应包含模板引擎执⾏变量映射所需的所有数据，
并且还引⽤必须⽤于外部消息的区域设置。

```java
public interface IContext {

    public Locale getLocale();
    public boolean containsVariable(final String name);
    public Set<String> getVariableNames();
    public Object getVariable(final String name);

}
```

这个接口有一个专门的扩展，`org.thymeleaf.context.IWebContext`，
用于基于 ServletAPI 的 web 应用程序(如 SpringMVC)。

```java
public interface IWebContext extends IContext {

    public HttpServletRequest getRequest();
    public HttpServletResponse getResponse();
    public HttpSession getSession();
    public ServletContext getServletContext();

}
```

Thymeleaf核⼼库提供了以下每个接⼝的实现：

 * `org.thymeleaf.context.Context` 实现了 `IContext`
 * `org.thymeleaf.context.WebContext` 实现了 `IWebContext`

如你在 controller 代码中所见，我们使用了 `WebContext` 。
事实上，我们必须这么做，
因为 `ServletContextTemplateResolver` 的使用需要一个实现了 `IWebContext` 的上下文。

```java
WebContext ctx = new WebContext(request, response, servletContext, request.getLocale());
```

在四个构造函数参数中只有三个是必需的，因为
如果没有指定任何语言环境，将使用系统的默认语言环境(尽管在实际应用程序中不应该让这种情况发生)。

我们可以使用一些专门的表达式从模板中的 `WebContext` 获取请求参数和请求、会话和应用程序属性。例如:

 * `${x}` 将返回一个存储在 Thymeleaf 上下文中或是作为 *request attribute* 的变量
 * `${param.x}` 将返回一个名为 `x` 的 *request parameter* (它可能有多个值)
 * `${session.x}` 将返回一个名为 `x` 的 *session attribute*
 * `${application.x}` 将返回一个名为 `x` 的 *servlet context attribute*


### 执行模板引擎

上下文对象就绪后，我们就可以告诉模板引擎使用上下文处理模板(通过其名称)，
并将响应写入器传递给它，以便将响应写入到它:

```java
templateEngine.process("home", ctx, response.getWriter());
```

让我们看看使用西班牙语言环境的结果:

```html
<!DOCTYPE html>

<html>

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta content="text/html; charset=UTF-8" http-equiv="Content-Type"/>
    <link rel="stylesheet" type="text/css" media="all" href="/gtvg/css/gtvg.css" />
  </head>

  <body>

    <p>¡Bienvenido a nuestra tienda de comestibles!</p>

  </body>

</html>
```



3.2 更多关于文本和变量的信息
-------------------------------


### 不转义文本

我们的主页最简单的版本似乎已经准备好了，
但有一些东西我们还没有考虑……如果我们有这样的信息呢?

```java
home.welcome=Welcome to our <b>fantastic</b> grocery store!
```

如果我们像以前一样执行这个模板，我们会得到:

```html
<p>Welcome to our &lt;b&gt;fantastic&lt;/b&gt; grocery store!</p>
```

这并不是我们所期望的，因为我们的 `<b>` 标记已经转义，因此它将显示在浏览器中。

这是 `th:text` 的默认行为。如果想要 Thymeleaf 保留 HTML 标签，并不对它进行转义，
需要使用一个不同的属性：`th:utext`（用于 "不转义文本"）

```html
<p th:utext="#{home.welcome}">Welcome to our grocery store!</p>
```

它将输出如你所愿的消息：

```html
<p>Welcome to our <b>fantastic</b> grocery store!</p>
```


### 使用和显示变量

现在让我们向主页添加更多内容。例如，我们可能希望在欢迎信息下面显示日期，如下所示:

```
Welcome to our fantastic grocery store!

Today is: 12 july 2010
```

首先，我们必须修改我们的 controller，以便我们添加日期作为上下文变量:

```java
public void process(
            final HttpServletRequest request, final HttpServletResponse response,
            final ServletContext servletContext, final ITemplateEngine templateEngine)
            throws Exception {

    SimpleDateFormat dateFormat = new SimpleDateFormat("dd MMMM yyyy");
    Calendar cal = Calendar.getInstance();

    WebContext ctx =
            new WebContext(request, response, servletContext, request.getLocale());
    ctx.setVariable("today", dateFormat.format(cal.getTime()));

    templateEngine.process("home", ctx, response.getWriter());

}
```

我们在上下文中添加了一个名为 `today` 的 `String` 变量，现在我们可以在模板中显示它:

```html
<body>

  <p th:utext="#{home.welcome}">Welcome to our grocery store!</p>

  <p>Today is: <span th:text="${today}">13 February 2011</span></p>

</body>
```

如你所见，我们仍然在使用 `th:text` 属性(这是正确的，因为我们希望替换标记的主体)，
但是这次的语法稍有不同，我们使用的是 `${...}`，而不是使用 `#{...}` 表达式值。
这是一个**变量表达式**，它包含一种名为
_OGNL (Object-Graph Navigation Language)_ 的语言中的表达式，
该表达式将在我们前面讨论的上下文变量映射上执行。

`${today}` 表达式仅仅意味着 “获取名为 today 的变量”，
但这些表达式可能更复杂(例如 `${user.name}` 用于 “获取名为 user 的变量，并调用其 `getName()` 方法)。


属性值中有相当多的可能性:消息、变量表达式……还有很多。下一章将向我们展示所有这些可能性。


4 标准表达式语法
============================

我们将在杂货店虚拟商店的开发中稍事休息，学习一下 Thymeleaf 标准方言最重要的部分之一:
Thymeleaf 标准表达式语法。

我们已经见过两种有效的属性值表达式：消息和变量表达式

```html
<p th:utext="#{home.welcome}">Welcome to our grocery store!</p>

<p>Today is: <span th:text="${today}">13 february 2011</span></p>
```

但是关于我们已经知道的表达式，还有更多的类型和更有趣的细节需要学习。
首先，让我们快速总结一下标准表达式的特性:

 * 简单的表达式:
    * 变量表达式: `${...}`
    * 选择变量表达式: `*{...}`
    * 消息表达式: `#{...}`
    * 链接 URL 表达式: `@{...}`
    * 片段表达式: `~{...}`
 * 字面量（Literals）
    * 文本字面量: `'one text'`, `'Another one!'`,...
    * 数字字面量: `0`, `34`, `3.0`, `12.3`,...
    * 布尔字面量: `true`, `false`
    * Null 字面量: `null`
    * 字面量 tokens: `one`, `sometext`, `main`,...
 * 文本运算:
    * 字符串连接: `+`
    * 字面量替换: `|The name is ${name}|` 模板字符串
 * 算术运算:
    * 二元运算: `+`, `-`, `*`, `/`, `%`
    * 负号(一元算子): `-`
 * 布尔运算:
    * 二元运算: `and`, `or`
    * 布尔否定(一元运算符): `!`, `not`
 * 比较与相等:
    * 比较运算: `>`, `<`, `>=`, `<=` (`gt`, `lt`, `ge`, `le`)
    * 相等运算: `==`, `!=` (`eq`, `ne`)
 * 条件运算:
    * If-then: `(if) ? (then)`
    * If-then-else: `(if) ? (then) : (else)`
    * Default: `(value) ?: (defaultvalue)`
 * 专用记号:
    * No-Operation: `_`

所有的这些特性都可以被组合和嵌套：

```html
'User is of type ' + (${user.isAdmin()} ? 'Administrator' : (${user.type} ?: 'Unknown'))
```



4.1 消息
------------

我们已经知道，`#{...}` 消息表达式允许我们这样：

```html
<p th:utext="#{home.welcome}">Welcome to our grocery store!</p>
```

...这样：

```
home.welcome=¡Bienvenido a nuestra tienda de comestibles!
```

But there's one aspect we still haven't thought of: what happens if the message
text is not completely static? What if, for example, our application knew who is
the user visiting the site at any moment and we wanted to greet them by name?

```html
<p>¡Bienvenido a nuestra tienda de comestibles, John Apricot!</p>
```

This means we would need to add a parameter to our message. Just like this:

```
home.welcome=¡Bienvenido a nuestra tienda de comestibles, {0}!
```

Parameters are specified according to the
[`java.text.MessageFormat`](https://docs.oracle.com/javase/10/docs/api/java/text/MessageFormat.html)
standard syntax, which means you can format to numbers and dates as specified
in the API docs for classes in the `java.text.*` package.

In order to specify a value for our parameter, and given an HTTP session
attribute called `user`, we could have:

```html
<p th:utext="#{home.welcome(${session.user.name})}">
  Welcome to our grocery store, Sebastian Pepper!
</p>
```

> Note that the use of `th:utext` here means that the formatted message will
> not be escaped. This example assumes that `user.name` is already escaped.

Several parameters can be specified, separated by commas.

The message key itself can come from a variable:

```html
<p th:utext="#{${welcomeMsgKey}(${session.user.name})}">
  Welcome to our grocery store, Sebastian Pepper!
</p>
```



4.2 变量
-------------

我们已经提到 `${...}` 的表达式实际上是在上下文中包含的变量映射上执行的 OGNL(Object-Graph
Navigation Language)表达式。

> 有关 OGNL 语法和特性的详细信息，请参阅
> [OGNL Language Guide](http://commons.apache.org/ognl/)
>
> 在Spring MVC 应用程序中，OGNL将被替换为**SpringEL**，
> 但是它的语法与 OGNL 非常相似(实际上，对于大多数常见的情况是完全相同的)。

从 OGNL 的语法中，我们知道:

```html
<p>Today is: <span th:text="${today}">13 february 2011</span>.</p>
```

...实际上等于:

```java
ctx.getVariable("today");
```

但是 OGNL 允许我们创建更强大的表达式，这就是它的工作原理:

```html
<p th:utext="#{home.welcome(${session.user.name})}">
  Welcome to our grocery store, Sebastian Pepper!
</p>
```

...通过执行以下命令获得用户名:

```java
((User) ctx.getVariable("session").get("user")).getName();
```

getter 方法导航只是 OGNL 的特性之一。让我们再看一些:

```java
/*
 * 使用点(.)访问属性。相当于调用属性 getter。
 */
${person.father.name}

/*
 * 也可以使用方括号([])访问属性，并将属性的名称作为变量写入，或者在单引号之间写入。
 */
${person['father']['name']}

/*
 * 如果对象是 map，则点和括号语法都相当于调用其 get(…)方法。
 */
${countriesByCode.ES}
${personsByName['Stephen Zucchini'].age}

/*
 * 对数组或集合的索引访问也使用方括号执行，写入索引时不使用引号。
 */
${personsArray[0].name}

/*
 * 方法可以被调用，甚至带有参数。
 */
${person.createCompleteName()}
${person.createCompleteNameWithSeparator('-')}
```


### 表达式基本对象

当在上下文变量上计算 OGNL 表达式时，为了获得更高的灵活性，表达式可以使用一些对象。
这些对象使用 `#` 符号开头进行引用(按照OGNL标准):


 * `#ctx`: 上下文对象
 * `#vars:` 上下文变量
 * `#locale`: 本地化上下文
 * `#request`: (仅在 Web Contexts 中) `HttpServletRequest` 对象
 * `#response`: (仅在 Web Contexts 中) `HttpServletResponse` 对象
 * `#session`: (仅在 Web Contexts 中)  `HttpSession` 对象
 * `#servletContext`: (仅在 Web Contexts 中) `ServletContext` 对象

我们可以这样做:

```html
Established locale country: <span th:text="${#locale.country}">US</span>.
```

你可以在 [Appendix A](#appendix-a-expression-basic-objects) 阅读这些对象的完成参考文档。


### 表达式实用对象

除了这些基本对象之外，Thymeleaf 还将提供一组实用对象，帮助我们在表达式中执行常见任务。

 * `#execInfo`: 有关正在处理的模板的信息。
 * `#messages`: 在变量表达式中获取外部消息的方法集，与使用 `#{...}` 语法相同。
 * `#uris`: 用于转义 URLs/URIs 部分的方法集
 * `#conversions`: 执行配置的 *conversion service* 的方法 (如果有)
 * `#dates`:`java.util.Date` 对象的方法集: 格式化、组件提取等。
 * `#calendars`: 类似于 `#dates`, 但用于 `java.util.Calendar` 对象
 * `#numbers`: 用于格式化数字对象的方法集
 * `#strings`: 作用于 `String` 对象的方法集： contains, startsWith, prepending/appending 等等
 * `#objects`: 用于普通对象的方法集
 * `#bools`: 用于布尔运算的方法集
 * `#arrays`: 用于数组的方法集
 * `#lists`: 用于 list 的方法集
 * `#sets`: 用于 sets 的方法集
 * `#maps`: 用于 maps 的方法集
 * `#aggregates`: 用于在数组或集合上创建聚合的方法集。
 * `#ids`: 处理可能重复的 id 属性的方法集(例如，作为迭代的结果)。

可以在 [Appendix B](#appendix-b-expression-utility-objects) 检查这些实用程序对象提供的函数

### 重新格式化主页中的日期

现在我们知道了这些实用程序对象，我们可以使用它们来更改在主页中显示日期的方式。而不是在 `HomeController` 做:

```java
SimpleDateFormat dateFormat = new SimpleDateFormat("dd MMMM yyyy");
Calendar cal = Calendar.getInstance();

WebContext ctx = new WebContext(request, servletContext, request.getLocale());
ctx.setVariable("today", dateFormat.format(cal.getTime()));

templateEngine.process("home", ctx, response.getWriter());
```

...我们可以这样做:

```java
WebContext ctx =
    new WebContext(request, response, servletContext, request.getLocale());
ctx.setVariable("today", Calendar.getInstance());

templateEngine.process("home", ctx, response.getWriter());
```

...然后在视图层本身执行日期格式化:

```html
<p>
  Today is: <span th:text="${#calendars.format(today,'dd MMMM yyyy')}">13 May 2011</span>
</p>
```



4.3 Expressions on selections (星号语法)
-----------------------------------------------

变量表达式不仅可以写为 `${...}`，也可以写为 `*{...}`。

但是有一个重要的区别：星号语法在 _selected objects_ 上计算表达式，而不是在整个上下文中。
也就是说，只要没有选择对象，`${...}` 和 `*{...}` 语法的作用完全相同。

什么是 _selected objects_?使用 `th:object` 属性的表达式的结果。
让我们在我们的用户配置文件(`userprofile.html`)页使用:

```html
  <div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
  </div>
```

这正好等于:

```html
<div>
  <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
</div>
```

当然，美元和星号语法可以混合使用:

```html
<div th:object="${session.user}">
  <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div>
```

当对象选择就绪时，所选对象也可以作为 `#object` 表达式变量提供给美元表达式:

```html
<div th:object="${session.user}">
  <p>Name: <span th:text="${#object.firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div>
```

如前所述，如果没有执行对象选择，则美元和星号语法是等效的。

```html
<div>
  <p>Name: <span th:text="*{session.user.name}">Sebastian</span>.</p>
  <p>Surname: <span th:text="*{session.user.surname}">Pepper</span>.</p>
  <p>Nationality: <span th:text="*{session.user.nationality}">Saturn</span>.</p>
</div>
```



4.4 链接 URLs
-------------

由于它们的重要性，URL 在 web 应用程序模板中是一等公民，
_Thymeleaf标准方言_ 对它们有特殊的语法，`@` 语法: `@{...}`

不同类型的URL:

 * 绝对 URLs: `http://www.thymeleaf.org`
 * 相对 URLs, 可以是:
    * Page-relative: `user/login.html`
    * Context-relative: `/itemdetails?id=3` (服务器中的上下文名称将自动添加)
    * Server-relative: `~/billing/processInvoice` (允许在同一服务器的另一个上下文(=应用程序)中调用 url。
    * Protocol-relative URLs: `//code.jquery.com/jquery-2.0.3.min.js`

这些表达式的真正处理以及它们对将要输出的 URL 的转换是通过注册到正在使用的 `ITemplateEngine` 对象中的  `org.thymeleaf.linkbuilder.ILinkBuilder` 接口的实现完成的。

默认情况下，该接口的单个实现注册为类 `org.thymeleaf.linkbuilder.StandardLinkBuilder`。
这对于离线(非 web)和基于 Servlet API 的 web 场景都足够了。
其他场景(如与非 ServletAPI web 框架的集成)可能需要 link builder 接口的特定实现。

让我们使用这个新语法。接触 `th:href` 属性:

```html
<!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->
<a href="details.html"
   th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a>

<!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->
<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>

<!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->
<a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a>
```
这里需要注意的是:

 * `th:href` 是一个修饰符属性:一旦处理，它将计算要使用的链接 URL，并将该值设置为 `<a>` 标记的 `href` 属性。
 * 我们可以对 URL 参数使用表达式(正如您在 `orderId=${o.id}` 中看到的那样)。
   还将自动执行所需的 URL 参数编码操作。
 * 如果需要几个参数，这些参数将用逗号分隔: `@{/order/process(execId=${execId},execType='FAST')}`
 * 在 URL 路径中也允许使用可变模板: `@{/order/{orderId}/details(orderId=${orderId})}`
 * 以 `/` 开头的相对 URL(例如：`/order/details`)将自动以应用程序上下文名称作为前缀。
 * 如果没有启用 cookie，或者还不知道这一点，则使用 `";jsessionid=..."` 后缀可能被添加到相对 URL，
   以便会话被保留。这称为 _URL Rewriting_， Thymeleaf 允许您通过对每个 URL 使用 Servlet API 中的`response.encodeURL(...)` 机制插入自己的重写过滤器。
 * `th:href` 属性允许我们(可选地)在模板中拥有一个可工作的静态 `href` 属性，以便在直接在设计原型页面时打开模板链接，浏览器仍然可以导航。

与消息语法(`#{...}`)一样，URL 基也可以是计算另一个表达式的结果:

```html
<a th:href="@{${url}(orderId=${o.id})}">view</a>
<a th:href="@{'/details/'+${user.login}(orderId=${o.id})}">view</a>
```


### 主页菜单

我们已经知道如何创建链接 URL，那在主页上为网站上的其他页面添加一个小菜单怎么样?

```html
<p>Please select an option</p>
<ol>
  <li><a href="product/list.html" th:href="@{/product/list}">Product List</a></li>
  <li><a href="order/list.html" th:href="@{/order/list}">Order List</a></li>
  <li><a href="subscribe.html" th:href="@{/subscribe}">Subscribe to our Newsletter</a></li>
  <li><a href="userprofile.html" th:href="@{/userprofile}">See User Profile</a></li>
</ol>
```


### 相对于服务器根路径的 URL


为了链接到统一服务器的不同上下文，可以使用一个额外的语法可以用来创建相对于服务器根路径的的 URL。
这些 URL 应该像这样：`@{~/path/to/something}`



4.5 片段（Fragments）
-------------

片段表达式是一种表示标记片段并在模板中移动它们的简单方法。
这允许我们复制它们，将它们作为参数传递给其他模板，等等。


最常见的用法是使用 `th:insert` 或 `th:replace` 插入片段(后面一节将详细介绍):

```html
<div th:insert="~{commons :: main}">...</div>
```

但是它们可以在任何地方使用，就像其他变量一样:

```html
<div th:with="frag=~{footer :: #main/text()}">
  <p th:insert="${frag}">
</div>
```

在本教程的后面有一个完整的部分专门讨论模板布局，包括对片段表达式的更深入的解释。


4.6 字面量（Literals）
------------

### 文本字面量


文本字面量只是在单引号之间指定的字符串。
它们可以包含任何字符，但是应该使用`\'`转义其中的任何单引号。

```html
<p>
  Now you are looking at a <span th:text="'working web application'">template file</span>.
</p>
```

### 数字字面量

数字字面量就是:数字。

```html
<p>The year is <span th:text="2013">1492</span>.</p>
<p>In two years, it will be <span th:text="2013 + 2">1494</span>.</p>
```


### 布尔字面量

布尔字面量是 `true` 和 `false`. 例如：

```html
<div th:if="${user.isAdmin()} == false"> ...
```

在这个例子中，`== false` 写在大括号外面，所以是 Thymeleaf 处理它。
如果它写在括号内，它将是 OGNL/SpringEL 引擎的责任:

```html
<div th:if="${user.isAdmin() == false}"> ...
```


### null 字面量

`null` 字面量可以这样使用：

```html
<div th:if="${variable.something} == null"> ...
```


### Literal tokens

数字、布尔和 null 字面量实际上是 _literal tokens_ 的特殊情况

这些令牌允许在标准表达式中进行一些简化。它们的工作原理与文字字面量完全相同(`'...'`)，
他们仅允许字面量（`A-Z` 和 `a-z`），数字（`0-9`），中括号（`[` 和 `]`），点 (`.`)，
连字符 (`-`) 和下划线 (`_`)。没有空格，没有逗号等等。

好的部分？Tokens 不需要任何引号围绕。我们可以这样做：

```html
<div th:class="content">...</div>
```

instead of:

```html
<div th:class="'content'">...</div>
```



4.7 附加文本
-------------------

文本，无论它们是字面量，还是变量或消息表达式求值的结果，都可以很容易地使用 `+` 运算符附加:

```html
<span th:text="'The name of the user is ' + ${user.name}">
```



4.8 字面量替换
-------------------------

字面量替换允许对包含变量值的字符串进行简单的格式化，而不需要在字符串后面加上 `'...' + '...'`。


这些替换必须由竖条(' | ')包围，例如:

```html
<span th:text="|Welcome to our application, ${user.name}!|">
```

这等价于：

```html
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
```

字面量替换可以和其他类型的表达式组合：

```html
<span th:text="${onevar} + ' ' + |${twovar}, ${threevar}|">
```
> `|...|` 字面量替换中只允许变量/消息表达式（`${...}`, `*{...}`, `#{...}`）
> 不能出现字面量（`'...'`）、布尔/数字 token，条件表达式等等。


4.9 算术运算
-------------------------

一些算术运算也可用：`+`, `-`, `*`, `/` 和 `%`。

```html
<div th:with="isEven=(${prodStat.count} % 2 == 0)">
```

注意，这些操作符也可以应用于 OGNL 变量表达式本身(在这种情况下，将由 OGNL 而不是 Thymeleaf 标准表达式引擎执行):

```html
<div th:with="isEven=${prodStat.count % 2 == 0}">
```

注意，对于其中一些操作符，文本别名是存在的：`div` (`/`), `mod` (`%`)。


4.10 比较器与相等
-----------------------------

表达式中的值可以与`>`, `<`, `>=` 和 `<=` 符号进行比较，
使用 `==` 和 `!=` 运算符可用于检查是否相等(或是否不相等)。
注意，XML 规定不应该在属性值中使用 `<` 和 `>` 符号，因此应该用 `&lt;` 和 `&gt;` 替换它们。

```html
<div th:if="${prodStat.count} &gt; 1">
<span th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')">
```

一种更简单的替代方法可能是为其中一些操作符使用文本别名：

*  `gt` (`>`)
*  `lt` (`<`)
*  `ge` (`>=`)
*  `le` (`<=`)
*  `not` (`!`)。
  
还有

* `eq` (`==`)
* `neq`/`ne` (`!=`)



4.11 条件表达式
----------------------------

_条件表达式_ 旨在根据一个条件的评估结果来选取两个表达式之一的值。

让我们看一个例子片段(引入另一个 _attribute modifier_， `th:class`):

```html
<tr th:class="${row.even}? 'even' : 'odd'">
  ...
</tr>
```

条件表达式的是三个部分 (`condition`, `then` and `else`) 都是表达式, 
这意味着他们可以是变量 (`${...}`, `*{...}`)，
消息 (`#{...}`), URLs (`@{...}`) 或者 字面量 (`'...'`).

条件表达式也可以嵌套使用括号:

```html
<tr th:class="${row.even}? (${row.first}? 'first' : 'even') : 'odd'">
  ...
</tr>
```

Else 表达式也可以省略，如果条件为 false，则返回 null 值:

```html
<tr th:class="${row.even}? 'alt'">
  ...
</tr>
```



4.12 默认表达式(Elvis 操作符)
-----------------------------------------

_默认表达式_ 是一种特殊的条件值，没有 _then_ 部分。
它等价于 Groovy 等一些语言中的 _Elvis 操作符，
它允许指定两个表达式：如果第一个表达式的值不为 null，则使用第一个表达式;
如果值为 null，则使用第二个表达式。


让我们看看它在我们的用户配置页面的行动:

```html
<div th:object="${session.user}">
  ...
  <p>Age: <span th:text="*{age}?: '(no age specified)'">27</span>.</p>
</div>
```

如你所见，操作符是`?:`，只有在计算 `*{age}` 的结果为 null 时，
我们才使用它来指定名称的默认值(在本例中为字面量值)。因此，这相当于:

```html
<p>Age: <span th:text="*{age != null}? *{age} : '(no age specified)'">27</span>.</p>
```

与条件值一样，它们可以在括号之间包含嵌套表达式:

```html
<p>
  Name:
  <span th:text="*{firstName}?: (*{admin}? 'Admin' : #{default.username})">Sebastian</span>
</p>
```



4.13 无操作 token
---------------------------

无操作 token 由下划线符号 (`_`) 表示。


这个 token 背后的思想是指定表达式的期望结果是“什么也不做”，
即完全按照可处理属性(例如 `th:text`)根本不在那里。

在其他可能性中，这允许开发人员使用原型文本作为默认值。例如，代替:

```html
<span th:text="${user.name} ?: 'no user authenticated'">...</span>
```

我们可以直接使用 *'no user authenticated'* 作为原型文本，这使得代码从设计的角度来说更加简洁和通用:

```html
<span th:text="${user.name} ?: _">no user authenticated</span>
```



4.14 数据转换/格式化
---------------------------------

Thymeleaf 为变量(`${...}`) 和选择 (`*{...}`) 表达式定义了*双括号*语法，允许我们通过配置的*转换服务*应用*数据转换*。

基本上是这样的:

```html
<td th:text="${{user.lastAccessDate}}">...</td>
```

注意到这里的双括号了吗?: `${{...}}`。它指示Thymeleaf传递 `user.lastAccessDate` 表达式的结果到*conversion service*，并要求它在写入结果之前执行**格式化操作**(转换为 `String`)。


假设 `user.lastAccessDate` 是 `java.util.Calendar` 类型，
如果已经注册了一个 *conversion service* (`IStandardConversionService` 的实现)，
并且包含一个有效的 `Calendar -> String` 转换，那么它将被应用。

`IStandardConversionService` (`StandardConversionService` 类)的默认实现只是在任何转换为 `String` 的对象上执行 `.toString()`。
有关如何注册自定义 *conversion service* 实现的更多信息，请参阅[More on Configuration](#more-on-configuration)一节。

> 官方的 Thymeleaf-spring3 和 Thymeleaf-spring4 集成包透明地将
> Thymeleaf 的转换服务机制与 Spring 自己的 *Conversion Service* 基础设施集成在一起，
> 这样在 Spring 配置中声明的转换服务和格式化程序将自动提供给 `${{...}}` 和 `*{{...}}` 表达式。



4.15 预处理
------------------


除了这些用于表达式处理的特性外，Thymeleaf 还具有 _预处理_ 表达式的特性。

预处理是在正常表达式之前执行的表达式，它允许修改最终将被执行的表达式。

预处理表达式与普通表达式完全相同，但是由一个双下划线符号(如 `__${expression}__`)包围。

假设我们有一个 i18n `Messages_fr.properties` 的条目包含调用特定于语言的静态方法的 OGNL 表达式，如:

```java
article.text=@myapp.translator.Translator@translateToFrench({0})
```

...和一个 `Messages_es.properties equivalent`:

```java
article.text=@myapp.translator.Translator@translateToSpanish({0})
```

我们可以创建一个标记片段，它根据语言环境计算一个表达式或另一个表达式。
为此，我们首先选择表达式(通过预处理)，然后 让Thymeleaf 执行:

```html
<p th:text="${__#{article.text('textVar')}__}">Some text here...</p>
```

请注意，法语地区的预处理步骤将创建以下等效程序:

```html
<p th:text="${@myapp.translator.Translator@translateToFrench(textVar)}">Some text here...</p>
```

预处理字符串 `__` 可以使用 `\_\_` 在属性中转义。


5 设置属性值
==========================

本章将解释如何设置(或修改)标记中的属性值。


5.1 设置任何属性的值
--------------------------------------

假设我们的网站发布了一份时事通讯，我们希望我们的用户能够订阅它，
所以我们创建了一个带有表单的 `/WEB-INF/templates/subscribe.html` 模板:

```html
<form action="subscribe.html">
  <fieldset>
    <input type="text" name="email" />
    <input type="submit" value="Subscribe!" />
  </fieldset>
</form>
```

与使用 Thymeleaf 一样，这个模板一开始更像是一个静态原型，而不是 web 应用程序的模板。
首先，表单中的 `action` 属性静态链接到模板文件本身，这样就没有地方进行有用的 URL 重写。
其次，submit 按钮中的 `value` 属性使它显示英语文本，但我们希望它是国际化的。

然后输入 `th:attr` 属性，它有能力改变设置在其中的标签属性值:

```html
<form action="subscribe.html" th:attr="action=@{/subscribe}">
  <fieldset>
    <input type="text" name="email" />
    <input type="submit" value="Subscribe!" th:attr="value=#{subscribe.submit}"/>
  </fieldset>
</form>
```


这个概念非常简单: `th:attr` 只是接受一个为属性赋值的表达式。
创建了相应的控制器和消息文件后，处理该文件的结果为:

```html
<form action="/gtvg/subscribe">
  <fieldset>
    <input type="text" name="email" />
    <input type="submit" value="¡Suscríbe!"/>
  </fieldset>
</form>
```

除了新的属性值之外，您还可以看到应用程序上下文名已经自动添加到 `/gtvg/subscribe` 中的 URL 基前面，如前一章所述。

但是如果我们想一次设置多个属性呢?
XML 规则不允许在标记中两次设置属性，因此 `th:attr` 将采用逗号分隔的赋值列表，例如:

```html
<img src="../../images/gtvglogo.png"
     th:attr="src=@{/images/gtvglogo.png},title=#{logo},alt=#{logo}" />
```

给定所需的消息文件，这将输出:

```html
<img src="/gtgv/images/gtvglogo.png" title="Logo de Good Thymes" alt="Logo de Good Thymes" />
```



5.2 将值设置为特定属性
----------------------------------------

现在，你可能会想:

```html
<input type="submit" value="Subscribe!" th:attr="value=#{subscribe.submit}"/>
```

...是一个相当丑陋的标记。在属性值中指定赋值可能非常实用，但是如果必须一直这样做，
那么它不是创建模板最优雅的方法。


Thymeleaf 同意您的观点，这就是为什么很少在模板中使用 `th:attr` 的原因。
通常，您将使用其他 `th:*` 属性，其任务是设置特定的标记属性(而不是像 `th:attr` 这样的任何属性)。

例如，要设置 `value` 属性，可以使用 `th:value`:

```html
<input type="submit" value="Subscribe!" th:value="#{subscribe.submit}"/>
```

这个看起来好多了!让我们试着对 `form` 标签中的 `action` 属性做同样的处理:

```html
<form action="subscribe.html" th:action="@{/subscribe}">
```

你还记得我们把 `th:href` 放在 `home.html` 里吗?它们是完全相同的属性:

```html
<li><a href="product/list.html" th:href="@{/product/list}">Product List</a></li>
```

有很多这样的属性，每个都针对一个特定的 HTML5 属性:

<div class="table-scroller">
---------------------- ---------------------- ----------------------
`th:abbr`              `th:accept`            `th:accept-charset`    
`th:accesskey`         `th:action`            `th:align`             
`th:alt`               `th:archive`           `th:audio`             
`th:autocomplete`      `th:axis`              `th:background`        
`th:bgcolor`           `th:border`            `th:cellpadding`       
`th:cellspacing`       `th:challenge`         `th:charset`           
`th:cite`              `th:class`             `th:classid`           
`th:codebase`          `th:codetype`          `th:cols`              
`th:colspan`           `th:compact`           `th:content`           
`th:contenteditable`   `th:contextmenu`       `th:data`              
`th:datetime`          `th:dir`               `th:draggable`         
`th:dropzone`          `th:enctype`           `th:for`               
`th:form`              `th:formaction`        `th:formenctype`       
`th:formmethod`        `th:formtarget`        `th:fragment`          
`th:frame`             `th:frameborder`       `th:headers`           
`th:height`            `th:high`              `th:href`              
`th:hreflang`          `th:hspace`            `th:http-equiv`        
`th:icon`              `th:id`                `th:inline`            
`th:keytype`           `th:kind`              `th:label`             
`th:lang`              `th:list`              `th:longdesc`          
`th:low`               `th:manifest`          `th:marginheight`      
`th:marginwidth`       `th:max`               `th:maxlength`         
`th:media`             `th:method`            `th:min`               
`th:name`              `th:onabort`           `th:onafterprint`      
`th:onbeforeprint`     `th:onbeforeunload`    `th:onblur`            
`th:oncanplay`         `th:oncanplaythrough`  `th:onchange`          
`th:onclick`           `th:oncontextmenu`     `th:ondblclick`        
`th:ondrag`            `th:ondragend`         `th:ondragenter`       
`th:ondragleave`       `th:ondragover`        `th:ondragstart`       
`th:ondrop`            `th:ondurationchange`  `th:onemptied`         
`th:onended`           `th:onerror`           `th:onfocus`           
`th:onformchange`      `th:onforminput`       `th:onhashchange`      
`th:oninput`           `th:oninvalid`         `th:onkeydown`         
`th:onkeypress`        `th:onkeyup`           `th:onload`            
`th:onloadeddata`      `th:onloadedmetadata`  `th:onloadstart`       
`th:onmessage`         `th:onmousedown`       `th:onmousemove`       
`th:onmouseout`        `th:onmouseover`       `th:onmouseup`         
`th:onmousewheel`      `th:onoffline`         `th:ononline`          
`th:onpause`           `th:onplay`            `th:onplaying`         
`th:onpopstate`        `th:onprogress`        `th:onratechange`      
`th:onreadystatechange``th:onredo`            `th:onreset`           
`th:onresize`          `th:onscroll`          `th:onseeked`          
`th:onseeking`         `th:onselect`          `th:onshow`            
`th:onstalled`         `th:onstorage`         `th:onsubmit`          
`th:onsuspend`         `th:ontimeupdate`      `th:onundo`            
`th:onunload`          `th:onvolumechange`    `th:onwaiting`         
`th:optimum`           `th:pattern`           `th:placeholder`       
`th:poster`            `th:preload`           `th:radiogroup`        
`th:rel`               `th:rev`               `th:rows`              
`th:rowspan`           `th:rules`             `th:sandbox`           
`th:scheme`            `th:scope`             `th:scrolling`         
`th:size`              `th:sizes`             `th:span`              
`th:spellcheck`        `th:src`               `th:srclang`           
`th:standby`           `th:start`             `th:step`              
`th:style`             `th:summary`           `th:tabindex`          
`th:target`            `th:title`             `th:type`              
`th:usemap`            `th:value`             `th:valuetype`         
`th:vspace`            `th:width`             `th:wrap`              
`th:xmlbase`           `th:xmllang`           `th:xmlspace`          
---------------------- ---------------------- ----------------------
</div>


5.3 一次设置多个值
-----------------------------------------

有两个非常特殊的属性叫做 `th:alt-title` 和 `th:lang-xmllang`，
它们可以用于将两个属性同时设置为相同的值。具体地说:

 * `th:alt-title` 将设置 `alt` 和 `title`.
 * `th:lang-xmllang` 将设置 `lang` 和 `xml:lang`.

对于我们的 GTVG 主页，这将允许我们替换以下内容:

```html
<img src="../../images/gtvglogo.png"
     th:attr="src=@{/images/gtvglogo.png},title=#{logo},alt=#{logo}" />
```

...这等价于：

```html
<img src="../../images/gtvglogo.png"
     th:src="@{/images/gtvglogo.png}" th:title="#{logo}" th:alt="#{logo}" />
```

...或这样

```html
<img src="../../images/gtvglogo.png"
     th:src="@{/images/gtvglogo.png}" th:alt-title="#{logo}" />
```



5.4 Appending and prepending
----------------------------

Thymeleaf 还提供了 `th:attrappend` 和 `th:attrprepend` 属性，
它们将(后缀)或(前缀)的计算结果附加到现有属性值。

例如，您可能希望将要添加(不是设置，只是添加)的 CSS 类的名称存储在上下文变量中的一个按钮中，
因为要使用的特定 CSS 类将取决于用户以前所做的事情:

```html
<input type="button" value="Do it!" class="btn" th:attrappend="class=${' ' + cssStyle}" />
```

如果您在处理这个模板时将 `cssStyle` 变量设置为 `"warning"`，你将得到:

```html
<input type="button" value="Do it!" class="btn warning" />
```
在标准方言中还有两个特定的 _appending attributes_ : `th:classappend` 和 `th:styleappend` 属性，
用于向元素添加 CSS 类或 _style_ 片段，而不覆盖现有的属性:

```html
<tr th:each="prod : ${prods}" class="row" th:classappend="${prodStat.odd}? 'odd'">
```

(不要担心 `th:each` 属性。它是一个 _iterating attribute_，稍后我们将讨论它)。



5.5 固定值的布尔属性
----------------------------------

HTML 有*布尔属性*的概念，这些属性没有值，其中一个的值表示值为 “true”。
在 XHTML 中，这些属性只取 1 个值，也就是它本身。

例如，`checked`:

```html
<input type="checkbox" name="option2" checked /> <!-- HTML -->
<input type="checkbox" name="option1" checked="checked" /> <!-- XHTML -->
```

标准方言包括一些属性，这些属性允许您通过计算条件来设置这些属性，
因此，如果计算为真，属性将被设置为其固定值，如果计算为假，属性将不会被设置:

```html
<input type="checkbox" name="active" th:checked="${user.active}" />
```

以下固定值布尔属性存在于标准方言中:

<div class="table-scroller">
------------------- ------------------ ------------------
`th:async`          `th:autofocus`     `th:autoplay`      
`th:checked`        `th:controls`      `th:declare`       
`th:default`        `th:defer`         `th:disabled`      
`th:formnovalidate` `th:hidden`        `th:ismap`         
`th:loop`           `th:multiple`      `th:novalidate`    
`th:nowrap`         `th:open`          `th:pubdate`       
`th:readonly`       `th:required`      `th:reversed`      
`th:scoped`         `th:seamless`      `th:selected`      
------------------- ------------------ ------------------
</div>



5.6 设置任何属性的值(默认属性处理器)
--------------------------------------------------------------------

Thymeleaf 提供了一个*默认属性处理器*，允许我们设置*任何*属性的值，
即使没有以标准方言为其定义特定的 `th:*` 处理器。

类似:

```html
<span th:whatever="${user.name}">...</span>
```

结果为：

```html
<span whatever="John Apricot">...</span>
```



5.7 支持 HTML5 友好的属性和元素名称
----------------------------------------------------------

还可以使用完全不同的语法，以更加 HTML5 友好的方式将处理器应用于模板。

```html
<table>
    <tr data-th-each="user : ${users}">
        <td data-th-text="${user.login}">...</td>
        <td data-th-text="${user.name}">...</td>
    </tr>
</table>
```

`data-{prefix}-{name}` 语法是在 HTML5 中编写定制属性的标准方式，
不需要开发人员使用任何名称空间名称，如 `th:*`。
Thymeleaf 使这种语法自动适用于所有方言(不仅仅是标准方言)。

还有一种语法来指定定制标记：`{prefix}-{name}`，它遵循*W3C定制元素规范*(更大的*W3C Web组件规范*的一部分)。
例如，这可以用于 `th:block` 元素(也可以用于 `th-block` 元素)，后面的部分将对此进行解释。

**重要:** 这个语法是对名称空间为 `th:*` 的语法的补充，它不替换它。将来完全没有打算反对使用名称空间语法。




6 迭代
===========

到目前为止，我们已经创建了一个主页，一个用户资料页面，
以及一个让用户订阅我们的时事通讯的页面…但是我们的产品呢?
为此，我们需要一种方法来迭代集合中的项，以构建我们的产品页面。



6.1 迭代基础
--------------------

在我们的 `/WEB-INF/templates/product/list.html` 中显示产品。
我们将使用一个表格。每一个我们的产品将显示在一行(`<tr>` 元素),
所以对于我们的模板，我们需要创建一个*模板行* ——一个将举例说明我们希望如何展示每个产品，
然后显示指示 Thymeleaf 重复,每个产品一次。

标准方言为我们提供了一个属性：`th:each`。


### 使用 th:each

对于我们的产品列表页面，我们需要一个控制器方法从服务层检索产品列表并将其添加到模板上下文:

```java
public void process(
        final HttpServletRequest request, final HttpServletResponse response,
        final ServletContext servletContext, final ITemplateEngine templateEngine)
        throws Exception {

    ProductService productService = new ProductService();
    List<Product> allProducts = productService.findAll();

    WebContext ctx = new WebContext(request, response, servletContext, request.getLocale());
    ctx.setVariable("prods", allProducts);

    templateEngine.process("product/list", ctx, response.getWriter());

}
```

然后我们将在模板中模板中使用 `th:each` 来迭代产品列表:

```html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="../../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
  </head>

  <body>

    <h1>Product list</h1>

    <table>
      <tr>
        <th>NAME</th>
        <th>PRICE</th>
        <th>IN STOCK</th>
      </tr>
      <tr th:each="prod : ${prods}">
        <td th:text="${prod.name}">Onions</td>
        <td th:text="${prod.price}">2.41</td>
        <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
      </tr>
    </table>

    <p>
      <a href="../home.html" th:href="@{/}">Return to home</a>
    </p>

  </body>

</html>
```

That `prod : ${prods}` attribute value you see above means "for each element in
the result of evaluating `${prods}`, repeat this fragment of template, using the
current element in a variable called prod". Let's give a name each of the things
we see:

您在上面看到的 `prod : ${prods}` 属性值表示：“对于计算 `${prods}` 结果中的每个元素，
使用名为 prod 的变量中的当前元素来重复这个模板片段”。让我们给每一个我们看到的事物命名:

 * 我们称 `${prods}` 为*迭代表达式*或*被迭代变量*
 * 我们称 `prod` 为*迭代变量*

注意，`prod` 迭代变量的作用域是 `<tr>` 元素，这意味着它可以用于像 `<td>` 这样的内部标记。


### 可迭代的值

`java.util.List` 类并不是在 Thymeleaf 中唯一可以用于迭代的值。
`th:each` 属性认为一下的对象集都是可迭代的：

 * 任何实现了 `java.util.Iterable` 的对象
 * 任何实现了 `java.util.Enumeration` 的对象
 * 任何实现了 `java.util.Iterator` 的对象，其值将在迭代器返回时使用，而不需要在内存中缓存所有值。
 * 任何实现了 `java.util.Map` 的对象，当迭代 map 时，迭代变量将会是 `java.util.Map.Entry` 类 
 * 任何数组
 * 任何其他对象都将被视为包含该对象本身的单值列表。



6.2 保持迭代状态
----------------------------

在使用 `th:each` 时，Thymeleaf 提供了一种用于跟踪迭代状态的机制：状态变量。

状态变量定义在 `th:each` 属性中，包含以下数据:

 * `index` 属性： 当前的*迭代索引*，从 0 开始
 * `count` 属性： 当前的*迭代计数*，从 1 开始
 * `size` 属性：被迭代变量的元素个数
 * `current` 属性：对应每一次迭代的迭代变量
 * `even/odd` 属性：当前迭代是偶数还是奇数。
 * `first` 属性：当前迭代是否是第一个迭代。
 * `index` 属性：当前迭代是否为最后一次迭代。这是最后一个布尔属性。

让我们看看如何在前面的例子中使用它:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
  </tr>
  <tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
  </tr>
</table>
```

状态变量(在本例中为 `iterStat`)在 `th:each` 属性中定义，方法是在迭代变量本身后面用逗号分隔。
就像迭代变量一样，状态变量的作用域也限定在包含 `th:each` 属性的标记定义的代码片段。

让我们来看看处理我们的模板的结果:

```html
<!DOCTYPE html>

<html>

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta content="text/html; charset=UTF-8" http-equiv="Content-Type"/>
    <link rel="stylesheet" type="text/css" media="all" href="/gtvg/css/gtvg.css" />
  </head>

  <body>

    <h1>Product list</h1>

    <table>
      <tr>
        <th>NAME</th>
        <th>PRICE</th>
        <th>IN STOCK</th>
      </tr>
      <tr class="odd">
        <td>Fresh Sweet Basil</td>
        <td>4.99</td>
        <td>yes</td>
      </tr>
      <tr>
        <td>Italian Tomato</td>
        <td>1.25</td>
        <td>no</td>
      </tr>
      <tr class="odd">
        <td>Yellow Bell Pepper</td>
        <td>2.50</td>
        <td>yes</td>
      </tr>
      <tr>
        <td>Old Cheddar</td>
        <td>18.75</td>
        <td>yes</td>
      </tr>
    </table>

    <p>
      <a href="/gtvg/" shape="rect">Return to home</a>
    </p>

  </body>

</html>
```

注意，我们的迭代状态变量工作得很好，只对奇数行建立了 `odd` CSS类。

如果您没有显式地设置状态变量，Thymeleaf 将始终为您创建一个状态变量，方法是在迭代变量的名称后面加上 `Stat`:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
  </tr>
</table>
```



6.3 通过懒检索数据进行优化
---------------------------------------------

有时，我们可能希望优化数据集合的检索(例如从数据库中检索)，以便只有在真正要使用这些集合时才能检索它们。

> 实际上，这可以应用于*任何*数据块，但是考虑到内存集合可能具有的大小，检索要迭代的集合是本场景中最常见的情况。


为了支持这一点，Thymeleaf 提供了一种机制来*懒加载上下文变量*。
实现 `ILazyContextVariable` 接口的上下文变量——很可能是通过扩展其 `LazyContextVariable` 默认实现实现的——将在执行时解析。例如:

```java
context.setVariable(
     "users",
     new LazyContextVariable<List<User>>() {
         @Override
         protected List<User> loadValue() {
             return databaseRepository.findAllUsers();
         }
     });
```

在代码中，这个变量可以在不知道其*懒惰*的情况下使用，例如:

```html
<ul>
  <li th:each="u : ${users}" th:text="${u.name}">user name</li>
</ul>
```

但同时，如果 `condition` 在代码中计算为 `false`，则永远不会初始化(其 `loadValue()` 方法永远不会被调用)，例如:

```html
<ul th:if="${condition}">
  <li th:each="u : ${users}" th:text="${u.name}">user name</li>
</ul>
```




7 条件评估
========================



7.1 简单条件: "if" 和 "unless"
------------------------------------------

有时候，您需要模板的一个片段，只有在满足特定条件时才会出现在结果中。

例如，假设我们希望在产品表中显示一个列，列中包含每个产品的评论数量，如果有评论，则显示到该产品的评论详细页面的链接。

为了做到这一点，我们将使用 `th:if` 属性:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    <td>
      <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
      <a href="comments.html"
         th:href="@{/product/comments(prodId=${prod.id})}"
         th:if="${not #lists.isEmpty(prod.comments)}">view</a>
    </td>
  </tr>
</table>
```

这里有很多东西值得一看，所以让我们关注一下重要的一行:

```html
<a href="comments.html"
   th:href="@{/product/comments(prodId=${prod.id})}"
   th:if="${not #lists.isEmpty(prod.comments)}">view</a>
```

这将创建一个到评论页面的链接(URL为 `/product/comments`)，并将 `prodId` 参数设置为产品的 `id`，但前提是该产品有评论。

让我们看看生成的标记:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr>
    <td>Fresh Sweet Basil</td>
    <td>4.99</td>
    <td>yes</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr class="odd">
    <td>Italian Tomato</td>
    <td>1.25</td>
    <td>no</td>
    <td>
      <span>2</span> comment/s
      <a href="/gtvg/product/comments?prodId=2">view</a>
    </td>
  </tr>
  <tr>
    <td>Yellow Bell Pepper</td>
    <td>2.50</td>
    <td>yes</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr class="odd">
    <td>Old Cheddar</td>
    <td>18.75</td>
    <td>yes</td>
    <td>
      <span>1</span> comment/s
      <a href="/gtvg/product/comments?prodId=4">view</a>
    </td>
  </tr>
</table>
```

完美!这正是我们想要的。

注意，`th:if` 属性不仅会评估*布尔*条件。
它的能力略高于此，它将按照以下规则将指定的表达式计算为`true`:


 * 如果值不为空:
    * 如果值为一个布尔值并且值为 `true`.
    * 如果值为一个非 0 数字
    * 如果值为一个非 0 字符
    * 如果置为非 "false", "off" 或 "no" 的字符串
    * 如果值不是一个布尔值，数字，字符或者一个字符串
 * (如果值为null，则 `th:if` 将计算为 `false`)

此外，`th:if` 有一个相反的属性，`th:unless`，我们可以在前面的例子中使用，而不是在 OGNL 表达式中使用 `not`:

```html
<a href="comments.html"
   th:href="@{/comments(prodId=${prod.id})}"
   th:unless="${#lists.isEmpty(prod.comments)}">view</a>
```



7.2 Switch语句
---------------------


还有一种方法可以使用 Java 中等价的 _switch_ 结构有条件地显示内容：`th:switch` / `th:case` 属性集。

```html
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
</div>
```

请注意，一旦一个 `th:case`  属性被计算为 `true`，那么在同一个开关上下文中，其他每个 `th:case` 属性都会被计算为 `false`。

默认选项指定为 `th:case="*"`:


```html
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <!-- 相当于 switch 语句中的 default -->
  <p th:case="*">User is some other thing</p> 
</div>
```




8 模板布局
=================



8.1 包含模板片段
--------------------------------

### 定义和引用片段

在我们的模板中，我们通常希望包含来自其他模板的部分，比如页脚、页眉、菜单……

为了做到这一点，Thymeleaf 需要我们定义这些部分，"fragments"，用于包含，
这可以使用 `th:fragment` 属性来完成。

Say we want to add a standard copyright footer to all our grocery pages, so we
create a `/WEB-INF/templates/footer.html` file containing this code:

假设我们想要添加一个标准的版权页脚到我们所有的杂货店页面，
所以我们创建一个 `/WEB-INF/templates/footer.html` 页脚文件包含以下代码:

```html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <body>

    <div th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
    </div>

  </body>

</html>
```

上面的代码定义了一个名为 `copy` 的片段，
我们可以使用 `th:insert` 或 `th:replace` 属性
(还有 `th:include` ，尽管自 Thymeleaf 3.0 以来不再推荐使用它)轻松地将其包含在我们的主页中:

```html
<body>

  ...

  <div th:insert="~{footer :: copy}"></div>

</body>
```

注意，`th:insert` 需要一个 *片段表达式* (`~{...}`)，
这是*一个片段结果的表达式*。
在上面的例子中，是一个非复杂的*片段表达式*，`~{`,`}`）封装是完全可选的，因此上面的代码将等于:

```html
<body>

  ...

  <div th:insert="footer :: copy"></div>

</body>
```


### 片段规范语法

*片段表达式*的语法非常简单。有三种不同的格式:

 * `"~{templatename::selector}"` 包含在名为 `templatename` 的模板上应用指定标记选择器产生的片段。
   请注意，`selector` 可能只是一个片段名称，因此您可以指定像上面的 `~{footer :: copy}`
   中的 `~{templatename::fragmentname}` 这样简单的东西。

   > 标记选择器语法由底层 AttoParser 解析库定义，类似于 XPath 表达式或 CSS 选择器。
   > 有关更多信息，请参见 [Appendix C](#appendix-c-markup-selector-syntax)。

 * `"~{templatename}"` 包含了名为 `templatename` 完整模板。

   > 注意，您在 `th:insert`/`th:replace` 标记中使用的模板名称必须由模板引擎当前使用的模板解析器解析。

 * `~{::selector}"` 或 `"~{this::selector}"` 从相同的模板插入一个片段，匹配 `selector` 的片段。
   如果在表达式出现的模板中没有找到，那么模板调用的堆栈(插入)将被遍历到最初处理的模板(*root*)，直到 `selector` 在某一级匹配为止。

上面例子中的 `templatename` 和 `selector` 都可以是全功能的表达式(甚至是条件句!)


```html
<div th:insert="footer :: (${user.isAdmin}? #{footer.admin} : #{footer.normaluser})"></div>
```

再次注意， 在 `th:insert`/`th:replace` 中，`~{...}` 包装层是可选的。

片段可以包含任何 `th:*` 属性。一旦片段被包含到目标模板中(带有 `th:insert`/`th:replace` 属性的那个)，
这些属性将被计算，它们将能够引用在目标模板中定义的任何上下文变量。

> 这种方法的一大优点是，您可以在浏览器完美地显示的页面中编写片段，
> 使用完整的、甚至是*有效的*标记结构，同时仍然能够使 Thymeleaf 将它们包含到其他模板中。


### 不使用 `th:fragment` 的引用片段

由于标记选择器的强大功能，我们可以包含不使用任何 `th:fragment` 属性的片段。
它甚至可以是来自完全不了解 Thymeleaf 的另一个应用程序的标记代码:

```html
...
<div id="copy-section">
  &copy; 2011 The Good Thymes Virtual Grocery
</div>
...
```

我们可以使用上面的片段简单地引用它的 `id` 属性，类似于 CSS 选择器:

```html
<body>

  ...

  <div th:insert="~{footer :: #copy-section}"></div>

</body>
```



### `th:insert` 和 `th:replace`(和 `th:include`)之间的区别

`th:insert` 和 `th:replace`(以及 `th:include`，自3.0版本以来不推荐使用)之间的区别是什么?

 * `th:insert` 是最简单的: 它将简单地插入指定的片段作为其宿主标签的主体。

 * `th:replace` 实际上是用指定的片段*替换*它的宿主标签。

 * `th:include` 类似于 `th:insert`，但是它没有插入片段，而是只插入片段的 *内容*。

有这样一个 HTML 片段：

```html
<footer th:fragment="copy">
  &copy; 2011 The Good Thymes Virtual Grocery
</footer>
```

...在宿主 `<div>` 标签中包含三次，如下:

```html
<body>

  ...

  <div th:insert="footer :: copy"></div>

  <div th:replace="footer :: copy"></div>

  <div th:include="footer :: copy"></div>

</body>
```

...结果为：

```html
<body>

  ...

  <div>
    <footer>
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
  </div>

  <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
  </footer>

  <div>
    &copy; 2011 The Good Thymes Virtual Grocery
  </div>

</body>
```



8.2 可参数化的片段签名
---------------------------------------

为了为模板片段创建一个更像*函数*的机制，使用 `th:fragment` 定义的片段可以指定一组参数:

```html
<div th:fragment="frag (onevar,twovar)">
    <p th:text="${onevar} + ' - ' + ${twovar}">...</p>
</div>
```

这需要使用 `th:insert` 或 `th:replace` 两种语法中的一种来调用片段:

```html
<div th:replace="::frag (${value1},${value2})">...</div>
<div th:replace="::frag (onevar=${value1},twovar=${value2})">...</div>
```
注意最后一个选项中的顺序并不重要:

```html
<div th:replace="::frag (twovar=${value2},onevar=${value1})">...</div>
```


### 不带参数的片段本地变量

即使片段的定义没有参数:

```html
<div th:fragment="frag">
    ...
</div>
```

我们可以使用上面指定的第二种语法来调用它们(并且只能使用第二种):

```html
<div th:replace="::frag (onevar=${value1},twovar=${value2})">
```

这相当于 `th:replace` 和 `th:with` 的组合:

```html
<div th:replace="::frag" th:with="onevar=${value1},twovar=${value2}">
```

**注意** 这个片段局部变量的规范——不管它是否有参数签名——不会导致在执行之前清空上下文。
fragment 仍然能够像现在一样访问调用模板中使用的每个上下文变量。


### th:assert 模板内的断言

`th:assert` 属性可以指定一个逗号分隔的表达式列表，
这些表达式应该被求值，并为每个求值生成 true，否则会引发异常。

```html
<div th:assert="${onevar},(${twovar} != 43)">...</div>
```

这对于验证片段签名的参数非常方便:

```html
<header th:fragment="contentheader(title)" th:assert="${!#strings.isEmpty(title)}">...</header>
```



8.3 灵活的布局:不仅仅是插入片段
----------------------------------------------------

由于*片段表达式*，我们可以为非文本、数字、bean 对象的片段指定参数……而是标记的片段。

这允许我们以一种方式创建片段，使它们可以通过调用模板的标记得到丰富，从而产生非常灵活的**模板布局机制**。

注意下面片段中 `title` 和 `links` 变量的使用:

```html
<head th:fragment="common_header(title,links)">

  <title th:replace="${title}">The awesome application</title>

  <!-- 公共样式和脚本 -->
  <link rel="stylesheet" type="text/css" media="all" th:href="@{/css/awesomeapp.css}">
  <link rel="shortcut icon" th:href="@{/images/favicon.ico}">
  <script type="text/javascript" th:src="@{/sh/scripts/codebase.js}"></script>

  <!--/* 用于额外链接的每页的占位符 */-->
  <th:block th:replace="${links}" />

</head>
```

我们现在可以这样调用这个片段:

```html
...
<head th:replace="base :: common_header(~{::title},~{::link})">

  <title>Awesome - Main</title>

  <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
  <link rel="stylesheet" th:href="@{/themes/smoothness/jquery-ui.css}">

</head>
...
```

...结果将使用我们调用模板中实际的 `<title>` 和 `<link>` 标签作为 `title` 和 `links` 变量的值，
这将导致我们的片段在插入时被定制:

```html
...
<head>

  <title>Awesome - Main</title>

  <!-- Common styles and scripts -->
  <link rel="stylesheet" type="text/css" media="all" href="/awe/css/awesomeapp.css">
  <link rel="shortcut icon" href="/awe/images/favicon.ico">
  <script type="text/javascript" src="/awe/sh/scripts/codebase.js"></script>

  <link rel="stylesheet" href="/awe/css/bootstrap.min.css">
  <link rel="stylesheet" href="/awe/themes/smoothness/jquery-ui.css">

</head>
...
```


### 使用空片段


一个特殊的片段表达式，*空片段*(`~{}`)，可以用来指定*没有标记*的情况。使用前面的例子:

```html
<head th:replace="base :: common_header(~{::title},~{})">

  <title>Awesome - Main</title>

</head>
...
```

注意，片段(`links`)的第二个参数是如何设置为*空片段*的，
因此，对于 `<th:block th:replace="${links}" />` 块来讲，什么也没有写。

```html
...
<head>

  <title>Awesome - Main</title>

  <!-- Common styles and scripts -->
  <link rel="stylesheet" type="text/css" media="all" href="/awe/css/awesomeapp.css">
  <link rel="shortcut icon" href="/awe/images/favicon.ico">
  <script type="text/javascript" src="/awe/sh/scripts/codebase.js"></script>

</head>
...
```


### 使用无操作 token


如果我们只想让片段使用其当前标记作为默认值，那么 no-op 也可以用作片段的参数。
同样，使用 `common_header` 示例:

```html
...
<head th:replace="base :: common_header(_,~{::link})">

  <title>Awesome - Main</title>

  <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
  <link rel="stylesheet" th:href="@{/themes/smoothness/jquery-ui.css}">

</head>
...
```

查看 `title` 参数(`common_header` 片段的第一个参数)如何设置为*no-op*(`_`)，
从而导致片段的这一部分根本没有执行(`title` = *no-operation*):

```html
  <title th:replace="${title}">The awesome application</title>
```

所以结果是:

```html
...
<head>

  <title>The awesome application</title>

  <!-- Common styles and scripts -->
  <link rel="stylesheet" type="text/css" media="all" href="/awe/css/awesomeapp.css">
  <link rel="shortcut icon" href="/awe/images/favicon.ico">
  <script type="text/javascript" src="/awe/sh/scripts/codebase.js"></script>

  <link rel="stylesheet" href="/awe/css/bootstrap.min.css">
  <link rel="stylesheet" href="/awe/themes/smoothness/jquery-ui.css">

</head>
...
```


### 高级条件插入片段

*空片段*和*无操作 token*的可用性允许我们以一种非常简单和优雅的方式执行片段的条件插入。

例如，如果用户是管理员，我们可以*只*插入 `common :: adminhead` 片段，
如果用户不是管理员，则什么也不插入(空片段):

```html
...
<div th:insert="${user.isAdmin()} ? ~{common :: adminhead} : ~{}">...</div>
...
```

此外，我们还可以使用 *无操作 token*，
以便仅在满足指定条件的情况下插入片段，而在不满足条件的情况下不修改标记:

```html
...
<div th:insert="${user.isAdmin()} ? ~{common :: adminhead} : _">
    Welcome [[${user.name}]], click <a th:href="@{/support}">here</a> for help-desk support.
</div>
...
```

Additionally, if we have configured our template resolvers to *check for
existence* of the template resources –- by means of their `checkExistence` flag
-– we can use the existence of the fragment itself as the condition in a *default*
operation:

此外，如果我们将模板解析器配置为*检查片段是否存在*于模板资源中——
通过它们的 `checkExistence` 标志——我们可以使用片段本身的存在作为*default*操作的条件:

```html
...
<!-- 如果 "common :: salutation" 片段不存在(或为空)，将使用 <div> 的主体。 -->
<div th:insert="~{common :: salutation} ?: _">
    Welcome [[${user.name}]], click <a th:href="@{/support}">here</a> for help-desk support.
</div>
...
```



8.4 删除模板片段
-------------------------------

回到示例应用程序，让我们回顾一下产品列表模板的上一个版本:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    <td>
      <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
      <a href="comments.html"
         th:href="@{/product/comments(prodId=${prod.id})}"
         th:unless="${#lists.isEmpty(prod.comments)}">view</a>
    </td>
  </tr>
</table>
```

这段代码可以作为模板，但是作为一个静态页面(当浏览器直接打开而不需要 Thymeleaf 处理它时)，它就不是一个很好的原型。

为什么?因为，尽管浏览器可以完美地显示该表，但该表只有一行，而这一行有模拟数据。
作为一个原型，它看起来不够逼真……我们应该有不止一种产品，我们需要更多的产品。

所以让我们添加一些:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    <td>
      <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
      <a href="comments.html"
         th:href="@{/product/comments(prodId=${prod.id})}"
         th:unless="${#lists.isEmpty(prod.comments)}">view</a>
    </td>
  </tr>
  <tr class="odd">
    <td>Blue Lettuce</td>
    <td>9.55</td>
    <td>no</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr>
    <td>Mild Cinnamon</td>
    <td>1.99</td>
    <td>yes</td>
    <td>
      <span>3</span> comment/s
      <a href="comments.html">view</a>
    </td>
  </tr>
</table>
```

好了，现在我们有三个，对于一个原型来说肯定更好。
但是...当我们用 Thymeleaf 处理时会发生什么?

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr>
    <td>Fresh Sweet Basil</td>
    <td>4.99</td>
    <td>yes</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr class="odd">
    <td>Italian Tomato</td>
    <td>1.25</td>
    <td>no</td>
    <td>
      <span>2</span> comment/s
      <a href="/gtvg/product/comments?prodId=2">view</a>
    </td>
  </tr>
  <tr>
    <td>Yellow Bell Pepper</td>
    <td>2.50</td>
    <td>yes</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr class="odd">
    <td>Old Cheddar</td>
    <td>18.75</td>
    <td>yes</td>
    <td>
      <span>1</span> comment/s
      <a href="/gtvg/product/comments?prodId=4">view</a>
    </td>
  </tr>
  <tr class="odd">
    <td>Blue Lettuce</td>
    <td>9.55</td>
    <td>no</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr>
    <td>Mild Cinnamon</td>
    <td>1.99</td>
    <td>yes</td>
    <td>
      <span>3</span> comment/s
      <a href="comments.html">view</a>
    </td>
  </tr>
</table>
```

最后两行是模拟行!它们当然是:迭代只应用于第一行，所以没有理由 Thymeleaf 应该删除其他两行。

我们需要一种方法在模板处理期间删除这两行。让我们在第二个和第三个 `<tr>` 标记上使用 `th:remove` 属性:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    <td>
      <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
      <a href="comments.html"
         th:href="@{/product/comments(prodId=${prod.id})}"
         th:unless="${#lists.isEmpty(prod.comments)}">view</a>
    </td>
  </tr>
  <tr class="odd" th:remove="all">
    <td>Blue Lettuce</td>
    <td>9.55</td>
    <td>no</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr th:remove="all">
    <td>Mild Cinnamon</td>
    <td>1.99</td>
    <td>yes</td>
    <td>
      <span>3</span> comment/s
      <a href="comments.html">view</a>
    </td>
  </tr>
</table>
```

一旦处理完毕，一切都会恢复原样:

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr>
    <td>Fresh Sweet Basil</td>
    <td>4.99</td>
    <td>yes</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr class="odd">
    <td>Italian Tomato</td>
    <td>1.25</td>
    <td>no</td>
    <td>
      <span>2</span> comment/s
      <a href="/gtvg/product/comments?prodId=2">view</a>
    </td>
  </tr>
  <tr>
    <td>Yellow Bell Pepper</td>
    <td>2.50</td>
    <td>yes</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr class="odd">
    <td>Old Cheddar</td>
    <td>18.75</td>
    <td>yes</td>
    <td>
      <span>1</span> comment/s
      <a href="/gtvg/product/comments?prodId=4">view</a>
    </td>
  </tr>
</table>
```


属性中的 `all` 值是什么意思？`th:remove` 可以有五种不同的行为方式，具体取决于它的值:

 * `all`: 移除包含标签及其所有子标签。
 * `body`: 不要删除包含标签，而是删除它的所有子标签。
 * `tag`: 删除包含标签，但不要删除其子标签。
 * `all-but-first`: 除去包含标记的所有子标记，第一个除外。
 * `none` : 什么也不做。这个值对于动态评估很有用。


这种 `all-but-first` 的值有什么用呢？它将让我们保存一些 `th:remove="all"` 时原型:

```html
<table>
  <thead>
    <tr>
      <th>NAME</th>
      <th>PRICE</th>
      <th>IN STOCK</th>
      <th>COMMENTS</th>
    </tr>
  </thead>
  <tbody th:remove="all-but-first">
    <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
      <td th:text="${prod.name}">Onions</td>
      <td th:text="${prod.price}">2.41</td>
      <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
      <td>
        <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
        <a href="comments.html"
           th:href="@{/product/comments(prodId=${prod.id})}"
           th:unless="${#lists.isEmpty(prod.comments)}">view</a>
      </td>
    </tr>
    <tr class="odd">
      <td>Blue Lettuce</td>
      <td>9.55</td>
      <td>no</td>
      <td>
        <span>0</span> comment/s
      </td>
    </tr>
    <tr>
      <td>Mild Cinnamon</td>
      <td>1.99</td>
      <td>yes</td>
      <td>
        <span>3</span> comment/s
        <a href="comments.html">view</a>
      </td>
    </tr>
  </tbody>
</table>
```

`th:remove` 属性可以接受任何 *Thymeleaf 标准表达式*，
只要它返回一个允许的字符串值(`all`, `tag`, `body`, `all-but-first` 或 `none`)。

这意味着删除可能是有条件的，比如:

```html
<a href="/something" th:remove="${condition}? tag : none">Link text not to be removed</a>
```

还要注意，`th:remove` 认为 `null` 是 `none` 的同义词，所以下面的操作与上面的示例相同:

```html
<a href="/something" th:remove="${condition}? tag">Link text not to be removed</a>
```

在这种情况下，如果 `${condition}` 为 false，则返回 `null`，因此不会执行删除操作。


8.5 布局继承
----------------------

为了能够将单个文件作为布局，可以使用片段。一个简单布局的例子，
有 `title` 和 `content` 并使用 `th:fragment` 和 `th:replace`：

```html
<!DOCTYPE html>
<html th:fragment="layout (title, content)" xmlns:th="http://www.thymeleaf.org">
<head>
    <title th:replace="${title}">Layout Title</title>
</head>
<body>
    <h1>Layout H1</h1>
    <div th:replace="${content}">
        <p>Layout content</p>
    </div>
    <footer>
        Layout footer
    </footer>
</body>
</html>
```


这个例子声明了一个名为**layout**的片段，其中 _title_ 和 _content_ 作为参数。
下面的示例中提供的片段表达式将在继承它的页面上替换这两个表达式。

```html
<!DOCTYPE html>
<html th:replace="~{layoutFile :: layout(~{::title}, ~{::section})}">
<head>
    <title>Page Title</title>
</head>
<body>
<section>
    <p>Page content</p>
    <div>Included on page</div>
</section>
</body>
</html>
```

在这个文件中，`html` 标签将被 _layout_ 替换，
但在布局 `title` 和 `content` 将分别被 `title` 和 `section` 块替换。

如果需要，布局可以由几个片段组成，如 _header_ 和 _footer_。



9 局部变量
=================

Thymeleaf 调用 _局部变量_ 变量，这些变量是为模板的特定片段定义的，并且只能在该片段中进行求值。

我们已经看到的一个例子是产品列表页面中的 `prod` 迭代变量:

```html
<tr th:each="prod : ${prods}">
    ...
</tr>
```

这个 `prod` 变量只能在 `<tr>` 标签的范围内可用。具体地说:

 * 它可以用于标记中执行的任何其他 `th:*` 属性，
   这些属性的 _beforedence_ 小于 `th:each`(这意味着它们将在 `th:each` 之后执行)。
 * 它可以用于 `<tr>` 标记的任何子元素，例如任何 `<td>` 元素。

Thymeleaf 提供了一种无需迭代就可以声明局部变量的方法，使用 `th:with` 属性，其语法类似于属性值赋值:

```html
<div th:with="firstPer=${persons[0]}">
  <p>
    The name of the first person is <span th:text="${firstPer.name}">Julius Caesar</span>.
  </p>
</div>
```

当 `th:with` 被处理时，`firstPer` 变量被创建为一个局部变量，
并添加到来自上下文的变量映射中，这样它就可以与上下文中声明的任何其他变量一起进行计算，
但只能在包含 `<div>` 标记的范围内。

您可以使用通常的多重赋值语法同时定义多个变量:

```html
<div th:with="firstPer=${persons[0]},secondPer=${persons[1]}">
  <p>
    The name of the first person is <span th:text="${firstPer.name}">Julius Caesar</span>.
  </p>
  <p>
    But the name of the second person is
    <span th:text="${secondPer.name}">Marcus Antonius</span>.
  </p>
</div>
```

`th:with` 属性允许重用在同一属性中定义的变量:

```html
<div th:with="company=${user.company + ' Co.'},account=${accounts[company]}">...</div>
```

让我们在杂货店的主页上使用它吧!还记得我们编写的输出格式化日期的代码吗?

```html
<p>
  Today is:
  <span th:text="${#calendars.format(today,'dd MMMM yyyy')}">13 february 2011</span>
</p>
```

好吧，如果我们想要那个 `"dd MMMM yyyy"` 实际上取决于地区呢？例如，我们可能希望将以下消息添加到 `home_en.properties`：

```
date.format=MMMM dd'','' yyyy
```

...等价于 `home_es.properties`:

```
date.format=dd ''de'' MMMM'','' yyyy
```

现在，让我们使用 `th:with` 将本地化的日期格式转换为一个变量，然后在我们的 `th:text` 表达式中使用它:

```html
<p th:with="df=#{date.format}">
  Today is: <span th:text="${#calendars.format(today,df)}">13 February 2011</span>
</p>
```

这既干净又容易。事实上，考虑到 `th:with` 比 `th:text` 具有更高的 `precedence`(优先级)，
我们可以在 `span` 标记中解决所有问题:


```html
<p>
  Today is:
  <span th:with="df=#{date.format}"
        th:text="${#calendars.format(today,df)}">13 February 2011</span>
</p>
```

你可能会想:优先?我们还没说呢!别担心，这正是下一章的内容。


10 属性优先级
=======================

当您在同一个标记中编写多个 `th:*` 属性时会发生什么？例如:

```html
<ul>
  <li th:each="item : ${items}" th:text="${item.description}">Item description here...</li>
</ul>
```

我们期望的 `th:each` 属性在 `th:text` 之前执行，这样我们得到我们想要的结果，
但考虑到 HTML/XML 标准不给书写的任何意义的标签的属性给定顺序，
_precedence_ 机制必须建立在自己的属性以确保这将正常工作。

因此，所有的 Thymeleaf 属性都定义了一个数字优先级，它确定了它们在标记中执行的顺序。这个顺序是:

<div class="table-scroller">
-----------------------------------------------------------------
顺序   特性                            属性
------- ---------------------------------- ----------------------
      1 片段包含                              `th:insert`\
                                            `th:replace`

      2 片段迭代                              `th:each`

      3 条件计算                            `th:if`\
                                           `th:unless`\
                                           `th:switch`\
                                           `th:case`

      4 局部变量定义                         `th:object`\
                                           `th:with`

      5 通用属性修改                         `th:attr`\
                                           `th:attrprepend`\
                                           `th:attrappend`

      6 特定属性修改                         `th:value`\
                                           `th:href`\
                                           `th:src`\
                                           `...`

      7 文本(标签主体修改)                    `th:text`\
                                           `th:utext`

      8 片段规范                            `th:fragment`

      9 片段删除                            `th:remove`
-----------------------------------------------------------------
</div>

这种优先机制意味着，如果属性位置被颠倒，上面的迭代片段将给出完全相同的结果(尽管它的可读性略差):

```html
<ul>
  <li th:text="${item.description}" th:each="item : ${items}">Item description here...</li>
</ul>
```




11 注释和块
======================



11.1. 标准 HTML/XML 注释
--------------------------------


标准 HTML/XML 注释 `<!-- ... -->` 可以在 Thymeleaf 模板的任何地方使用。
Thymeleaf 不会处理这些注释中的任何内容，并将逐字复制到结果中:

```html
<!-- User info follows -->
<div th:text="${...}">
  ...
</div>
```



11.2. Thymeleaf 解析器级注释块
-------------------------------------------

解析器级别的注释块是在 Thymeleaf 解析模板时将从模板中简单删除的代码。它们是这样的:

```html
<!--/* This code will be removed at Thymeleaf parsing time! */-->
```

Thymeleaf 将删除 `<!--/*` 和 `*/-->`，
所以当模板静态打开时，这些注释块也可以用来显示代码，知道当 Thymeleaf 处理它时，它将被删除:

```html
<!--/*-->
  <div>
     you can see me only before Thymeleaf processes me!
  </div>
<!--*/-->
```

这对于很多 `<tr>` 的表格原型来说可能非常方便:

```html
<table>
   <tr th:each="x : ${xs}">
     ...
   </tr>
   <!--/*-->
   <tr>
     ...
   </tr>
   <tr>
     ...
   </tr>
   <!--*/-->
</table>
```



11.3. 只使用 Thymeleaf 原型的注释块
---------------------------------------------

Thymeleaf 允许定义特殊的注释块，当模板静态打开时(即作为原型)标记为注释，
但是在执行模板时，Thymeleaf 认为这是正常的标记。

```html
<span>hello!</span>
<!--/*/
  <div th:text="${...}">
    ...
  </div>
/*/-->
<span>goodbye!</span>
```

Thymeleaf 的解析系统将简单地删除'`<!--/*/` 和 `/*/-->` 标记，
但不是它的内容，因此它将不被注释。
所以在执行模板时，Thymeleaf 会看到:

```html
<span>hello!</span>

  <div th:text="${...}">
    ...
  </div>

<span>goodbye!</span>
```

与解析器级别的注释块一样，该特性与方言无关。


11.4. 合成 `th:block` 标签
------------------------------

在标准方言中，Thymeleaf 唯一的元素处理器(不是属性)是 `th:block`。


`th:block` 仅仅是一个属性容器，它允许模板开发人员指定他们想要的任何属性。
Thymeleaf 将执行这些属性，然后简单地使块(而不是其内容)消失。

因此，它可能是有用的，例如，当创建迭代表时，每个元素需要一个以上的 `<tr>`:

```html
<table>
  <th:block th:each="user : ${users}">
    <tr>
        <td th:text="${user.login}">...</td>
        <td th:text="${user.name}">...</td>
    </tr>
    <tr>
        <td colspan="2" th:text="${user.address}">...</td>
    </tr>
  </th:block>
</table>
```

当与仅原型的注释块结合使用时尤其有用:

```html
<table>
    <!--/*/ <th:block th:each="user : ${users}"> /*/-->
    <tr>
        <td th:text="${user.login}">...</td>
        <td th:text="${user.name}">...</td>
    </tr>
    <tr>
        <td colspan="2" th:text="${user.address}">...</td>
    </tr>
    <!--/*/ </th:block> /*/-->
</table>
```

请注意，这个解决方案是如何允许模板成为有效的 HTML(不需要在 `<table>` 中添加禁止的 `<div>` 块)，
并且在浏览器中作为原型静态打开时仍然可以正常工作!




12 内联
===========



12.1 内联表达式
------------------------

尽管标准方言允许我们使用标记属性做几乎所有的事情，
但是在某些情况下，我们可能更喜欢直接将表达式写入 HTML 文本。
例如，我们更喜欢这样写:

```html
<p>Hello, [[${session.user.name}]]!</p>
```

...而不是:

```html
<p>Hello, <span th:text="${session.user.name}">Sebastian</span>!</p>
```

表达式之间的 `[[...]]` 或 `[(...)]` 在 Thymeleaf 中被认为是**内联表达式**，
在它们内部，我们可以使用任何在 `th:text` 或 `th:utext` 属性中有效的表达式。


注意，当 `[[...]]` 对应于 `th:text` (即结果为 *转译的 HTML*)， 
`[(...)]` 对应于 `th:utext`，不会执行任何 HTML 转译。
所以如果有一个变量，比如 `msg = 'This is <b>great!</b>'`，给定这个片段:

```html
<p>The message is "[(${msg})]"</p>
```


结果将会有那些未被转义的 `<b>` 标签，所以:

```html
<p>The message is "This is <b>great!</b>"</p>
```

反之，转译为：

```html
<p>The message is "[[${msg}]]"</p>
```

结果将是被转译的 HTML:

```html
<p>The message is "This is &lt;b&gt;great!&lt;/b&gt;"</p>
```

注意，默认情况下，在标记中的每个标记的主体中**文本内联是激活的**，而不是标记本身，因此我们不需要做什么来启用它。


### 内联 vs 自然模板

如果您来自其他以这种方式输出文本为标准的模板引擎，
您可能会问:_我们为什么不从一开始就这样做呢？它的代码比所有那些_ `th:text` _属性_ 都要少


请注意，尽管您可能会发现内联非常有趣，但您应该始终记住，
当您静态打开内联表达式时，它们将在 HTML 文件中逐字显示，所以您可能再也不能将它们用作设计原型了!


浏览器如何在不使用内联的情况下静态显示我们的代码片段……

```
Hello, Sebastian!
```

...使用它…

```
Hello, [[${session.user.name}]]!
```

...这在设计有效性方面是显而易见的


### 禁用内联

这个机制可以被禁用，因为在某些情况下，
我们确实需要输出 `[[...]]` 或 `[(...)]` 序列，其内容不作为表达式处理。
为此，我们将使用 `th:inline="none"`:


```html
<p th:inline="none">A double array looks like this: [[1, 2, 3], [4, 5]]!</p>
```

这将生成:

```html
<p>A double array looks like this: [[1, 2, 3], [4, 5]]!</p>
```



12.2 文本内联
------------------

*文本内联*非常类似于我们刚才看到的*表达式内联*功能，但是它实际上增加了更多的功能。
它必须依靠 `th:inline="text"` 显式地启用 `th:inline="text"`。

文本内联不仅允许我们使用我们刚刚看到的相同的*内联表达式*，
而且实际上处理*标记体*就像它们是在 `TEXT` 模板模式中处理的模板一样，
这允许我们执行基于文本的模板逻辑(不仅仅是输出表达式)。

我们将在下一章关于*文本模板模式*中了解更多。


12.3 JavaScript 内联
------------------------

JavaScript 内联允许 JavaScript `<script>` 块更好地集成在以 `HTML` 模板模式处理的模板中。

与*文本内联*一样，这实际上相当于将脚本内容处理为 `JAVASCRIPT` 模板模式下的模板，
因此，*文本模板模式*(参见下一章)的所有功能都将唾手可得。
但是，在本节中，我们将重点讨论如何使用它将 Thymeleaf 表达式的输出添加到 JavaScript 块中。

这个模式必须使用 `th:inline="javascript"`显式启用:

```html
<script th:inline="javascript">
    ...
    var username = [[${session.user.name}]];
    ...
</script>
```

这将生成:

```html
<script th:inline="javascript">
    ...
    var username = "Sebastian \"Fruity\" Applejuice";
    ...
</script>
```

在上面的代码中有两件重要的事情需要注意:

*第一*, JavaScript 内联不仅可以输出所需的文本，还可以用引号和JavaScript 转义它的内容来封装它，
这样表达式结果就可以输出为**格式良好的 JavaScript 字面量**。

*第二*, 这是因为我们将 `${session.user.name}` 表达式输出为**转义的**，
即使用双尖括号表达式: `[[${session.user.name}]]`。如果我们使用*不转译的*，例如:

```html
<script th:inline="javascript">
    ...
    var username = [(${session.user.name})];
    ...
</script>
```

结果会是这样的:

```html
<script th:inline="javascript">
    ...
    var username = Sebastian "Fruity" Applejuice;
    ...
</script>
```

...这是格式错误的 JavaScript 代码。
但是，如果要通过追加内联表达式来构建脚本的某些部分，
输出未转义的内容可能是我们所需要的，所以手边有这个工具很好。


### JavaScript 自然模板

前面提到的 JavaScript 内联机制的*智能*远远不止应用 JavaScript 特有的转义和输出
表达式结果为有效的文字。

例如，我们可以将(被转义的)内联表达式包装在 JavaScript 注释中，比如:

```html
<script th:inline="javascript">
    ...
    var username = /*[[${session.user.name}]]*/ "Gertrud Kiwifruit";
    ...
</script>
```

Thymeleaf 会忽略我们在注释后面和分号之前所写的所有内容*(在本例中是 ` 'Gertrud Kiwifruit'`)，
因此执行该操作的结果与我们没有使用包装注释时的结果完全相同:

```html
<script th:inline="javascript">
    ...
    var username = "Sebastian \"Fruity\" Applejuice";
    ...
</script>
```

但是再仔细看看原始的模板代码:


```html
<script th:inline="javascript">
    ...
    var username = /*[[${session.user.name}]]*/ "Gertrud Kiwifruit";
    ...
</script>
```

注意这是**有效的 JavaScript**代码。当您以静态方式打开模板文件时(无需在服务器上执行)，它将完美地执行。

所以我们这里有一个方法来做 **JavaScript 自然模板**!


### 高级内联计算和 JavaScript 序列化

关于 JavaScript 内联，需要注意的一件重要事情是，这种表达式求值是智能的，并不局限于字符串。
Thymeleaf 会正确地用 JavaScript 语法编写以下对象:

 * Strings
 * Numbers
 * Booleans
 * Arrays
 * Collections
 * Maps
 * Beans (带有 _getter_ 和 _setter_ 方法的对象)

例如，如果我们有以下代码:

```html
<script th:inline="javascript">
    ...
    var user = /*[[${session.user}]]*/ null;
    ...
</script>
```

`${session.user}` 表达式将求值为 `User` 对象，Thymeleaf 将其正确转换为 Javascript 语法:

```html
<script th:inline="javascript">
    ...
    var user = {"age":null,"firstName":"John","lastName":"Apricot",
                "name":"John Apricot","nationality":"Antarctica"};
    ...
</script>
```

JavaScript 序列化是通过实现 `org.thymeleaf.standard.serializer.IStandardJavaScriptSerializer` 接口来实现的,
此接口可以在模板引擎中使用的 `StandardDialect` 实例中进行配置。

这个 JS 序列化机制的默认实现将在类路径中查找 [Jackson 库](https://github.com/FasterXML/jackson)，如果存在，将使用它。
如果没有，它将应用一个内置的序列化机制来满足大多数场景的需求，并产生类似的结果(但是不那么灵活)。


12.4 CSS 内联
-----------------

Thymeleaf 还允许在 CSS `<style>` 标签中使用内联，例如:

```html
<style th:inline="css">
  ...
</style>
```

例如，假设我们有两个变量被设置为两个不同的 `String` 值:


```
classname = 'main elems'
align = 'center'
```

我们可以像这样使用它们:

```html
<style th:inline="css">
    .[[${classname}]] {
      text-align: [[${align}]];
    }
</style>
```

结果会是:

```html
<style th:inline="css">
    .main\ elems {
      text-align: center;
    }
</style>
```

注意 CSS 内联也有一些*智能*，就像 JavaScript 一样。
具体来说，通过 `[[${classname}]]` 等*转义*表达式输出的表达式将转义为 **CSS 标识符**。
这就是为什么我们的 `classname = 'main elems'` 在上面的代码片段中变成了 `main\ elems`。


### 高级功能：CSS 自然模板等。

CSS 内联还允许我们的 `<style>` 标签静态和动态地工作，
即通过在注释中包装内联表达式，作为**CSS 自然模板**。看到的:

```html
<style th:inline="css">
    .main\ elems {
      text-align: /*[[${align}]]*/ left;
    }
</style>
```




13 文本模板模式
=========================



13.1 文本语法
-------------------

三种 Thymeleaf *模板模式* 被认为是**文本**：`TEXT`，`JAVASCRIPT` 和 `CSS`。这将它们与标记模板模式区分开来：`HTML` 和 `XML`。

文本模板模式和标记模式的关键区别在于，在文本模板中没有以属性形式插入逻辑的标记，因此我们必须依赖其他机制。

第一个，也是最基本的机制是**内联**，我们在上一章中已经详细介绍过了。
内联语法是在文本模板模式下输出表达式结果最简单的方法，因此这是一个非常有效的文本电子邮件模板。

```
  Dear [(${name})],

  Please find attached the results of the report you requested
  with name "[(${report.name})]".

  Sincerely,
    The Reporter.
```

即使没有标记，上面的示例也是完整有效的 Thymeleaf 模板，可以在 `TEXT` 模板模式下执行。

但是为了包含比简单的*输出表达式*更复杂的逻辑，我们需要一个新的非基于标记的语法:

```
[# th:each="item : ${items}"]
  - [(${item})]
[/]
```

这实际上是更冗长的“精简”版本:

```
[#th:block th:each="item : ${items}"]
  - [#th:block th:utext="${item}" /]
[/th:block]
```

注意，这个新语法是如何基于声明为 `[#element ...]`，而不是 `<element ...>`。
元素是打开如 `[#element ...]`，关闭如 `[/element]`。
独立的标签可以通过使用 `/` 最小化打开的元素来声明，其方式几乎等同于 XML 标签: `[#element ... /]`。

标准方言只包含这些元素中的一个的处理器：已经知道的 `th:block`，尽管我们可以在我们的方言中扩展它，
并以通常的方式创建新元素。此外，`th:block` 元素(`[#th:block ...] ... [/th:block]`)
可以缩写为空字符串(`[# ...] ... [/]`)，所以上面的 block 实际上等于:

```
[# th:each="item : ${items}"]
  - [# th:utext="${item}" /]
[/]
```

并且给定 `[# th:utext="${item}" /]` 等价于一个*内联的未转义表达式*，
我们可以使用它来获得更少的代码。因此，我们得到了上面看到的第一个代码片段:

```
[# th:each="item : ${items}"]
  - [(${item})]
[/]
```

注意，*文本语法需要完整的元素平衡(没有非封闭标记)和引号属性*——它更像 XML 风格，而不是 HTML 风格。

让我们看一个更完整的 `TEXT` 模板的例子，一个*纯文本*电子邮件模板:

```
Dear [(${customer.name})],

This is the list of our products:

[# th:each="prod : ${products}"]
   - [(${prod.name})]. Price: [(${prod.price})] EUR/kg
[/]

Thanks,
  The Thymeleaf Shop
```

执行之后，结果可能是这样的:

```
Dear Mary Ann Blueberry,

This is the list of our products:

   - Apricots. Price: 1.12 EUR/kg
   - Bananas. Price: 1.78 EUR/kg
   - Apples. Price: 0.85 EUR/kg
   - Watermelon. Price: 1.91 EUR/kg

Thanks,
  The Thymeleaf Shop
```

`JAVASCRIPT` 模板模式下的另一个例子是 `greeter.js`。
我们将 js 文件作为文本模板处理，并从 HTML 页面调用结果。
注意，这*不是* HTML 模板中的 `<script>`  块，而是一个 `.js` 文件作为模板单独处理:

```javascript
var greeter = function() {

    var username = [[${session.user.name}]];

    [# th:each="salut : ${salutations}"]    
      alert([[${salut}]] + " " + username);
    [/]

};
```

执行之后，结果可能是这样的:

```javascript
var greeter = function() {

    var username = "Bertrand \"Crunchy\" Pear";

      alert("Hello" + " " + username);
      alert("Ol\u00E1" + " " + username);
      alert("Hola" + " " + username);

};
```


### 转译的元素属性

为了避免与模板中可能在其他模式(例如 `text` 模式内联在 `HTML` 模板中)，
Thymeleaf 3.0 允许转义其*文本语法*中的元素属性。因此:

 * `TEXT` 模板模式中的属性将是*未转译的 HTML*。
 * `JAVASCRIPT` 模板模式中的属性将是*未转译的 JAVASCRIPT*。
 * `CSS` 模板模式中的属性将是*未转译的 CSS*。

所以这在 `TEXT` 模式模板中是完全可以的(注意 `&gt;`):
```
  [# th:if="${120&lt;user.age}"]
     Congratulations!
  [/]
```

当然，在*真正的文本*模板中，`&lt;` 是没有意义的，
但这是一个好主意如果我们处理 HTML 模板和一个`th:inline="text"` 块包含上面的代码,
我们要确保我们的浏览器不需要 `<user.age` 的开放标签的名字当静态打开文件作为一个原型。



13.2 可扩展性
------------------

One of the advantages of this syntax is that it is just as extensible as the
*markup* one. Developers can still define their own dialects with custom
elements and attributes, apply a prefix to them (optionally), and then use them
in textual template modes:

```
  [#myorg:dosomething myorg:importantattr="211"]some text[/myorg:dosomething]
```



13.3 Textual prototype-only comment blocks: adding code
-------------------------------------------------------

The `JAVASCRIPT` and `CSS` template modes (not available for `TEXT`) allow
including code between a special comment syntax `/*[+...+]*/` so that Thymeleaf
will automatically uncomment such code when processing the template:

```javascript
var x = 23;

/*[+

var msg  = "This is a working application";

+]*/

var f = function() {
    ...
```

Will be executed as:

```javascript
var x = 23;

var msg  = "This is a working application";

var f = function() {
...
```

You can include expressions inside these comments, and they will be evaluated:

```javascript
var x = 23;

/*[+

var msg  = "Hello, " + [[${session.user.name}]];

+]*/

var f = function() {
...
```



13.4 Textual parser-level comment blocks: removing code
-------------------------------------------------------

In a way similar to that of prototype-only comment blocks, all the three textual
template modes (`TEXT`, `JAVASCRIPT` and `CSS`) make it possible to instruct
Thymeleaf to remove code between special `/*[- */` and `/* -]*/` marks, like
this:

```javascript
var x = 23;

/*[- */

var msg  = "This is shown only when executed statically!";

/* -]*/

var f = function() {
...
```

Or this, in `TEXT` mode:

```
...
/*[- Note the user is obtained from the session, which must exist -]*/
Welcome [(${session.user.name})]!
...
```



13.5 Natural JavaScript and CSS templates
-----------------------------------------

As seen in the previous chapter, JavaScript and CSS inlining offer the
possibility to include inlined expressions inside JavaScript/CSS comments, like:

```javascript
...
var username = /*[[${session.user.name}]]*/ "Sebastian Lychee";
...
```

...which is valid JavaScript, and once executed could look like:

```html
...
var username = "John Apricot";
...
```

This same *trick* of enclosing inlined expressions inside comments can in fact be
used for the entire textual mode syntax:

```
  /*[# th:if="${user.admin}"]*/
     alert('Welcome admin');
  /*[/]*/
```

That alert in the code above will be shown when the template is open statically
-- because it is 100% valid JavaScript --, and also when the template is run if
the user is an admin. It is equivalent to:

```
  [# th:if="${user.admin}"]
     alert('Welcome admin');
  [/]
```

...which is actually the code to which the initial version is converted during
template parsing.

Note however that wrapping elements in comments does not clean the lines they
live in (to the right until a `;` is found) as inlined output expressions do.
That behaviour is reserved for inlined output expressions only.

So Thymeleaf 3.0 allows the development of **complex JavaScript scripts and CSS
style sheets in the form of natural templates**, valid both as a *prototype* and
as a *working template*.




14 再给我们的杂货店添几个页面
==================================

Now we know a lot about using Thymeleaf, we can add some new pages to our
website for order management.

Note that we will focus on HTML code, but you can have a look at the bundled
source code if you want to see the corresponding controllers.



14.1 Order List
---------------

Let's start by creating an order list page, `/WEB-INF/templates/order/list.html`:

```html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>

    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="../../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
  </head>

  <body>

    <h1>Order list</h1>

    <table>
      <tr>
        <th>DATE</th>
        <th>CUSTOMER</th>
        <th>TOTAL</th>
        <th></th>
      </tr>
      <tr th:each="o : ${orders}" th:class="${oStat.odd}? 'odd'">
        <td th:text="${#calendars.format(o.date,'dd/MMM/yyyy')}">13 jan 2011</td>
        <td th:text="${o.customer.name}">Frederic Tomato</td>
        <td th:text="${#aggregates.sum(o.orderLines.{purchasePrice * amount})}">23.32</td>
        <td>
          <a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>
        </td>
      </tr>
    </table>

    <p>
      <a href="../home.html" th:href="@{/}">Return to home</a>
    </p>

  </body>

</html>
```

There's nothing here that should surprise us, except for this little bit of OGNL
magic:

```html
<td th:text="${#aggregates.sum(o.orderLines.{purchasePrice * amount})}">23.32</td>
```

What that does is, for each order line (`OrderLine` object) in the order,
multiply its `purchasePrice` and `amount` properties (by calling the
corresponding `getPurchasePrice()` and `getAmount()` methods) and return the
result into a list of numbers, later aggregated by the `#aggregates.sum(...)`
function in order to obtain the order total price.

You've got to love the power of OGNL.



14.2 Order Details
------------------

Now for the order details page, in which we will make a heavy use of asterisk
syntax:

```html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="../../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
  </head>

  <body th:object="${order}">

    <h1>Order details</h1>

    <div>
      <p><b>Code:</b> <span th:text="*{id}">99</span></p>
      <p>
        <b>Date:</b>
        <span th:text="*{#calendars.format(date,'dd MMM yyyy')}">13 jan 2011</span>
      </p>
    </div>

    <h2>Customer</h2>

    <div th:object="*{customer}">
      <p><b>Name:</b> <span th:text="*{name}">Frederic Tomato</span></p>
      <p>
        <b>Since:</b>
        <span th:text="*{#calendars.format(customerSince,'dd MMM yyyy')}">1 jan 2011</span>
      </p>
    </div>

    <h2>Products</h2>

    <table>
      <tr>
        <th>PRODUCT</th>
        <th>AMOUNT</th>
        <th>PURCHASE PRICE</th>
      </tr>
      <tr th:each="ol,row : *{orderLines}" th:class="${row.odd}? 'odd'">
        <td th:text="${ol.product.name}">Strawberries</td>
        <td th:text="${ol.amount}" class="number">3</td>
        <td th:text="${ol.purchasePrice}" class="number">23.32</td>
      </tr>
    </table>

    <div>
      <b>TOTAL:</b>
      <span th:text="*{#aggregates.sum(orderLines.{purchasePrice * amount})}">35.23</span>
    </div>

    <p>
      <a href="list.html" th:href="@{/order/list}">Return to order list</a>
    </p>

  </body>

</html>
```

Not much really new here, except for this nested object selection:

```html
<body th:object="${order}">

  ...

  <div th:object="*{customer}">
    <p><b>Name:</b> <span th:text="*{name}">Frederic Tomato</span></p>
    ...
  </div>

  ...
</body>
```

...which makes that `*{name}` equivalent to:


```html
<p><b>Name:</b> <span th:text="${order.customer.name}">Frederic Tomato</span></p>
```




15 关于配置的更多信息
========================



15.1 Template Resolvers
-----------------------

For our Good Thymes Virtual Grocery, we chose an `ITemplateResolver`
implementation called `ServletContextTemplateResolver` that allowed us to obtain
templates as resources from the Servlet Context.

Besides giving us the ability to create our own template resolver by
implementing `ITemplateResolver,` Thymeleaf includes four implementations out of
the box:

 * `org.thymeleaf.templateresolver.ClassLoaderTemplateResolver`, which resolves
   templates as classloader resources, like:

    ```java
    return Thread.currentThread().getContextClassLoader().getResourceAsStream(template);
    ```

 * `org.thymeleaf.templateresolver.FileTemplateResolver`, which resolves
   templates as files from the file system, like:

    ```java
    return new FileInputStream(new File(template));
    ```

 * `org.thymeleaf.templateresolver.UrlTemplateResolver`, which resolves
   templates as URLs (even non-local ones), like:

    ```java
    return (new URL(template)).openStream();
    ```

 * `org.thymeleaf.templateresolver.StringTemplateResolver`, which resolves
   templates directly as the `String` being specified as `template` (or
   *template name*, which in this case is obviously much more than a mere name):

    ```java
    return new StringReader(templateName);
    ```

All of the pre-bundled implementations of `ITemplateResolver` allow the same set
of configuration parameters, which include:

 * Prefix and suffix (as already seen):

    ```java
    templateResolver.setPrefix("/WEB-INF/templates/");
    templateResolver.setSuffix(".html");
    ```

 * Template aliases that allow the use of template names that do not directly
   correspond to file names. If both suffix/prefix and alias exist, alias will
   be applied before prefix/suffix:

    ```java
    templateResolver.addTemplateAlias("adminHome","profiles/admin/home");
    templateResolver.setTemplateAliases(aliasesMap);
    ```

 * Encoding to be applied when reading templates:

    ```java
    templateResolver.setEncoding("UTF-8");
    ```

 * Template mode to be used:

    ```java
    // Default is HTML
    templateResolver.setTemplateMode("XML");
    ```

 * Default mode for template cache, and patterns for defining whether specific
   templates are cacheable or not:

    ```java
    // Default is true
    templateResolver.setCacheable(false);
    templateResolver.getCacheablePatternSpec().addPattern("/users/*");
    ```

 * TTL in milliseconds for parsed template cache entries originated in this
   template resolver. If not set, the only way to remove an entry from the cache
   will be to exceed the cache max size (oldest entry will be removed).

    ```java
    // Default is no TTL (only cache size exceeded would remove entries)
    templateResolver.setCacheTTLMs(60000L);
    ```

> The Thymeleaf + Spring integration packages offer a `SpringResourceTemplateResolver`
> implementation which uses all the Spring infrastructure for accessing and
> reading resources in applications, and which is the recommended implementation
> in Spring-enabled applications.


### 链式模板解析器


Also, a Template Engine can specify several template resolvers, in which case an
order can be established between them for template resolution so that, if the
first one is not able to resolve the template, the second one is asked, and so
on:

```java
ClassLoaderTemplateResolver classLoaderTemplateResolver = new ClassLoaderTemplateResolver();
classLoaderTemplateResolver.setOrder(Integer.valueOf(1));

ServletContextTemplateResolver servletContextTemplateResolver =
        new ServletContextTemplateResolver(servletContext);
servletContextTemplateResolver.setOrder(Integer.valueOf(2));

templateEngine.addTemplateResolver(classLoaderTemplateResolver);
templateEngine.addTemplateResolver(servletContextTemplateResolver);
```

When several template resolvers are applied, it is recommended to specify
patterns for each template resolver so that Thymeleaf can quickly discard those
template resolvers that are not meant to resolve the template, enhancing
performance. Doing this is not a requirement, but a recommendation:

```java
ClassLoaderTemplateResolver classLoaderTemplateResolver = new ClassLoaderTemplateResolver();
classLoaderTemplateResolver.setOrder(Integer.valueOf(1));
// This classloader will not be even asked for any templates not matching these patterns
classLoaderTemplateResolver.getResolvablePatternSpec().addPattern("/layout/*.html");
classLoaderTemplateResolver.getResolvablePatternSpec().addPattern("/menu/*.html");

ServletContextTemplateResolver servletContextTemplateResolver =
        new ServletContextTemplateResolver(servletContext);
servletContextTemplateResolver.setOrder(Integer.valueOf(2));
```

If these *resolvable patterns* are not specified, we will be relying on the
specific capabilities of each of the `ITemplateResolver` implementations we are
using. Note that not all implementations might be able to determine the
existence of a template before resolving, and thus could always consider a
template as *resolvable* and break the resolution chain (not allowing other
resolvers to check for the same template), but then be unable to read the real
resource.

All the `ITemplateResolver` implementations that are included with core
Thymeleaf include a mechanism that will allow us to make the resolvers *really
check* if a resource exists before considering it *resolvable*. It is the
`checkExistence` flag, which works like:

```java
ClassLoaderTemplateResolver classLoaderTemplateResolver = new ClassLoaderTemplateResolver();
classLoaderTemplateResolver.setOrder(Integer.valueOf(1));
classLoaderTempalteResolver.setCheckExistence(true);
```

This `checkExistence` flag forces the resolver perform a *real check* for
resource existence during the resolution phase (and let the following resolver
in the chain be called if existence check returns false). While this might sound
good in every case, in most cases this will mean a double access to the resource
itself (once for checking existence, another time for reading it), and could be
a performance issue in some scenarios, e.g. remote URL-based template resources
-- a potential performance issue that might anyway get largely mitigated by the
use of the template cache (in which case templates will only be *resolved* the
first time they are accessed).



15.2 消息解析器
----------------------

We did not explicitly specify a Message Resolver implementation for our Grocery
application, and as it was explained before, this meant that the implementation
being used was an `org.thymeleaf.messageresolver.StandardMessageResolver` object.

`StandardMessageResolver` is the standard implementation of the `IMessageResolver`
interface, but we could create our own if we wanted, adapted to the specific
needs of our application.

> The Thymeleaf + Spring integration packages offer by default an `IMessageResolver`
> implementation which uses the standard Spring way of retrieving externalized
> messages, by using `MessageSource` beans declared at the Spring Application
> Context.


### Standard Message Resolver

So how does `StandardMessageResolver` look for the messages requested at a
specific template?

If the template name is `home` and it is located in `/WEB-INF/templates/home.html`,
and the requested locale is `gl_ES` then this resolver will look for messages in
the following files, in this order:

 * `/WEB-INF/templates/home_gl_ES.properties`
 * `/WEB-INF/templates/home_gl.properties`
 * `/WEB-INF/templates/home.properties`

Refer to the JavaDoc documentation of the `StandardMessageResolver` class for
more detail on how the complete message resolution mechanism works.


### 配置消息解析器

What if we wanted to add a message resolver (or more) to the Template Engine?
Easy:

```java
// For setting only one
templateEngine.setMessageResolver(messageResolver);

// For setting more than one
templateEngine.addMessageResolver(messageResolver);
```

And why would we want to have more than one message resolver? For the same
reason as template resolvers: message resolvers are ordered and if the first one
cannot resolve a specific message, the second one will be asked, then the third,
etc.




15.3 转换 Services
------------------------

The *conversion service* that enables us to perform data conversion and
formatting operations by means of the *double-brace* syntax (`${{...}}`) is
actually a feature of the Standard Dialect, not of the Thymeleaf Template Engine
itself.

As such, the way to configure it is by setting our custom implementation of the
`IStandardConversionService` interface directly into the instance of `StandardDialect`
that is being configured into the template engine. Like:

```java
IStandardConversionService customConversionService = ...

StandardDialect dialect = new StandardDialect();
dialect.setConversionService(customConversionService);

templateEngine.setDialect(dialect);
```

> Note that the thymeleaf-spring3 and thymeleaf-spring4 packages contain the
> `SpringStandardDialect`, and this dialect already comes pre-configured with an
> implementation of `IStandardConversionService` that integrates Spring's own
> *Conversion Service* infrastructure into Thymeleaf.



15.4 日志记录
------------

Thymeleaf pays quite a lot of attention to logging, and always tries to offer
the maximum amount of useful information through its logging interface.

The logging library used is `slf4j,` which in fact acts as a bridge to whichever
logging implementation we might want to use in our application (for example, `log4j`).

Thymeleaf classes will log `TRACE`, `DEBUG` and `INFO`-level information,
depending on the level of detail we desire, and besides general logging it will
use three special loggers associated with the TemplateEngine class which we can
configure separately for different purposes:

 * `org.thymeleaf.TemplateEngine.CONFIG` will output detailed configuration of
   the library during initialization.
 * `org.thymeleaf.TemplateEngine.TIMER` will output information about the amount
   of time taken to process each template (useful for benchmarking!)
 * `org.thymeleaf.TemplateEngine.cache` is the prefix for a set of loggers that
   output specific information about the caches. Although the names of the cache
   loggers are configurable by the user and thus could change, by default they
   are:
    * `org.thymeleaf.TemplateEngine.cache.TEMPLATE_CACHE`
    * `org.thymeleaf.TemplateEngine.cache.EXPRESSION_CACHE`

An example configuration for Thymeleaf's logging infrastructure, using `log4j`,
could be:

```
log4j.logger.org.thymeleaf=DEBUG
log4j.logger.org.thymeleaf.TemplateEngine.CONFIG=TRACE
log4j.logger.org.thymeleaf.TemplateEngine.TIMER=TRACE
log4j.logger.org.thymeleaf.TemplateEngine.cache.TEMPLATE_CACHE=TRACE
```




16 模板缓存
=================

Thymeleaf works thanks to a set of parsers -- for markup and text -- that parse
templates into sequences of events (open tag, text, close tag, comment, etc.)
and a series of processors -- one for each type of behaviour that needs to be
applied -- that modify the template parsed event sequence in order to create the
results we expect by combining the original template with our data.

It also includes -- by default -- a cache that stores parsed templates; the
sequence of events resulting from reading and parsing template files before
processing them. This is especially useful when working in a web application,
and builds on the following concepts:

 * Input/Output is almost always the slowest part of any application. In-memory
   processing is extremely quick by comparison.
 * Cloning an existing in-memory event sequence is always much quicker than
   reading a template file, parsing it and creating a new event sequence for it.
 * Web applications usually have only a few dozen templates.
 * Template files are small-to-medium size, and they are not modified while the
   application is running.

This all leads to the idea that caching the most used templates in a web
application is feasible without wasting large amounts of memory, and also that
it will save a lot of time that would be spent on input/output operations on a
small set of files that, in fact, never change.

And how can we take control of this cache? First, we've learned before that we
can enable or disable it at the Template Resolver, even acting only on specific
templates:

```java
// Default is true
templateResolver.setCacheable(false);
templateResolver.getCacheablePatternSpec().addPattern("/users/*");
```

Also, we could modify its configuration by establishing our own _Cache Manager_
object, which could be an instance of the default `StandardCacheManager`
implementation:

```java
// Default is 200
StandardCacheManager cacheManager = new StandardCacheManager();
cacheManager.setTemplateCacheMaxSize(100);
...
templateEngine.setCacheManager(cacheManager);
```

Refer to the javadoc API of `org.thymeleaf.cache.StandardCacheManager` for more
info on configuring the caches.

Entries can be manually removed from the template cache:

```java
// Clear the cache completely
templateEngine.clearTemplateCache();

// Clear a specific template from the cache
templateEngine.clearTemplateCacheFor("/users/userList");
```




17 解耦的模板逻辑
===========================

17.1 Decoupled logic: The concept
---------------------------------

So far we have worked for our Grocery Store with templates done the *usual way*,
with logic being inserted into our templates in the form of attributes.

But Thymeleaf also allows us to completely *decouple* the template markup from
its logic, allowing the creation of **completely logic-less markup templates**
in the `HTML` and `XML` template modes.

The main idea is that template logic will be defined in a separate *logic file*
(more exactly a *logic resource*, as it doesn't need to be a *file*). By default,
that logic resource will  be an additional file living in the same place (e.g.
folder) as the template file, with the same name but with `.th.xml` extension:

```
/templates
+->/home.html
+->/home.th.xml
```

So the `home.html` file can be completely logic-less. It might look like this:

```html
<!DOCTYPE html>
<html>
  <body>
    <table id="usersTable">
      <tr>
        <td class="username">Jeremy Grapefruit</td>
        <td class="usertype">Normal User</td>
      </tr>
      <tr>
        <td class="username">Alice Watermelon</td>
        <td class="usertype">Administrator</td>
      </tr>
    </table>
  </body>
</html>
```

Absolutely no Thymeleaf code there. This is a template file that a designer with
no Thymeleaf or templating knowledge could have created, edited and/or
understood. Or a fragment of HTML provided by some external system with no
Thymeleaf hooks at all.

Let's now turn that `home.html` template into a Thymeleaf template by creating
our additional `home.th.xml` file like this:

```xml
<?xml version="1.0"?>
<thlogic>
  <attr sel="#usersTable" th:remove="all-but-first">
    <attr sel="/tr[0]" th:each="user : ${users}">
      <attr sel="td.username" th:text="${user.name}" />
      <attr sel="td.usertype" th:text="#{|user.type.${user.type}|}" />
    </attr>
  </attr>
</thlogic>
```

Here we can see a lot of `<attr>` tags inside a `thlogic` block. Those `<attr>`
tags perform *attribute injection* on nodes of the original template selected by
means of their `sel` attributes, which contain Thymeleaf *markup selectors*
(actually *AttoParser markup selectors*).

Also note that `<attr>` tags can be nested so that their selectors are *appended*.
That `sel="/tr[0]"` above, for example, will be processed as `sel="#usersTable/tr[0]"`.
And the selector for the user name `<td>` will be processed as `sel="#usersTable/tr[0]//td.username"`.

So once merged, both files seen above will be the same as:

```html
<!DOCTYPE html>
<html>
  <body>
    <table id="usersTable" th:remove="all-but-first">
      <tr th:each="user : ${users}">
        <td class="username" th:text="${user.name}">Jeremy Grapefruit</td>
        <td class="usertype" th:text="#{|user.type.${user.type}|}">Normal User</td>
      </tr>
      <tr>
        <td class="username">Alice Watermelon</td>
        <td class="usertype">Administrator</td>
      </tr>
    </table>
  </body>
</html>
```

This looks more familiar, and is indeed less *verbose* than creating two
separate files. But the advantage of *decoupled templates* is that we can
give for our templates total independence from Thymeleaf, and therefore better
maintainability from the design standpoint.

Of course some *contracts* between designers or developers will still be needed
-- e.g. the fact that the users `<table>` will need an `id="usersTable"` --, but
in many scenarios a pure-HTML template will be a much better communication
artifact between design and development teams.



17.2 配置解耦的模板
------------------------------------

### 启用解耦的模板

Decoupled logic will not be expected for every template by default. Instead, the
configured template resolvers (implementations of `ITemplateResolver`) will need
to specifically mark the templates they resolve as *using decoupled logic*.

Except for `StringTemplateResolver` (which does not allow decoupled logic), all
other out-of-the-box implementations of `ITemplateResolver` will provide a flag
called `useDecoupledLogic` that will mark all templates resolved by that
resolver as potentially having all or part of its logic living in a separate
resource:

```java
final ServletContextTemplateResolver templateResolver =
        new ServletContextTemplateResolver(servletContext);
...
templateResolver.setUseDecoupledLogic(true);
```


### 混合耦合和解耦逻辑

Decoupled template logic, when enabled, is not a requirement. When enabled, it
means that the engine will *look for* a resource containing decoupled logic,
parsing and merging it with the original template if it exists. No error will be
thrown if the decoupled logic resource does not exist.

Also, in the same template we can mix both *coupled* and *decoupled* logic, for
example by adding some Thymeleaf attributes at the original template file but
leaving others for the separate decoupled logic file. The most common case for
this is using the new (in v3.0) `th:ref` attribute.



17.3 th:ref 属性
---------------------------

`th:ref` 只是一个标记属性。从处理的角度来看，它什么也不做，只是在处理模板时消失，
但它的有用之处在于它充当了一个*标记引用*，也就是说，
它可以像一个*标记名称*或一个*片段*(`th:fragment`)一样，通过名称从*标记选择器*解析。

如果我们有一个选择器，比如

```xml
  <attr sel="whatever" .../>
```

这将匹配:

 * 任意 `<whatever>` 标签。
 * 任何带有 `th:fragment="whatever"` 属性的标签。
 * 任何带有 `th:ref="whatever"` 属性的标签。

例如，对于使用纯 HTML `id` 属性的情况，`th:ref` 的优点是什么？
仅仅是因为我们可能不希望向标记中添加这么多的 `id` 和 `class` 属性来充当*逻辑锚*，这可能最终会*污染*我们的输出。

同样，`th:ref` 的缺点是什么？很明显，我们需要在模板中添加一些 Thymeleaf 逻辑(*“逻辑”*)。

注意，`th:ref` 属性**的适用性不仅适用于分离的逻辑模板文件**:它在其他类型的场景中也适用，比如片段表达式(`~{...}`)。



17.4 解耦模板的性能影响
----------------------------------------------

The impact is extremely small. When a resolved template is marked to use
decoupled logic and it is not cached, the template logic resource will be
resolved first, parsed and processed into a secuence of instructions in-memory:
basically a list of attributes to be injected to each markup selector.

But this is the only *additional step* required because, after this, the real
template will be parsed, and while it is parsed these attributes will be
injected *on-the-fly* by the parser itself, thanks to the advanced capabilities
for node selection in AttoParser. So parsed nodes will come out of the parser as
if they had their injected attributes written in the original template file.

The biggest advantage of this? When a template is configured to be cached, it
will be cached already containing the injected attributes. So the overhead of
using *decoupled templates* for cacheable templates, once they are cached,
will be absolutely *zero*.



17.5 解耦逻辑的解析
----------------------------------

The way Thymeleaf resolves the decoupled logic resources corresponding to each
template is configurable by the user. It is determined by an extension point,
the `org.thymeleaf.templateparser.markup.decoupled.IDecoupledTemplateLogicResolver`,
for which a *default implementation* is provided: `StandardDecoupledTemplateLogicResolver`.

What does this standard implementation do?

 * First, it applies a `prefix` and a `suffix` to the *base name* of the
   template resource (obtained by means of its `ITemplateResource#getBaseName()`
   method). Both prefix and suffix can be configured and, by default, the prefix
   will be empty and the suffix will be `.th.xml`.
 * Second, it asks the template resource to resolve a *relative resource* with
   the computed name by means of its `ITemplateResource#relative(String relativeLocation)`
   method.

The specific implementation of `IDecoupledTemplateLogicResolver` to be used can
be configured at the `TemplateEngine` easily:

```java
final StandardDecoupledTemplateLogicResolver decoupledresolver =
        new StandardDecoupledTemplateLogicResolver();
decoupledResolver.setPrefix("../viewlogic/");
...
templateEngine.setDecoupledTemplateLogicResolver(decoupledResolver);
```




18 附录 A: 表达式基本对象
=======================================

Some objects and variable maps are always available to be invoked. Let's see
them:


### Base objects

 * **\#ctx** : the context object. An implementation of `org.thymeleaf.context.IContext`
   or `org.thymeleaf.context.IWebContext` depending on our environment
   (standalone or web).

   Note `#vars` and `#root` are synomyns for the same object, but using `#ctx`
   is recommended.

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.context.IContext
 * ======================================================================
 */

${#ctx.locale}
${#ctx.variableNames}

/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.context.IWebContext
 * ======================================================================
 */

${#ctx.request}
${#ctx.response}
${#ctx.session}
${#ctx.servletContext}
```

 * **\#locale** : direct access to the `java.util.Locale` associated with
   current request.

```java
${#locale}
```


### Web context namespaces for request/session attributes, etc.

When using Thymeleaf in a web environment, we can use a series of shortcuts for
accessing request parameters, session attributes and application attributes:

> Note these are not *context objects*, but maps added to the context as
> variables, so we access them without `#`. In some way, they act as *namespaces*.

 * **param** : for retrieving request parameters. `${param.foo}` is a `String[]`
   with the values of the `foo` request parameter, so `${param.foo[0]}` will
   normally be used for getting the first value.

```java
/*
 * ============================================================================
 * See javadoc API for class org.thymeleaf.context.WebRequestParamsVariablesMap
 * ============================================================================
 */

${param.foo}              // Retrieves a String[] with the values of request parameter 'foo'
${param.size()}
${param.isEmpty()}
${param.containsKey('foo')}
...
```

 * **session** : for retrieving session attributes.

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.context.WebSessionVariablesMap
 * ======================================================================
 */

${session.foo}                 // Retrieves the session atttribute 'foo'
${session.size()}
${session.isEmpty()}
${session.containsKey('foo')}
...
```

 * **application** : for retrieving application/servlet context attributes.

```java
/*
 * =============================================================================
 * See javadoc API for class org.thymeleaf.context.WebServletContextVariablesMap
 * =============================================================================
 */

${application.foo}              // Retrieves the ServletContext atttribute 'foo'
${application.size()}
${application.isEmpty()}
${application.containsKey('foo')}
...
```

Note there is **no need to specify a namespace for accessing request attributes**
(as opposed to *request parameters*) because all request attributes are
automatically added to the context as variables in the context root:

```java
${myRequestAttribute}
```


### Web context objects

Inside a web environment there is also direct access to the following objects
(note these are objects, not maps/namespaces):

 * **\#request** : direct access to the `javax.servlet.http.HttpServletRequest`
   object associated with the current request.

```java
${#request.getAttribute('foo')}
${#request.getParameter('foo')}
${#request.getContextPath()}
${#request.getRequestName()}
...
```

 * **\#session** : direct access to the `javax.servlet.http.HttpSession` object
   associated with the current request.

```java
${#session.getAttribute('foo')}
${#session.id}
${#session.lastAccessedTime}
...
```

 * **\#servletContext** : direct access to the `javax.servlet.ServletContext`
   object associated with the current request.

```java
${#servletContext.getAttribute('foo')}
${#servletContext.contextPath}
...
```




19 附录 B: 表达式工具对象
=========================================


### Execution Info

 * **\#execInfo** : 表达式对象提供有关在 Thymeleaf 标准表达式中处理的模板的有用信息。

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.ExecutionInfo
 * ======================================================================
 */

/*
 * Return the name and mode of the 'leaf' template. This means the template
 * from where the events being processed were parsed. So if this piece of
 * code is not in the root template "A" but on a fragment being inserted
 * into "A" from another template called "B", this will return "B" as a
 * name, and B's mode as template mode.
 */
${#execInfo.templateName}
${#execInfo.templateMode}

/*
 * Return the name and mode of the 'root' template. This means the template
 * that the template engine was originally asked to process. So if this
 * piece of code is not in the root template "A" but on a fragment being
 * inserted into "A" from another template called "B", this will still
 * return "A" and A's template mode.
 */
${#execInfo.processedTemplateName}
${#execInfo.processedTemplateMode}

/*
 * Return the stacks (actually, List<String> or List<TemplateMode>) of
 * templates being processed. The first element will be the
 * 'processedTemplate' (the root one), the last one will be the 'leaf'
 * template, and in the middle all the fragments inserted in nested
 * manner to reach the leaf from the root will appear.
 */
${#execInfo.templateNames}
${#execInfo.templateModes}

/*
 * Return the stack of templates being processed similarly (and in the
 * same order) to 'templateNames' and 'templateModes', but returning
 * a List<TemplateData> with the full template metadata.
 */
${#execInfo.templateStack}
```


### Messages

 * **\#messages** : 在变量表达式中获取外部化消息的实用方法，与使用 `#{...}` 的语法相同。

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Messages
 * ======================================================================
 */

/*
 * Obtain externalized messages. Can receive a single key, a key plus arguments,
 * or an array/list/set of keys (in which case it will return an array/list/set of
 * externalized messages).
 * If a message is not found, a default message (like '??msgKey??') is returned.
 */
${#messages.msg('msgKey')}
${#messages.msg('msgKey', param1)}
${#messages.msg('msgKey', param1, param2)}
${#messages.msg('msgKey', param1, param2, param3)}
${#messages.msgWithParams('msgKey', new Object[] {param1, param2, param3, param4})}
${#messages.arrayMsg(messageKeyArray)}
${#messages.listMsg(messageKeyList)}
${#messages.setMsg(messageKeySet)}

/*
 * Obtain externalized messages or null. Null is returned instead of a default
 * message if a message for the specified key is not found.
 */
${#messages.msgOrNull('msgKey')}
${#messages.msgOrNull('msgKey', param1)}
${#messages.msgOrNull('msgKey', param1, param2)}
${#messages.msgOrNull('msgKey', param1, param2, param3)}
${#messages.msgOrNullWithParams('msgKey', new Object[] {param1, param2, param3, param4})}
${#messages.arrayMsgOrNull(messageKeyArray)}
${#messages.listMsgOrNull(messageKeyList)}
${#messages.setMsgOrNull(messageKeySet)}
```


### URIs/URLs

 * **\#uris** : utility object for performing URI/URL operations (esp.
   escaping/unescaping) inside Thymeleaf Standard Expressions.

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Uris
 * ======================================================================
 */

/*
 * Escape/Unescape as a URI/URL path
 */
${#uris.escapePath(uri)}
${#uris.escapePath(uri, encoding)}
${#uris.unescapePath(uri)}
${#uris.unescapePath(uri, encoding)}

/*
 * Escape/Unescape as a URI/URL path segment (between '/' symbols)
 */
${#uris.escapePathSegment(uri)}
${#uris.escapePathSegment(uri, encoding)}
${#uris.unescapePathSegment(uri)}
${#uris.unescapePathSegment(uri, encoding)}

/*
 * Escape/Unescape as a Fragment Identifier (#frag)
 */
${#uris.escapeFragmentId(uri)}
${#uris.escapeFragmentId(uri, encoding)}
${#uris.unescapeFragmentId(uri)}
${#uris.unescapeFragmentId(uri, encoding)}

/*
 * Escape/Unescape as a Query Parameter (?var=value)
 */
${#uris.escapeQueryParam(uri)}
${#uris.escapeQueryParam(uri, encoding)}
${#uris.unescapeQueryParam(uri)}
${#uris.unescapeQueryParam(uri, encoding)}
```


### Conversions

 * **\#conversions** : utility object that allows the execution of the
   *Conversion Service* at any point of a template:

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Conversions
 * ======================================================================
 */

/*
 * Execute the desired conversion of the 'object' value into the
 * specified class.
 */
${#conversions.convert(object, 'java.util.TimeZone')}
${#conversions.convert(object, targetClass)}
```


### Dates

 * **\#dates** : utility methods for `java.util.Date` objects:

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Dates
 * ======================================================================
 */

/*
 * Format date with the standard locale format
 * Also works with arrays, lists or sets
 */
${#dates.format(date)}
${#dates.arrayFormat(datesArray)}
${#dates.listFormat(datesList)}
${#dates.setFormat(datesSet)}

/*
 * Format date with the ISO8601 format
 * Also works with arrays, lists or sets
 */
${#dates.formatISO(date)}
${#dates.arrayFormatISO(datesArray)}
${#dates.listFormatISO(datesList)}
${#dates.setFormatISO(datesSet)}

/*
 * Format date with the specified pattern
 * Also works with arrays, lists or sets
 */
${#dates.format(date, 'dd/MMM/yyyy HH:mm')}
${#dates.arrayFormat(datesArray, 'dd/MMM/yyyy HH:mm')}
${#dates.listFormat(datesList, 'dd/MMM/yyyy HH:mm')}
${#dates.setFormat(datesSet, 'dd/MMM/yyyy HH:mm')}

/*
 * Obtain date properties
 * Also works with arrays, lists or sets
 */
${#dates.day(date)}                    // also arrayDay(...), listDay(...), etc.
${#dates.month(date)}                  // also arrayMonth(...), listMonth(...), etc.
${#dates.monthName(date)}              // also arrayMonthName(...), listMonthName(...), etc.
${#dates.monthNameShort(date)}         // also arrayMonthNameShort(...), listMonthNameShort(...), etc.
${#dates.year(date)}                   // also arrayYear(...), listYear(...), etc.
${#dates.dayOfWeek(date)}              // also arrayDayOfWeek(...), listDayOfWeek(...), etc.
${#dates.dayOfWeekName(date)}          // also arrayDayOfWeekName(...), listDayOfWeekName(...), etc.
${#dates.dayOfWeekNameShort(date)}     // also arrayDayOfWeekNameShort(...), listDayOfWeekNameShort(...), etc.
${#dates.hour(date)}                   // also arrayHour(...), listHour(...), etc.
${#dates.minute(date)}                 // also arrayMinute(...), listMinute(...), etc.
${#dates.second(date)}                 // also arraySecond(...), listSecond(...), etc.
${#dates.millisecond(date)}            // also arrayMillisecond(...), listMillisecond(...), etc.

/*
 * Create date (java.util.Date) objects from its components
 */
${#dates.create(year,month,day)}
${#dates.create(year,month,day,hour,minute)}
${#dates.create(year,month,day,hour,minute,second)}
${#dates.create(year,month,day,hour,minute,second,millisecond)}

/*
 * Create a date (java.util.Date) object for the current date and time
 */
${#dates.createNow()}

${#dates.createNowForTimeZone()}

/*
 * Create a date (java.util.Date) object for the current date (time set to 00:00)
 */
${#dates.createToday()}

${#dates.createTodayForTimeZone()}
```


### Calendars

 * **\#calendars** : analogous to `#dates`, but for `java.util.Calendar` objects:

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Calendars
 * ======================================================================
 */

/*
 * Format calendar with the standard locale format
 * Also works with arrays, lists or sets
 */
${#calendars.format(cal)}
${#calendars.arrayFormat(calArray)}
${#calendars.listFormat(calList)}
${#calendars.setFormat(calSet)}

/*
 * Format calendar with the ISO8601 format
 * Also works with arrays, lists or sets
 */
${#calendars.formatISO(cal)}
${#calendars.arrayFormatISO(calArray)}
${#calendars.listFormatISO(calList)}
${#calendars.setFormatISO(calSet)}

/*
 * Format calendar with the specified pattern
 * Also works with arrays, lists or sets
 */
${#calendars.format(cal, 'dd/MMM/yyyy HH:mm')}
${#calendars.arrayFormat(calArray, 'dd/MMM/yyyy HH:mm')}
${#calendars.listFormat(calList, 'dd/MMM/yyyy HH:mm')}
${#calendars.setFormat(calSet, 'dd/MMM/yyyy HH:mm')}

/*
 * Obtain calendar properties
 * Also works with arrays, lists or sets
 */
${#calendars.day(date)}                // also arrayDay(...), listDay(...), etc.
${#calendars.month(date)}              // also arrayMonth(...), listMonth(...), etc.
${#calendars.monthName(date)}          // also arrayMonthName(...), listMonthName(...), etc.
${#calendars.monthNameShort(date)}     // also arrayMonthNameShort(...), listMonthNameShort(...), etc.
${#calendars.year(date)}               // also arrayYear(...), listYear(...), etc.
${#calendars.dayOfWeek(date)}          // also arrayDayOfWeek(...), listDayOfWeek(...), etc.
${#calendars.dayOfWeekName(date)}      // also arrayDayOfWeekName(...), listDayOfWeekName(...), etc.
${#calendars.dayOfWeekNameShort(date)} // also arrayDayOfWeekNameShort(...), listDayOfWeekNameShort(...), etc.
${#calendars.hour(date)}               // also arrayHour(...), listHour(...), etc.
${#calendars.minute(date)}             // also arrayMinute(...), listMinute(...), etc.
${#calendars.second(date)}             // also arraySecond(...), listSecond(...), etc.
${#calendars.millisecond(date)}        // also arrayMillisecond(...), listMillisecond(...), etc.

/*
 * Create calendar (java.util.Calendar) objects from its components
 */
${#calendars.create(year,month,day)}
${#calendars.create(year,month,day,hour,minute)}
${#calendars.create(year,month,day,hour,minute,second)}
${#calendars.create(year,month,day,hour,minute,second,millisecond)}

${#calendars.createForTimeZone(year,month,day,timeZone)}
${#calendars.createForTimeZone(year,month,day,hour,minute,timeZone)}
${#calendars.createForTimeZone(year,month,day,hour,minute,second,timeZone)}
${#calendars.createForTimeZone(year,month,day,hour,minute,second,millisecond,timeZone)}

/*
 * Create a calendar (java.util.Calendar) object for the current date and time
 */
${#calendars.createNow()}

${#calendars.createNowForTimeZone()}

/*
 * Create a calendar (java.util.Calendar) object for the current date (time set to 00:00)
 */
${#calendars.createToday()}

${#calendars.createTodayForTimeZone()}
```


### Numbers

 * **\#numbers** : utility methods for number objects:

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Numbers
 * ======================================================================
 */

/*
 * ==========================
 * Formatting integer numbers
 * ==========================
 */

/*
 * Set minimum integer digits.
 * Also works with arrays, lists or sets
 */
${#numbers.formatInteger(num,3)}
${#numbers.arrayFormatInteger(numArray,3)}
${#numbers.listFormatInteger(numList,3)}
${#numbers.setFormatInteger(numSet,3)}


/*
 * Set minimum integer digits and thousands separator:
 * 'POINT', 'COMMA', 'WHITESPACE', 'NONE' or 'DEFAULT' (by locale).
 * Also works with arrays, lists or sets
 */
${#numbers.formatInteger(num,3,'POINT')}
${#numbers.arrayFormatInteger(numArray,3,'POINT')}
${#numbers.listFormatInteger(numList,3,'POINT')}
${#numbers.setFormatInteger(numSet,3,'POINT')}


/*
 * ==========================
 * Formatting decimal numbers
 * ==========================
 */

/*
 * Set minimum integer digits and (exact) decimal digits.
 * Also works with arrays, lists or sets
 */
${#numbers.formatDecimal(num,3,2)}
${#numbers.arrayFormatDecimal(numArray,3,2)}
${#numbers.listFormatDecimal(numList,3,2)}
${#numbers.setFormatDecimal(numSet,3,2)}

/*
 * Set minimum integer digits and (exact) decimal digits, and also decimal separator.
 * Also works with arrays, lists or sets
 */
${#numbers.formatDecimal(num,3,2,'COMMA')}
${#numbers.arrayFormatDecimal(numArray,3,2,'COMMA')}
${#numbers.listFormatDecimal(numList,3,2,'COMMA')}
${#numbers.setFormatDecimal(numSet,3,2,'COMMA')}

/*
 * Set minimum integer digits and (exact) decimal digits, and also thousands and
 * decimal separator.
 * Also works with arrays, lists or sets
 */
${#numbers.formatDecimal(num,3,'POINT',2,'COMMA')}
${#numbers.arrayFormatDecimal(numArray,3,'POINT',2,'COMMA')}
${#numbers.listFormatDecimal(numList,3,'POINT',2,'COMMA')}
${#numbers.setFormatDecimal(numSet,3,'POINT',2,'COMMA')}


/*
 * =====================
 * Formatting currencies
 * =====================
 */

${#numbers.formatCurrency(num)}
${#numbers.arrayFormatCurrency(numArray)}
${#numbers.listFormatCurrency(numList)}
${#numbers.setFormatCurrency(numSet)}


/*
 * ======================
 * Formatting percentages
 * ======================
 */

${#numbers.formatPercent(num)}
${#numbers.arrayFormatPercent(numArray)}
${#numbers.listFormatPercent(numList)}
${#numbers.setFormatPercent(numSet)}

/*
 * Set minimum integer digits and (exact) decimal digits.
 */
${#numbers.formatPercent(num, 3, 2)}
${#numbers.arrayFormatPercent(numArray, 3, 2)}
${#numbers.listFormatPercent(numList, 3, 2)}
${#numbers.setFormatPercent(numSet, 3, 2)}


/*
 * ===============
 * Utility methods
 * ===============
 */

/*
 * Create a sequence (array) of integer numbers going
 * from x to y
 */
${#numbers.sequence(from,to)}
${#numbers.sequence(from,to,step)}
```


### Strings

 * **\#strings** : utility methods for `String` objects:

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Strings
 * ======================================================================
 */

/*
 * Null-safe toString()
 */
${#strings.toString(obj)}                           // also array*, list* and set*

/*
 * Check whether a String is empty (or null). Performs a trim() operation before check
 * Also works with arrays, lists or sets
 */
${#strings.isEmpty(name)}
${#strings.arrayIsEmpty(nameArr)}
${#strings.listIsEmpty(nameList)}
${#strings.setIsEmpty(nameSet)}

/*
 * Perform an 'isEmpty()' check on a string and return it if false, defaulting to
 * another specified string if true.
 * Also works with arrays, lists or sets
 */
${#strings.defaultString(text,default)}
${#strings.arrayDefaultString(textArr,default)}
${#strings.listDefaultString(textList,default)}
${#strings.setDefaultString(textSet,default)}

/*
 * Check whether a fragment is contained in a String
 * Also works with arrays, lists or sets
 */
${#strings.contains(name,'ez')}                     // also array*, list* and set*
${#strings.containsIgnoreCase(name,'ez')}           // also array*, list* and set*

/*
 * Check whether a String starts or ends with a fragment
 * Also works with arrays, lists or sets
 */
${#strings.startsWith(name,'Don')}                  // also array*, list* and set*
${#strings.endsWith(name,endingFragment)}           // also array*, list* and set*

/*
 * Substring-related operations
 * Also works with arrays, lists or sets
 */
${#strings.indexOf(name,frag)}                      // also array*, list* and set*
${#strings.substring(name,3,5)}                     // also array*, list* and set*
${#strings.substringAfter(name,prefix)}             // also array*, list* and set*
${#strings.substringBefore(name,suffix)}            // also array*, list* and set*
${#strings.replace(name,'las','ler')}               // also array*, list* and set*

/*
 * Append and prepend
 * Also works with arrays, lists or sets
 */
${#strings.prepend(str,prefix)}                     // also array*, list* and set*
${#strings.append(str,suffix)}                      // also array*, list* and set*

/*
 * Change case
 * Also works with arrays, lists or sets
 */
${#strings.toUpperCase(name)}                       // also array*, list* and set*
${#strings.toLowerCase(name)}                       // also array*, list* and set*

/*
 * Split and join
 */
${#strings.arrayJoin(namesArray,',')}
${#strings.listJoin(namesList,',')}
${#strings.setJoin(namesSet,',')}
${#strings.arraySplit(namesStr,',')}                // returns String[]
${#strings.listSplit(namesStr,',')}                 // returns List<String>
${#strings.setSplit(namesStr,',')}                  // returns Set<String>

/*
 * Trim
 * Also works with arrays, lists or sets
 */
${#strings.trim(str)}                               // also array*, list* and set*

/*
 * Compute length
 * Also works with arrays, lists or sets
 */
${#strings.length(str)}                             // also array*, list* and set*

/*
 * Abbreviate text making it have a maximum size of n. If text is bigger, it
 * will be clipped and finished in "..."
 * Also works with arrays, lists or sets
 */
${#strings.abbreviate(str,10)}                      // also array*, list* and set*

/*
 * Convert the first character to upper-case (and vice-versa)
 */
${#strings.capitalize(str)}                         // also array*, list* and set*
${#strings.unCapitalize(str)}                       // also array*, list* and set*

/*
 * Convert the first character of every word to upper-case
 */
${#strings.capitalizeWords(str)}                    // also array*, list* and set*
${#strings.capitalizeWords(str,delimiters)}         // also array*, list* and set*

/*
 * Escape the string
 */
${#strings.escapeXml(str)}                          // also array*, list* and set*
${#strings.escapeJava(str)}                         // also array*, list* and set*
${#strings.escapeJavaScript(str)}                   // also array*, list* and set*
${#strings.unescapeJava(str)}                       // also array*, list* and set*
${#strings.unescapeJavaScript(str)}                 // also array*, list* and set*

/*
 * Null-safe comparison and concatenation
 */
${#strings.equals(first, second)}
${#strings.equalsIgnoreCase(first, second)}
${#strings.concat(values...)}
${#strings.concatReplaceNulls(nullValue, values...)}

/*
 * Random
 */
${#strings.randomAlphanumeric(count)}
```


### Objects

 * **\#objects** : utility methods for objects in general

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Objects
 * ======================================================================
 */

/*
 * Return obj if it is not null, and default otherwise
 * Also works with arrays, lists or sets
 */
${#objects.nullSafe(obj,default)}
${#objects.arrayNullSafe(objArray,default)}
${#objects.listNullSafe(objList,default)}
${#objects.setNullSafe(objSet,default)}
```


### Booleans

 * **\#bools** : utility methods for boolean evaluation

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Bools
 * ======================================================================
 */

/*
 * Evaluate a condition in the same way that it would be evaluated in a th:if tag
 * (see conditional evaluation chapter afterwards).
 * Also works with arrays, lists or sets
 */
${#bools.isTrue(obj)}
${#bools.arrayIsTrue(objArray)}
${#bools.listIsTrue(objList)}
${#bools.setIsTrue(objSet)}

/*
 * Evaluate with negation
 * Also works with arrays, lists or sets
 */
${#bools.isFalse(cond)}
${#bools.arrayIsFalse(condArray)}
${#bools.listIsFalse(condList)}
${#bools.setIsFalse(condSet)}

/*
 * Evaluate and apply AND operator
 * Receive an array, a list or a set as parameter
 */
${#bools.arrayAnd(condArray)}
${#bools.listAnd(condList)}
${#bools.setAnd(condSet)}

/*
 * Evaluate and apply OR operator
 * Receive an array, a list or a set as parameter
 */
${#bools.arrayOr(condArray)}
${#bools.listOr(condList)}
${#bools.setOr(condSet)}
```


### Arrays

 * **\#arrays** : utility methods for arrays

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Arrays
 * ======================================================================
 */

/*
 * Converts to array, trying to infer array component class.
 * Note that if resulting array is empty, or if the elements
 * of the target object are not all of the same class,
 * this method will return Object[].
 */
${#arrays.toArray(object)}

/*
 * Convert to arrays of the specified component class.
 */
${#arrays.toStringArray(object)}
${#arrays.toIntegerArray(object)}
${#arrays.toLongArray(object)}
${#arrays.toDoubleArray(object)}
${#arrays.toFloatArray(object)}
${#arrays.toBooleanArray(object)}

/*
 * Compute length
 */
${#arrays.length(array)}

/*
 * Check whether array is empty
 */
${#arrays.isEmpty(array)}

/*
 * Check if element or elements are contained in array
 */
${#arrays.contains(array, element)}
${#arrays.containsAll(array, elements)}
```


### Lists

 * **\#lists** : utility methods for lists

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Lists
 * ======================================================================
 */

/*
 * Converts to list
 */
${#lists.toList(object)}

/*
 * Compute size
 */
${#lists.size(list)}

/*
 * Check whether list is empty
 */
${#lists.isEmpty(list)}

/*
 * Check if element or elements are contained in list
 */
${#lists.contains(list, element)}
${#lists.containsAll(list, elements)}

/*
 * Sort a copy of the given list. The members of the list must implement
 * comparable or you must define a comparator.
 */
${#lists.sort(list)}
${#lists.sort(list, comparator)}
```


### Sets

 * **\#sets** : utility methods for sets

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Sets
 * ======================================================================
 */

/*
 * Converts to set
 */
${#sets.toSet(object)}

/*
 * Compute size
 */
${#sets.size(set)}

/*
 * Check whether set is empty
 */
${#sets.isEmpty(set)}

/*
 * Check if element or elements are contained in set
 */
${#sets.contains(set, element)}
${#sets.containsAll(set, elements)}
```


### Maps

 * **\#maps** : utility methods for maps

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Maps
 * ======================================================================
 */

/*
 * Compute size
 */
${#maps.size(map)}

/*
 * Check whether map is empty
 */
${#maps.isEmpty(map)}

/*
 * Check if key/s or value/s are contained in maps
 */
${#maps.containsKey(map, key)}
${#maps.containsAllKeys(map, keys)}
${#maps.containsValue(map, value)}
${#maps.containsAllValues(map, value)}
```


### Aggregates

 * **\#aggregates** : utility methods for creating aggregates on arrays or
   collections

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Aggregates
 * ======================================================================
 */

/*
 * Compute sum. Returns null if array or collection is empty
 */
${#aggregates.sum(array)}
${#aggregates.sum(collection)}

/*
 * Compute average. Returns null if array or collection is empty
 */
${#aggregates.avg(array)}
${#aggregates.avg(collection)}
```


### IDs

 * **\#ids** : utility methods for dealing with `id` attributes that might be
   repeated (for example, as a result of an iteration).

```java
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Ids
 * ======================================================================
 */

/*
 * Normally used in th:id attributes, for appending a counter to the id attribute value
 * so that it remains unique even when involved in an iteration process.
 */
${#ids.seq('someId')}

/*
 * Normally used in th:for attributes in <label> tags, so that these labels can refer to Ids
 * generated by means if the #ids.seq(...) function.
 *
 * Depending on whether the <label> goes before or after the element with the #ids.seq(...)
 * function, the "next" (label goes before "seq") or the "prev" function (label goes after
 * "seq") function should be called.
 */
${#ids.next('someId')}
${#ids.prev('someId')}
```




20 附录 C: 标记选择器语法
=====================================

Thymeleaf's Markup Selectors are directly borrowed from Thymeleaf's parsing
library: [AttoParser](http://attoparser.org).

The syntax for this selectors has large similarities with that of selectors in
XPath, CSS and jQuery, which makes them easy to use for most users. You can have
a look at the complete syntax reference at the
[AttoParser documentation](http://www.attoparser.org/apidocs/attoparser/2.0.4.RELEASE/org/attoparser/select/package-summary.html).

For example, the following selector will select every `<div>` with the class `content`,
in every position inside the markup (note this is not as concise as it could be,
read on to know why):

```html
<div th:insert="mytemplate :: //div[@class='content']">...</div>
```

The basic syntax includes:

 * `/x` means direct children of the current node with name x.

 * `//x` means children of the current node with name x, at any depth.

 * `x[@z="v"]` means elements with name x and an attribute called z with value
   "v".

 * `x[@z1="v1" and @z2="v2"]` means elements with name x and attributes z1 and
   z2 with values "v1" and "v2", respectively.

 * `x[i]` means element with name x positioned in number i among its siblings.

 * `x[@z="v"][i]` means elements with name x, attribute z with value "v" and
   positioned in number i among its siblings that also match this condition.

But more concise syntax can also be used:

 * `x` is exactly equivalent to `//x` (search an element with name or reference
   `x` at any depth level, a *reference* being a `th:ref` or a `th:fragment` attribute).

 * Selectors are also allowed without element name/reference, as long as they
   include a specification of arguments. So `[@class='oneclass']` is a valid
   selector that looks for any elements (tags) with a class attribute with value
   `"oneclass"`.

Advanced attribute selection features:

 * Besides `=` (equal), other comparison operators are also valid: `!=` (not
   equal), `^=` (starts with) and `$=` (ends with). For example: `x[@class^='section']`
   means elements with name `x` and a value for attribute `class` that starts
   with `section`.

 * Attributes can be specified both starting with `@` (XPath-style) and without
   (jQuery-style). So `x[z='v']` is equivalent to `x[@z='v']`.

 * Multiple-attribute modifiers can be joined both with `and` (XPath-style) and
   also by chaining multiple modifiers (jQuery-style). So `x[@z1='v1' and @z2='v2']`
   is actually equivalent to `x[@z1='v1'][@z2='v2']` (and also to `x[z1='v1'][z2='v2']`).

Direct _jQuery-like_ selectors:

 * `x.oneclass` is equivalent to `x[class='oneclass']`.

 * `.oneclass` is equivalent to `[class='oneclass']`.

 * `x#oneid` is equivalent to `x[id='oneid']`.

 * `#oneid` is equivalent to `[id='oneid']`.

 * `x%oneref` means `<x>` tags that have a `th:ref="oneref"` or `th:fragment="oneref"`
   attribute.

 * `%oneref` means any tags that have a `th:ref="oneref"` or `th:fragment="oneref"`
   attribute. Note this is actually equivalent to simply `oneref` because
   references can be used instead of element names.

 * Direct selectors and attribute selectors can be mixed: `a.external[@href^='https']`.

So the above Markup Selector expression:

```html
<div th:insert="mytemplate :: //div[@class='content']">...</div>
```

Could be written as:

```html
<div th:insert="mytemplate :: div.content">...</div>
```

Examining a different example, this:

```html
<div th:replace="mytemplate :: myfrag">...</div>
```

Will look for a `th:fragment="myfrag"` fragment signature (or `th:ref`
references). But would also look for tags with name `myfrag` if they existed
(which they don't, in HTML). Note the difference with:

```html
<div th:replace="mytemplate :: .myfrag">...</div>
```

...which will actually look for any elements with `class="myfrag"`, without
caring about `th:fragment` signatures (or `th:ref` references).


### Multivalued class matching

Markup Selectors understand the class attribute to be **multivalued**, and
therefore allow the application of selectors on this attribute even if the
element has several class values.

For example, `div.two` will match `<div class="one two three" />`
