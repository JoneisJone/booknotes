## Spring Boot项目构建

#### 使用 Spring Initializr 构建

Spring 官方提供了 Spring Initializr 来进行 Spring Boot 的快速构建，这是一个在线生成 Spring Boot 基础项目的工具，我们可以将其理解为 Spring Boot 的“创建向导”，接下来我们使用这个在线向导来快速的创建一个 Spring Boot 骨架工程。

- 首先，打开在浏览器中输入 Spring Initializr 的网站地址：[https://start.spring.io](https://start.spring.io/)
- 之后可以看到页面上需要我们填写和选择项目的基础信息，依次填写即可
- 最后点击“Generate Project”按钮即可获取到一个 Spring Boot 基础项目的代码压缩包

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10294timestamp1552979043544.png)

如图所示，Spring Boot 版本我们选择的是最新稳定版本 2.1.0 ，当然也可以选择其他稳定版本，视项目要求而定，“ dependencies ” 表示添加到项目所依赖的 Spring Boot 组件，根据项目要求来选择，需要哪些场景就直接选择相应模块即可，与 SpringBoot Initializr 构建方式类似，也可以多选，本次演示选择了 Web 模块。

#### mvn 命令行创建 Spring Boot 项目

打开命令行并将目录切换到对应的文件夹中，之后运行以下命令：

```
mvn archetype:generate -DinteractiveMode=false -DgroupId=com.lou.springboot -DartifactId=springboot-demo -Dversion=0.0.1-SNAPSHOT
```

在构建成功后可以生成骨架项目，但是由于生成的项目仅仅是骨架项目，因此 pom.xml 文件中需要自己添加依赖，主方法启动类也需要自行添加，并不是很方便，因此不是特别推荐。

#### 直接打开

最后一种方式是直接导入 Spring Boot 项目，如果已经存在 Spring Boot 项目则直接打开，与引入普通的 Maven 工程类似，解压并通过 IDE 打开项目即可(比如 Eclipse、IDEA 或者实验楼的 WebIDE )，导入成功就可以进行 Spring Boot 项目开发了。



打开项目之后可以看到 Spring Boot 项目的目录结构如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10294timestamp1552979067764.png)

如上图所示，Spring Boot 的目录结构主要由以下部分组成：

```properties
lou-springboot
    ├── src/main/java
    ├── src/main/resources
    ├── src/test/java
    └── pom.xml
```

其中 `src/main/java` 表示 Java 程序开发目录，这个目录大家应该都比较熟悉，唯一的区别是 Spring Boot 项目中还有一个主程序类。

`src/main/resources` 表示资源文件目录，与普通的 Spring 项目相比有些区别，如上图所示该目录下有 `static` 和 `templates` 两个目录，是 Spring Boot 项目默认的静态资源文件目录和模板文件目录，在 Spring Boot 项目中是没有 `webapp` 目录的，默认是使用 `static` 和 `templates` 两个文件夹。

`src/test/java` 表示测试类文件夹，与普通的 Spring 项目差别不大。

pom.xml 用于配置项目依赖。

以上即为 Spring Boot 项目的目录结构，与普通的 Spring 项目存在一些差异，不过在平常开发过程中，这个差异的影响并不大，说到差别较大的地方可能是部署和启动方式的差异

#### Main() 方法启动

与普通的 Web 项目相比，Spring Boot 启动项目减少了几个中间步骤，不用去配置 Servlet 容器，也不用打包并且发布到 Servlet 容器再去启动，而是直接运行主方法即可启动项目，开发调试都十分方便也节省开发时间。在你的本机上开发项目时，可以直接在 Eclipse 或者 IDEA 中运行 Spring Boot 主程序类即可，比如现在的项目中有 Application 类，可以直接运行它的 run() 方法，项目就能够正常启动了。

#### Maven 插件启动

由于 pom.xml 文件中引入了 `spring-boot-maven-plugin` 插件依赖，也可以直接使用 Maven 命令来启动 Spring Boot 项目，插件配置如下。如果 pom.xml 文件中没有该 Maven 插件，是无法通过这种方式启动 Spring Boot 项目的，这一点需要注意。

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

启动过程过程如下图所示，首先点击下方工具栏中的 Terminal 打开命令行窗口，之后在命令行中输入命令 `mvn spring-boot:run` 并执行该命令即可启动项目，如下图所示，Spring Boot 项目启动成功。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10294timestamp1552979089834.png)

#### java -jar 命令启动

项目初始化时我们选择的打包方式为 Jar ，因此项目开发完成进行打包时的结果是一个 Jar 包， Java 运行 Jar 包的命令为 `java -jar xxx.jar` ，结合以上两个原因我们可以使用这种方式启动 Spring Boot 项目，接下来我们来演示这一过程。

- 首先，点击下方工具栏中的 Terminal 打开命令行窗口
- 之后使用 Maven 命令将项目打包，执行命令为:`mvn clean package -Dmaven.test.skip=true`，等待打包结果即可
- 打包成功后进入 target 目录，`cd target`
- 最后就是启动已经生成的 Jar 包，执行命令为`java -jar springboot-demo-0.0.1-SNAPSHOT.jar`

这种方式也是 Spring Boot 上线时常用的启动流程



## SpringBoot项目 Web项目开发

#### 实验内容

我们将在这一节学习 Spring Boot 中如何进行 web 相关功能的开发，主要包括常用的接口开发和 Spring Boot 对于静态资源文件的处理。

#### 实验知识点

- DispatchServlet 配置
- Spring Boot 如何处理静态 web 资源
- 消息转换器 HttpMessageConverter

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+



我们自行实现一个 Controller 来测试一下 Spring Boot 如何处理 web 请求，接下来使用 Spring Boot 做一个简单的接口实现。在 `src/main/java` 目录下新建 `com.lou.springboot.controller` 包，之后在包里新建一个 HelloController 类，编码如下:

```java
package com.lou.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {

    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "hello,shiyanlou";
    }
}
```

这段代码大家应该很熟悉，在 Spring Boot 项目中，大部分功能实现的写法与 Spring 项目开发的写法是相同的，这段代码的含义就是处理请求路径为 `/hello` 的 get 请求，之后返回一段字符串。 编码完成后重新启动项目，点击 WEB 服务按钮，并在浏览器地址栏地址末尾中添加 `/hello`，如下图所示，咱们的第一个 Spring Boot 项目实例就完成了！

![hello](https://doc.shiyanlou.com/document-uid441493labid8432timestamp1550040465445.png)

我们创建了第一个 Spring Boot 项目，在初始化时我们选择了 web 模块(即在 pom 文件中引入了 `spring-boot-starter-web` 依赖)，该模块中包含了 SpringMVC 相关依赖，之后我们开发了第一个 web 功能，仅仅是在控制器类中实现了一个简单的字符串返回方法，并对该方法进行了请求地址映射设置，之后没有做其他配置操作，但是项目启动后就能够进行正常的 web 请求并正常返回数据。 咱们来回想一下在未使用 Spring Boot 开发 web 应用的场景，在平时的 Spring web 项目中，使用 SpringMVC 模块时需要在容器中 SpringMVC 相关的配置 bean，并且在 web.xml 中配置 DispatcherServlet 以及它的 进行请求地址的映射，配置文件如下:

```xml
    <!--Start spring mvc Servlet-->
    <servlet>
        <servlet-name>Spring</Servlet-name>
        <servlet-class>org.springframework.web.Servlet.DispatcherServlet</Servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!--End spring mvc Servlet-->
    <!--Start Servlet-mapping -->
    <servlet-mapping>
        <Servlet-name>Spring</Servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!--End Servlet-mapping -->
```

最后启动 Tomcat 服务器装载 DispatcherServlet，接着就可以进行基于 SpringMVC 框架的 web 项目开发和使用，但是 Spring Boot 开发 web 项目时，开发者只需导入 `spring-boot-starter-web` 场景启动器即可，根本无需再进行任何配置就能够使得 DispatherServlet 正常加载并使用，这是因为 **Spring Boot 在项目启动时已经将 DispatherServlet 自动配置到项目中**了，因此我们无需做多余的配置即可完成接口的开发。

不过呢，使用 Spring Boot 开发 web 应用时，有两个需要注意的配置项：

- **server.port** 表示启动后的端口号，默认是 8080
- **server.servlet.context-path** 项目路径，是构成 url 地址的一部分

如果想要修改这两个参数值，可以在 `application.properties` 配置文件中修改，示例如下：

```properties
server.port=8082
server.servlet.context-path=/shiyanlou/
```

这样的话，启动后的端口号就不再是 8080 而是 8082 了，在浏览器访问时也需要加上设置的项目路径，比如前面的 hello 请求，路径就变成了 http://localhost:8082/shiyanlou/hello ，这一点大家需要注意，如果想要对端口和项目访问路径做修改的话，修改这两个参数就可以了，由于实验楼线上环境只能访问 8080 端口，因此端口修改测试的访问需要在你本机上进行。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10296timestamp1552979992992.png)

静态资源处理



简单来说，在 B/S 架构中，静态资源一般指 Web 服务器对应目录中的资源文件，客户端发送请求到 Web 服务器后，Web 服务器（比如 Tomcat）直接从文件目录中获取文件并返回给客户端，客户端解析并渲染显示出来，比如 HTML、CSS、JavaScript、图片等文件，这些文件原样返回给客户端，并不会在 Web 服务器端有所改变，在进行 web 项目开发时，对于这些资源的处理也是十分重要的，这一小节我们将会讲解 Spring Boot 是如何处理这些文件的。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10296timestamp1552980027081.png)

通过查看源码我们可以看到，Spring Boot 默认存放静态资源文件的目录有四个，这些目录都在类路径下，它们分别是：

- **/META-INF/resources/**
- **/resources/**
- **/static/**
- **/public/**

也就是说，在进行 web 功能开发时，如果项目中存在静态资源文件，如 HTML、CSS、JavaScript、图片等文件，只要将这些文件放到以上四个文件目录即可正常访问到。接下来，我们新建四个目录并进行测试，类路径对应到项目路径中就是 **src/main/resources** ，新的目录结构如下：

```bash
src/main/resources
  ├── application.properties
  ├── META-INF
  │   └── resources
  ├── public
  ├── resources
  ├── static
  └── templates
```

为了验证，我们在分别在每个目录中新增一个静态资源文件，分别是 /META-INF/resources/test.html 、/resources/shiyanlou.png 、/static/test.css 、/public/test.js :

为 test.html 添加如下内容：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>test</title>
  </head>
  <body>
    resource - html
  </body>
</html>
```

下载 shiyanlou.png 到对应目录。

```bash
wget https://labfile.oss.aliyuncs.com/courses/1274/shiyanlou.png
```

为 test.css 添加如下内容：

```css
. test {
  font-size: 14px;
}
```

为 test.js 添加如下内容：

```js
function test() {
  console.log('resource - js');
}
```

接下来启动项目，启动成功后可以点击页面上方的 Web 服务直接在显示查看网站效果。

```bash
mvn spring-boot:run
```

之后点击 Web 服务，我们在路径中分别进行接口的访问和静态资源的访问，看一下服务器返回的结果，访问路径为：

- /test.html
- /test.css
- /test.js
- /shiyanlou.png

结果都是正确的，文件虽然在不同的路径中，

`但是四个路径都是默认的静态资源目录，因此都能够被访问到。`

当然，有的同学可能会说了，我就想要访问 shiyanlou 目录下的静态资源文件，这种情况该怎么办呢？其实，前面四个静态资源目录是 Spring Boot 默认设置的，如果想要更改，可以修改配置文件中的 `spring.resources.static-locations` 参数。比如，我们想要访问 shiyanlou 目录下的静态资源文件，首先需要在`src/main/resources`下新建 shiyanlou 目录，将静态资源复制到该目录中：

```bash
shiyanlou
  ├── shiyanlou.png
  ├── test.css
  └── test.html
```

我们复制了三个静态资源文件，test.js 依然放在 static 目录中，之后修改配置文件，添加如下设置：

```properties
spring.resources.static-locations=classpath:/shiyanlou/
```

重启后，在浏览器中再次分别访问这些静态资源，你会发现 shiyanlou 目录下的三个文件都能够正常访问，

（和上面键入内容一样，不用补充/shiyanlou/路径，因为把这个路径配置成了静态资源的默认路径），且优先级更高。

但是 test.js 访问时报错 404 了，因为配置文件中静态资源路径并没有包含 static 目录，shiyanlou 目录下也没有 test.js。如果想要正常访问到该文件，有两个办法，一是将 test.js 文件放到 shiyanlou 目录下，二是修改配置文件为 ：

```properties
spring.resources.static-locations=classpath:/shiyanlou/,classpath:/static/
```

`spring.resources.static-locations` 的参数值可以设置为一个数组。这样修改后，重启后又能够访问所有的静态资源文件了。



消息转换器 HttpMessageConverter



以往在使用 SpringMVC 框架开发项目时，大家应该都使用过 `@RequestBody`、`@ResponseBody` 注解进行请求实体的转换和响应结果的格式化输出，以普遍使用的 json 数据为例，这两个注解的作用分别可以将请求中的数据解析成 json 并绑定为实体对象以及将响应结果以 json 格式返回给请求发起者，但 Http 请求和响应是基于文本的，也就是说在 SpringMVC 内部维护了一套转换机制，也就是我们通常所说的“将 json 格式的请求信息转换为一个对象，将对象转换为 json 格式并输出为响应信息 ”，这些就是 HttpMessageConverter 的作用。

举一个简单的例子，我们定义一个实体类，并通过 `@RequestBody`、`@ResponseBody` 注解进行参数的读取和响应，代码如下：

在 com.lou.springboot 下创建如下目录结构：

```bash
com.lou.springboot
     ├── Application.java
     ├── controller
     │   ├── HelloController.java
     │   └── TestController.java
     ├── entity
     │   └── SaleGoods.java
     └── service
         └── HelloService.java
```

SaleGoods.java 的代码内容如下：

```java
// 实体类
package com.lou.springboot.entity;

public class SaleGoods {
    private Integer id;
    private String goodsName;
    private float weight;
    private int type;
    private Boolean onSale;
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getGoodsName() {
        return goodsName;
    }
    public void setGoodsName(String goodsName) {
        this.goodsName = goodsName;
    }
    public float getWeight() {
        return weight;
    }
    public void setWeight(float weight) {
        this.weight = weight;
    }
    public Boolean getOnSale() {
        return onSale;
    }
    public void setOnSale(Boolean onSale) {
        this.onSale = onSale;
    }
    public int getType() {
        return type;
    }
    public void setType(int type) {
        this.type = type;
    }
    @Override
    public String toString() {
        return "SaleGoods{" +
                "id=" + id +
                ", goodsName='" + goodsName + '\'' +
                ", weight=" + weight +
                ", type=" + type +
                ", onSale=" + onSale +
                '}';
    }
}
```

TestController.java 控制器方法如下，拿到参数数值后进行简单的修改并将对象数据返回：

```java
package com.lou.springboot.controller;
import com.lou.springboot.entity.SaleGoods;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
@Controller
public class TestController {
    @RequestMapping(value = "/get/httpmessageconverter", method = RequestMethod.GET)
    @ResponseBody
    public SaleGoods httpMessageConverterTest() {
        SaleGoods saleGoods = new SaleGoods();
        saleGoods.setGoodsName("华为手机");
        saleGoods.setId(1);
        saleGoods.setOnSale(true);
        saleGoods.setType(1);
        saleGoods.setWeight(300);
        return saleGoods;
    }
}
```

编码完成后重启项目，点击 WEB 服务，并在地址栏地址末尾添加 `/get/httpmessageconverter` 进行测试，结果如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid1375timestamp1555583359660.png)

返回数据如下：

```json
{
  "id": 1,
  "goodsName": "华为手机",
  "weight": 300.0,
  "type": 1,
  "onSale": true
}
```

添加 `@ResponseBody` 注解后，Spring Boot 会直接将对象转换为 json 格式并输出为响应信息，这是将对象作为相应数据的例子。 接下来我们再写一个案例，使用 `@RequestBody` 接收前端请求并将参数转换为后端定义的对象，在 `TestController` 类中添加的方法如下，请求方法为 POST，并使用 `@RequestBody` 注解将前端传输的参数直接转换为 SaleGoods 对象。

```java
    @RequestMapping(value = "/test/httpmessageconverter", method = RequestMethod.POST)
    @ResponseBody
    public SaleGoods httpMessageConverterTest2(@RequestBody SaleGoods saleGoods) {
        System.out.println(saleGoods.toString());
        saleGoods.setType(saleGoods.getType() + 1);
        saleGoods.setGoodsName("商品名：" + saleGoods.getGoodsName());
        return saleGoods;
    }
```

由于是 POST 请求，因此没有直接使用浏览器访问，而是在 static 中新增了 `api-test.html` 页面进行模拟请求,页面当中使用的是 `applicaiton/json` 格式进行 ajax 请求，`resources/static/api-test.html`代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>lou.SpringBoot | 请求测试</title>
  </head>
  <body class="hold-transition login-page">
    <div style="width:720px;margin:7% auto">
      <div class="content">
        <div class="container-fluid">
          <div class="row">
            <div class="col-lg-6">
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">接口测试</h5>
                </div>
                <div class="card-body">
                  <input id="id" type="text" placeholder="请输入id字段" />
                  <input
                    id="goodsName"
                    type="text"
                    placeholder="请输入goodsName字段"
                  />
                  <input
                    id="weight"
                    type="number"
                    placeholder="请输入weight字段"
                  />
                  <input id="type" type="number" placeholder="请输入type字段" />
                  <input
                    id="onSale"
                    type="number"
                    placeholder="请输入onSale字段(0或1)"
                  />
                  <h6 class="card-title">接口返回数据如下：</h6>
                  <p class="card-text" id="result2"></p>
                  <a
                    href="#"
                    class="btn btn-primary"
                    onclick="requestPostTest()"
                    >发送Post请求</a
                  >
                </div>
              </div>
              <br />
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- jQuery -->
    <script src="https://cdn.staticfile.org/jquery/1.12.0/jquery.min.js"></script>
    <script type="text/javascript">
      function requestPostTest() {
        var onSale = true;
        var id = $('#id').val();
        var weight = $('#weight').val();
        var type = $('#type').val();
        var goodsName = $('#goodsName').val();
        var onSaleValue = $('#onSale').val();
        if (onSaleValue !== 1) {
          onSale = false;
        }
        var data = {
          id: id,
          goodsName: goodsName,
          weight: weight,
          type: type,
          onSale: onSale,
        };
        $.ajax({
          type: 'POST', //方法类型
          dataType: 'json', //预期服务器返回的数据类型
          url: '/test/httpmessageconverter',
          contentType: 'application/json; charset=utf-8',
          data: JSON.stringify(data),
          success: function (result) {
            $('#result2').html(JSON.stringify(result));
          },
          error: function () {
            $('#result2').html('接口异常，请联系管理员！');
          },
        });
      }
    </script>
  </body>
</html>
```

重启项目，在地址栏访问 `/api-test.html` 页面，并在页面输入框中输入对应的数据，点击请求按钮，最终获得结果如下：

![图片描述](https://doc.shiyanlou.com/courses/uid18510-20190422-1555919382985)

前端 ajax 传输的数据是 5 个字段，到达后端后直接转换为 SaleGoods 对象。由于消息转换器的存在，对象数据的读取不仅简单而且完全正确，响应时也不用自行封装工具类，使得开发过程变得更加灵活和高效。

实验总结



本节实验讲解到此结束，为了后续实践项目的开发，这一节我们简单的介绍了如何使用 Spring Boot 进行接口开发和 web 资源的访问，希望大家参照我的步骤进行测试，本地或者实验楼的线上环境都可以进行演示和测试。

首先对 DispatchServlet 自动配置做一下介绍，让大家知道，在做 web 功能开发时其实与原来使用 SpringMVC 框架开发时类似，只不过 Spring Boot 默认是已经将其配置好而减少我们的工作量，不仅如此，Spring Boot 还做了很多自动配置使得我们能够如此方便的进行 web 项目开发。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10296timestamp1552980188255.png)

通过官方文档的介绍我们可以发现，Spring Boot 还做了如下的默认配置：

- 自动配置了 `ViewResolver` 视图解析器
- 静态资源文件夹处理
- 自动注册了大量的转换器和格式化器
- 提供了 `HttpMessageConverter` 对请求参数和返回结果进行处理
- 自动注册了 `MessageCodesResolver`
- 默认欢迎页配置
- favicon 自动配置

以上这些特性中，比较重要的内容我们都在文章中进行了讲解和实验，为了更好的掌握到这些内容，同学们也可以根据文章中的内容对接口或者配置进行修改，之后重启项目看一下访问效果是否有更改。



## 整合Thymeleaf模板引擎

认识 Thymeleaf 模板引擎

#### Thymeleaf

本次教程将对 Thymeleaf 模板引擎进行完整的介绍和实践练习，让大家能够真正的掌握这个技术进行项目的开发工作。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982682657.png)

Thymeleaf 应该是目前最受欢迎的模板引擎技术了，Spring Boot 官方也推荐 Java web 开发中使用该技术来替代 JSP 技术，主要由于其“原型即页面”的理念与 Spring Boot 倡导的快速开发非常契合，同时 Thymeleaf 模板引擎技术也确实拥有其他技术所不具备的优点。

#### Thymeleaf 3 的核心特性

Thymeleaf 于 2016 年 5 月 8 日正式发布了 thymeleaf-3.0.0.RELEASE 版本，目前的大部分项目开发过程中也是使用 Thymeleaf 3.0 及以上版本，因此在这里简单的介绍一下 Thymeleaf 3 的特性。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982704818.png)

- **完整的 HTML5 标记支持，全新的解析器**
- **自带多种模板模式，也可扩展支持其他模板格式。**
- **在 web 和非 web 环境（离线）下都可以正常工作**
- **对 Spring web 开发的支持非常完善**
- **独立于 Servlet API**
- **其他特性**:
  - Thymeleaf 3.0 引入了一种新型表达式作为一般 *Thymeleaf 标准表达*系统的一部分：_片段表达式；_。
  - Thymeleaf 3.0 中 *Thymeleaf 标准表达式* 的另一个新特性是 NO-OP（无操作）令牌，由下划线符号（`_`）表示。
  - Thymeleaf 3.0 允许在模板和模板模式下完全（和可选）将模板逻辑与模板本身*解耦*，从而实现 100％-Thymeleaf-free 无逻辑模板。
  - Thymeleaf 3.0 采用全新的方言系统。
  - Thymeleaf 3.0 完成了核心 API 的重构。

Thymeleaf 是高级语言的模板引擎，语法更简单，功能也更强大，接下来的内容将是 Thymeleaf 与 Spring Boot 的整合过程，以及 Thymeleaf 模板引擎的语法介绍

#### 引入 Thymeleaf 依赖



因为 Spring Boot 官方提供了 Thymeleaf 的场景启动器 `spring-boot-starter-thymeleaf` ，因此可以直接在 pom.xml 文件中添加该场景启动器，最终的 pom.xml 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lou.springboot</groupId>
    <artifactId>springboot-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- Thymeleaf 模板引擎依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

创建模板文件



在 `resources/templates` 目录新建模板文件 `thymeleaf.html` ，Thymeleaf 模板引擎的默认后缀名即为 html，新增文件后，首先在模板文件的 `<html>`标签中导入 Thymeleaf 的名称空间：

```xml
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

导入该名称空间主要是为了 Thymeleaf 的语法提示和 Thymeleaf 标签的使用，之后我们在模板中增加如前文 JSP 中相同的显示内容，最终的模板文件如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Thymeleaf demo</title>
  </head>
  <body>
    <p>description字段值为：</p>
    <p th:text="${description}">这里显示的是 description 字段内容</p>
  </body>
</html>
```

编辑 Controller 代码



在 controller 包下新增 ThymeleafController.java 文件，将模板文件所需的 description 字段赋值并转发至模板文件，编码如下：

```java
package com.lou.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import javax.servlet.http.HttpServletRequest;

@Controller
public class ThymeleafController {

    @GetMapping("/thymeleaf")
    public String hello(HttpServletRequest request, @RequestParam(value = "description", required = false, defaultValue = "springboot-thymeleaf") String description) {
        request.setAttribute("description", description);
        return "thymeleaf";
    }
}
```

最终的代码目录结构如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982730430.png)

启动并访问



在编码完成后，我们启动项目，由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run`，之后就可以等待项目启动。

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，我们访问 `/thymeleaf`，可以看到，原来静态 html 文件 `<p>` 标签中的内容已经替换为 "springboot-thymeleaf" 字符串，而不再是默认内容，过程如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid1375timestamp1555642908891.png)

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid1375timestamp1555643046055.png)

更换参数description的值可以得到request属性域中description不同的值

\${}能够获得不同域中的属性值​



#### th:* 属性



`th:text` 对应的是 HTML5 中的 text 属性，除 `th:text` 属性外，Thymeleaf 也提供了其他的标签属性来替换 HTML5 原生属性的值，属性节选如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10299timestamp1552982925834.png)

- `th:background` 对应 HTML5 中的背景属性
- `th:class` 对应 HTML5 中的 class 属性
- `th:href` 对应 HTML5 中的链接地址属性
- `th:id` 和 `th:name` 分别对应 HTML5 中的 id 和 name 属性...

`th:block` 比较特殊，它是 Thymeleaf 提供的唯一的一个 Thymeleaf 块级元素，其特殊性在于 Thymeleaf 模板引擎在处理`<th:block>`的时候会删掉它本身，而保留其内容。

这里只列举了部分属性，完整内容可以查看 [thymeleaf-attributes](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#setting-value-to-specific-attributes)。

修改属性值实践



接下来我们通过一个完整的编码实践来理解以上内容，同样，我们提供源码直接下载，对照源码来理解以上内容。重点关注 `attributes.html` 模板页面：

```bash
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-05.zip
unzip lou-springboot-05.zip
```

切换工作空间到 lou-springboot。

项目完整结构如下：

```bash
lou-springboot
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── lou
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               └── controller
    │   │                   └── ThymeleafController.java
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       └── templates
    │           ├── attributes.html
    │           ├── simple.html
    │           ├── test.html
    │           └── thymeleaf.html
    └── test
        └── java
            └── com
                └── lou
                    └── springboot
```

attributes.html 代码如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Thymeleaf setting-value-to-specific-attributes</title>
    <meta charset="UTF-8" />
  </head>
  <!-- background 标签-->
  <body th:background="${th_background}" background="#D0D0D0">
    <!-- text 标签-->
    <h1 th:text="${title}">html标签演示</h1>
    <div>
      <h5>id、name、value标签:</h5>
      <!-- id、name、value标签-->
      <input
        id="input1"
        name="input1"
        value="1"
        th:id="${th_id}"
        th:name="${th_name}"
        th:value="${th_value}"
      />
    </div>
    <br />
    <div class="div1" th:class="${th_class}">
      <h5>class、href标签:</h5>
      <!-- class、href标签-->
      <a th:href="${th_href}" href="##/">链接地址</a>
    </div>
  </body>
</html>
```

其中包含 `background` 、`id` 、`name` 、`class` 等 html 标签，设置默认值，并在每个标签体中添加对应的 th 标签读取动态数据，直接选择该文件，右键选择 open with -> Preview 或 Mini Browser 可查看页面效果如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid1375timestamp1555652308128.png)

需要注意的是，当前是直接打开该 html 文件并没有通过 web 服务器，此时页面能够直接正常访问且页面中的内容和元素的属性值都是默认值，之后，在 controller 包下新增对应的 Controller 方法并将请求转发至该模板页面，代码如下：

```java
// ThymeleafController.java
package com.lou.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ThymeleafController {

    @GetMapping("/attributes")
    public String attributes(ModelMap map) {
        // 更改 h1 内容
        map.put("title", "Thymeleaf 标签演示");
        // 更改 id、name、value
        map.put("th_id", "thymeleaf-input");
        map.put("th_name", "thymeleaf-input");
        map.put("th_value", "13");
        // 更改 class、href
        map.put("th_class", "thymeleaf-class");
        map.put("th_href", "http://13blog.site");
        return "attributes";
    }
}
```

在编码完成后，我们启动项目，切换到 lou-springboot 目录下通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run`，之后就可以等待项目启动。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10299timestamp1552982982218.png)

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，访问 `/attributes`，过程如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10299timestamp1552982997481.png)

由于 `th` 标签的存在，页面通过 Thymeleaf 渲染后，与静态页面相比较，内容和元素属性已经动态的切换了，这部分内容可以结合十三提供的源码进行理解，私下练习的时候也可以多尝试几个其他的常用标签

#### Thymeleaf 语法规则



首先，我们来看看 Thymeleaf 官方对于标准表达式特性的总结：

- **表达式语法**

  - 变量表达式： `${...}`
  - 选择变量表达式： `*{...}`
  - 信息表达式： `#{...}`
  - 链接 URL 表达式： `@{...}`
  - 分段表达式： `~{...}`

- **字面量**

  - 字符串： 'one text', 'Another one!' ...
  - 数字： `0`, `34`, `3.0`, `12.3` ...
  - 布尔值： `true`, `false`
  - Null 值： `null`
  - 字面量标记：`one`, `sometext`, `main` ...

- **文本运算**

  - 字符串拼接： `+`
  - 字面量置换: `|The name is ${name}|`

- **算术运算**

  - 二元运算符： `+`, `-`, `*`, `/`, `%`
  - 负号（一元运算符）： (unary operator): `-`

- **布尔运算**

  - 二元运算符： `and`, `or`
  - 布尔非（一元运算符）： `!`, `not`

- **比较运算**

  - 比较： `>`, `<`, `>=`, `<=` (`gt`, `lt`, `ge`, `le`)
  - 相等运算符： `==`, `!=` (`eq`, `ne`)

  比较运算符也可以使用转义字符，比如大于号，可以使用 Thymeleaf 语法 `gt` 也可以使用转义字符`&gt;`

- **条件运算符**

  - If-then: `(if) ? (then)`
  - If-then-else: `(if) ? (then) : (else)`
  - Default: `(value) ?: (defaultvalue)`

- **特殊语法**

  - 无操作： `_`

接下来我们将通过编码的方式将这些知识点进行实践，并将知识点进行串联以更接近实际开发而不是简单的 demo 介绍。

#### 简单语法



新建 simple.html 模板页面，该案例主要介绍字面量及简单的运算操作，包括字符串、数字、布尔值等常用的字面量及常用的运算和拼接操作，代码如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Thymeleaf simple syntax</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <h1>Thymeleaf简单语法</h1>
    <div>
      <h5>基本类型操作(字符串):</h5>
      <p>
        一个简单的字符串：<span th:text="'thymeleaf text'">default text</span>.
      </p>
      <p>
        字符串连接：<span th:text="'thymeleaf text concat,'+${thymeleafText}"
          >default text</span
        >.
      </p>
      <p>
        字符串连接：<span th:text="|thymeleaf text concat,${thymeleafText}|"
          >default text</span
        >.
      </p>
    </div>
    <div>
      <h5>基本类型操作(数字):</h5>
      <p>一个简单的神奇的数字：<span th:text="2019">1000</span>.</p>
      <p>算术运算： 2019+1=<span th:text="${number1}+1">0</span>.</p>
      <p>算术运算： 14-1=<span th:text="14-1">0</span>.</p>
      <p>算术运算： 673 * 3=<span th:text="673*${number2}">0</span>.</p>
      <p>算术运算： 39 ÷ 3=<span th:text="39/3">0</span>.</p>
    </div>
    <div>
      <h5>基本类型操作(布尔值):</h5>
      <p>
        一个简单的数字比较：2019 > 2018=<span th:text="2019&gt;2018"> </span>.
      </p>
      <p>
        字符串比较：thymeleafText == 'shiyanlou'，结果为<span
          th:text="${thymeleafText} == 'shiyanlou'"
          >0</span
        >.
      </p>
      <p>数字比较：13 == 39/3 结果为： <span th:text="13 == 39/3">0</span>.</p>
    </div>
  </body>
</html>
```

其中也有部分变量为后台设置的值，与字面量结合进行实践演示，新增 Controller 方法：

```java
    @GetMapping("/simple")
    public String simple(ModelMap map) {
        map.put("thymeleafText", "shiyanlou");
        map.put("number1", 2019);
        map.put("number2", 3);
        return "simple";
    }
```

之后可重启并查看效果，以上代码的结果如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10299timestamp1552983018444.png)

左侧为静态 html 结果，右侧为 Thymeleaf 模板引擎渲染的结果，可以看到字面量的展示及运算结果。以上为 Thymeleaf 语法中的变量使用方法和简单的运算操作，大家可以参考以上代码进行学习并适当的修改数值以尽快掌握该知识点。



```
标签属性值，不论是静态也好，获得模型封装的值也好，使用th:text做标签的文本属性配置，能够将内容替代原本静态html标签内的元素的值。在th:text表达时可以使用拼接，算数符号运算，布尔值
```



#### 表达式语法



表达式包括：变量表达式 `${...}`、选择变量表达式 `*{...}`、信息表达式 `#{...}`、链接 URL 表达式：`@{...}`、分段表达式： `~{...}`，这些表达式一般只能写在 Thymeleaf 模板文件的 `th` 标签内，不然不会生效，表达式语法的主要作用就是获取变量值、获取绑定对象的变量值、国际化变量取值、URL 拼接与生成、 Thymeleaf 模板布局，接下来我们选择一些常用的功能进行介绍和实践。

变量表达式

变量表达式即 OGNL 表达式或 Spring EL 表达式,作用是获取模板中与后端返回数据所绑定对象的值，写法为 `${...}`，这是最常见的一个表达式，在取值赋值、逻辑判断、循环语句中都可以使用该表达式，示例如下：

```html
<!-- 读取参数 -->
<p>算术运算： 2018+1=<span th:text="${number1}+1">0</span>.</p>
<!-- 读取参数并运算 -->
<div th:class="${path}=='links'?'nav-link active':'nav-link'"></div>
<!-- 读取对象中的属性 -->
<p>
  读取blog对象中title字段：<span th:text="${blog.blogTitle}">default text</span
  >.
</p>
<!-- 循环遍历 -->
<li th:each="blog : ${blogs}"></li>
```

变量表达式也可以使用内置的基本对象：

- ctx : the context object.
- vars : the context variables.
- locale : the context locale.
- request : web 环境下的 HttpServletRequest 对象.
- response :web 环境下的 HttpServletResponse 对象 .
- session : web 环境下的 HttpSession 对象.
- servletContext : web 环境下的 ServletContext 对象.

示例如下：

```html
<p>
  读取内置对象中 request 中的内容：<span
    th:text="${#request.getAttribute('requestObject')}"
    >default text</span
  >.
</p>
<p>
  读取内置对象中 session 中的内容：<span
    th:text="${#session.getAttribute('sessionObject')}"
    >default text</span
  >.
</p>
```

同时，Thymeleaf 还提供了一系列 Utility 工具对象（内置于 Context 中），可以通过 `#` 直接访问，工具类如下：

- dates ： *java.util.Date 的功能方法类。*
- calendars : *类似 #dates，面向 java.util.Calendar*
- numbers : *格式化数字的工具方法类*
- strings : *字符串对象的工具方法类，contains,startWiths,prepending/appending 等等。*
- bools：*对布尔值求值的工具方法。*
- arrays：*对数组的工具方法。*
- lists：*对 java.util.List 的工具方法*
- sets：*对 java.util.Set 的工具方法*
- maps：*对 java.util.Map 的工具方法*

你可以将这些方法视为工具类，通过这些方法可以使得 Thymeleaf 在操作变量时更加方便。

选择(星号)表达式

选择表达式与变量表达式类似，不过它会用一个预先选择的对象来代替上下文变量容器(map)来执行，语法如下： `*{blog.blogId}`，被指定的对象由 th:object 标签属性进行定义，前文中读取 blog 对象的 title 字段可以替换为：

```html
<p th:object="${blog}">
  读取blog对象中title字段：<span th:text="*{blogTitle}">text</span>.
</p>
```

如果不考虑上下文的情况下，两者没有区别，使用 `${...}`读取的内容也完全可以替换为使用`*{...}`进行读取，唯一的区别是使用`*{...}`前可以预先在父标签中通过 `th:object` 定义一个对象并进行操作。

```html
<p>
  读取blog对象中title字段：<span th:text="*{blog.blogTitle}">default text</span>
</p>
<p>读取text字段：<span th:text="*{text}">default text</span>.</p>
```

URL 表达式

`th:href` 对应的是 html 中的 href 标签，它将计算并替换 href 标签中的链接 URL 地址，`th:href` 中可以直接设置为静态地址，也可以使用表达式语法读取到的变量值进行动态拼接 URL 地址。

比如一个详情页 URL 地址：`http://localhost:8080/blog/1`，当使用 URL 表达式时，可以写成这样：

```html
<a th:href="@{'http://localhost:8080/blog/1'}">详情页</a>
```

也可以根据 id 值进行替换，写法为：

```html
<a th:href="@{'/blog/'+${blog.blogId}}">详情页</a>
```

或者也可以写成这样：

```html
<a th:href="@{/blog/{blogId}(blogId=${blog.blogId})">详情页</a>
```

以上三种表达式写法生成 URL 的最终结果都是相同的，开发者可以自己使用字符串拼接的方法组装 URL (第二种写法)，也可以使用 URL 表达式提供的语法进行 URL 组装（第三种写法）。如果有多个参数可以自行拼装字符串，或者使用逗号进行分隔，写法如下：

```html
<a
  th:href="@{/blog/{blogId}(blogId=${blog.blogId},title=${blog.blogTitle},tag='java')}"
  >详情页</a
>
```

最终生成的 URL 为 `http://localhost:8080/blog/1?title=lou-springboot&tag=java` 另外，URL 中以 "/" 开头的路径(比如 /blog/1 )，默认生成的 URL 会加上项目的当前地址形成完整的 URL 。

复杂语法



在这一小节里，我们将结合前文中的一些知识点进行更加复杂一些的语法实践，主要是后续教程中会出现的一些方法，比如判断语句、循环语句、工具类使用等等。

test.html 模板文件如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8" />
    <title th:text="${title}">语法测试</title>
  </head>
  <body>
    <h3>#strings 工具类测试</h3>
    <div th:if="${not #strings.isEmpty(testString)}">
      <p>testString初始值 : <span th:text="${testString}" /></p>
      <p>
        toUpperCase : <span th:text="${#strings.toUpperCase(testString)}" />
      </p>
      <p>
        toLowerCase : <span th:text="${#strings.toLowerCase(testString)}" />
      </p>
      <p>
        equalsIgnoreCase :
        <span th:text="${#strings.equalsIgnoreCase(testString, '13')}" />
      </p>
      <p>indexOf : <span th:text="${#strings.indexOf(testString, 'r')}" /></p>
      <p>
        substring : <span th:text="${#strings.substring(testString, 5, 9)}" />
      </p>
      <p>
        startsWith :
        <span th:text="${#strings.startsWith(testString, 'Spring')}" />
      </p>
      <p>
        contains : <span th:text="${#strings.contains(testString, 'Boot')}" />
      </p>
    </div>
    <h3>#bools 工具类测试</h3>
    <!-- 如果 bool 的值为false的话，该div将不会显示-->
    <div th:if="${#bools.isTrue(bool)}">
      <p th:text="${bool}"></p>
    </div>
    <h3>#arrays 工具类测试</h3>
    <div th:if="${not #arrays.isEmpty(testArray)}">
      <p>length : <span th:text="${#arrays.length(testArray)}" /></p>
      <p>contains : <span th:text="${#arrays.contains(testArray, 5)}" /></p>
      <p>
        containsAll :
        <span th:text="${#arrays.containsAll(testArray, testArray)}" />
      </p>
      <p>循环读取 : <span th:each="i:${testArray}" th:text="${i+' '}" /></p>
    </div>
    <h3>#lists 工具类测试</h3>
    <div th:unless="${#lists.isEmpty(testList)}">
      <p>size : <span th:text="${#lists.size(testList)}" /></p>
      <p>contains : <span th:text="${#lists.contains(testList, 0)}" /></p>
      <p>sort : <span th:text="${#lists.sort(testList)}" /></p>
      <p>循环读取 : <span th:each="i:${testList}" th:text="${i+' '}" /></p>
    </div>
    <h3>#maps 工具类测试</h3>
    <div th:if="${not #maps.isEmpty(testMap)}">
      <p>size : <span th:text="${#maps.size(testMap)}" /></p>
      <p>
        containsKey :
        <span th:text="${#maps.containsKey(testMap, 'platform')}" />
      </p>
      <p>
        containsValue : <span th:text="${#maps.containsValue(testMap, '13')}" />
      </p>
      <p>
        读取map中键为title的值 :
        <span
          th:if="${#maps.containsKey(testMap,'title')}"
          th:text="${testMap.get('title')}"
        />
      </p>
    </div>
    <h3>#dates 工具类测试</h3>
    <div>
      <p>year : <span th:text="${#dates.year(testDate)}" /></p>
      <p>month : <span th:text="${#dates.month(testDate)}" /></p>
      <p>day : <span th:text="${#dates.day(testDate)}" /></p>
      <p>hour : <span th:text="${#dates.hour(testDate)}" /></p>
      <p>minute : <span th:text="${#dates.minute(testDate)}" /></p>
      <p>second : <span th:text="${#dates.second(testDate)}" /></p>
      <p>格式化: <span th:text="${#dates.format(testDate)}" /></p>
      <p>
        yyyy-MM-dd HH:mm:ss 格式化:
        <span th:text="${#dates.format(testDate, 'yyyy-MM-dd HH:mm:ss')}" />
      </p>
    </div>
  </body>
</html>
```

增加 Controller 方法如下：

```java
    @GetMapping("/test")
    public String test(ModelMap map) {
        map.put("title", "Thymeleaf 语法测试");
        map.put("testString", "玩转 Spring Boot");
        map.put("bool", true);
        map.put("testArray", new Integer[]{2018,2019,2020,2021});
        map.put("testList", Arrays.asList("Spring", "Spring Boot", "Thymeleaf", "MyBatis", "Java"));
        Map testMap = new HashMap();
        testMap.put("platform", "shiyanlou");
        testMap.put("title", "玩转 Spring Boot");
        testMap.put("author", "十三");
        map.put("testMap", testMap);
        map.put("testDate", new Date());
        return "test";
    }
```

之后重启项目并访问 /test 即可，结果如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10299timestamp1552983034189.png)

在 strings 工具类测试中，我们首先使用了 `th:if` 标签进行逻辑判断，`th:if="${not #strings.isEmpty(testString)}"`即为一条判断语句，`${...}` 表达式中会返回一个布尔值结果，如果为 true 则该 div 中的内容会继续显示，否则将不会显示 `th:if` 所在的主标签。

`#strings.isEmpty` 的作用为字符串判空，如果 testString 为空则会返回 true，而表达式前面的 not 则表示逻辑非运算，即如果 testString 不为空则继续展示该 div 中的内容。

与 `th:if` 类似的判断标签为 `th:unless` ，它与 `th:if` 刚好相反，当表达式中返回的结果为 false 时，它所在标签中的内容才会继续显示，在 `#lists`工具类测试中我们使用了 `th:unless` ，大家在调试代码时可以比较二者的区别。

Thymeleaf 模板引擎中的循环语句语法为 `th:each="i:${testList}"` ，类似于 JSP 中的 `c:foreach` 表达式，主要是做循环的逻辑，很多页面逻辑在生成时会使用到该语法。

还有读取 Map 对象的方式为 `${testMap.get('title')}` ，与 Java 语言中也很类似。逻辑判断、循环语句这两个知识点是系统开发中比较常用也比较重要的内容，希望大家能够结合代码练习并牢牢掌握。



#### Thymeleaf 模板引擎使用注意事项



- 必须引入名称空间

  ```html
  <html lang="en" xmlns:th="http://www.thymeleaf.org"></html>
  ```

  即使不引入以上名称空间，静态资源访问以及模板动态访问都不会报错，因此有些开发者可能会忽略这个事情。但是建议在开发过程中最好引入该名称空间，因为引入之后会有 Thymeleaf 代码的语法提示，能够提升开发效率，也减少人为造成的低级错误。

  > 这里引入空间名是需要访问外网的，所有如果非会员用户运行代码效果显示不出来，请自行在本地尝试。

- 禁用模板缓存

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982767807.png)

Thymeleaf 的默认缓存设置是通过配置文件的 **spring.thymeleaf.cache**配置属性决定的，通过如上 Thymeleaf 模板的配置属性类 ThymeleafProperties 可以发现该属性默认为 true，因此 Thymeleaf 默认是使用模板缓存的，该设置有助于改善应用程序的性能，因为模板只需编译一次即可，但是在开发过程中不能实时看到页面变更的效果，除非重启应用程序，因此建议将该属性设置为 false，在配置文件中修改如下：

```xml
spring.thymeleaf.cache=false
```

- IDEA 中通过 Thymeleaf 语法读取变量时爆红色波浪线问题

  如下图所示，在刚开始使用 Thymeleaf 开发时可能会碰到这种问题，在模板文件中通过 Thymeleaf 语法读取变量时，该变量名称下会出现红色波浪线，也就是错误的标志。

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982790597.png)

  如果不熟的朋友可能会人为自己的模板文件有问题，但其实并不是那么严重，只是由于 IDEA 中默认开启了表达式参数验证，即使在后端的 model 数据中添加了该变量，但是对于前端文件是无法感知的，因此会报这个错误，可以在 IDEA 中默认将验证关闭或者将提示级别修改掉也可以。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982808053.png)

