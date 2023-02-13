# Spring Boot搭建第一个应用程序

Spring Boot是Spring平台的约定式的应用框架，使用Spring Boot可以更加方便简洁的开发基于Spring的应用程序。

## 依赖配置

使用Maven来做包的依赖管理，在pom.xml文件中我们需要添加Spring boot依赖：

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

同时我们要构建一个web应用程序，所以需要添加web依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

OOM框架，我们使用spring自带的jpa，数据库使用内存数据库H2：

```java
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
```

## main程序配置

接下来我们需要创建一个应用程序的主类：

```java
@SpringBootApplication
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

}
```

这里我们使用了注解： @SpringBootApplication。 它等同于三个注解：@Configuration, @EnableAutoConfiguration, 和 @ComponentScan同时使用。

最后，我们需要在resources目录中添加属性文件：application.properties。 在其中我们定义程序启动的端口：

```properties
server.port=8081
```

## MVC配置

spring MVC可以配合很多模板语言使用，这里我们使用Thymeleaf。

首先需要添加依赖：

```xml
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-thymeleaf</artifactId> 
</dependency>
```

然后在application.properties中添加如下配置：

```properties
spring.thymeleaf.cache=false
spring.thymeleaf.enabled=true
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html

spring.application.name=Bootstrap Spring Boot
```

然后创建一个home页面：

```html
<html>
<head><title>Home Page</title></head>
<body>
<h1>Hello !</h1>
<p>Welcome to <span th:text="${appName}">Our App</span></p>
</body>
</html>
```

最后创建一个Controller指向这个页面：

```java
@Controller
public class SimpleController {
    @Value("${spring.application.name}")
    String appName;

    @GetMapping("/")
    public String homePage(Model model) {
        model.addAttribute("appName", appName);
        return "home";
    }
}
```

## 安全配置

本例主要是搭一个基本完整的框架，所以必须的安全访问控制也是需要的。我们使用Spring Security来做安全控制，加入依赖如下：

```xml
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-security</artifactId> 
</dependency>
```

当spring-boot-starter-security加入依赖之后，应用程序所有的入库会被默认加入权限控制，在本例中，我们还用不到这些权限控制，所以需要自定义SecurityConfig，放行所有的请求：

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .anyRequest()
            .permitAll()
            .and().csrf().disable();
    }
}
```

## 存储

本例中，我们定义一个Book类，那么需要定义相应的Entity类：

```java
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    @Column(nullable = false, unique = true)
    private String title;

    @Column(nullable = false)
    private String author;
}
```

和相应的Repository类：

```java
public interface BookRepository extends CrudRepository<Book, Long> {
    List<Book> findByTitle(String title);
}
```

最后，我们需要让应用程序发现我们配置的存储类，如下：

```java
@EnableJpaRepositories("com.flydean.learn.repository")
@EntityScan("com.flydean.learn.entity")
@SpringBootApplication
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

}
```

这里，我们使用@EnableJpaRepositories 来扫描repository类。

使用@EntityScan来扫描JPA entity类。

为了方便起见，我们使用内存数据库H2. 一旦H2在依赖包里面，Spring boot会自动检测到，并使用它。 我们需要配置一些H2的属性：

```java
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:bootapp;DB_CLOSE_DELAY=-1
spring.datasource.username=sa
spring.datasource.password=
```

和安全一样，存储也是一个非常重要和复杂的课题，我们也会在后面的文章中讨论。

## Web 页面和Controller

有了Book entity， 我们需要为Book写一个Controller，主要做增删改查的操作，如下所示：

```java
@RestController
@RequestMapping("/api/books")
public class BookController {

    @Autowired
    private BookRepository bookRepository;

    @GetMapping
    public Iterable findAll() {
        return bookRepository.findAll();
    }

    @GetMapping("/title/{bookTitle}")
    public List findByTitle(@PathVariable String bookTitle) {
        return bookRepository.findByTitle(bookTitle);
    }

    @GetMapping("/{id}")
    public Book findOne(@PathVariable Long id) {
        return bookRepository.findById(id)
                .orElseThrow(BookNotFoundException::new);
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Book create(@RequestBody Book book) {
        return bookRepository.save(book);
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        bookRepository.findById(id)
                .orElseThrow(BookNotFoundException::new);
        bookRepository.deleteById(id);
    }

    @PutMapping("/{id}")
    public Book updateBook(@RequestBody Book book, @PathVariable Long id) {
        if (book.getId() != id) {
            throw new BookIdMismatchException("ID mismatch!");
        }
        bookRepository.findById(id)
                .orElseThrow(BookNotFoundException::new);
        return bookRepository.save(book);
    }
}
```

这里我们使用@RestController 注解，表示这个Controller是一个API，不涉及到页面的跳转。

@RestController是@Controller 和 @ResponseBody 的集合。

## 异常处理

基本上我们的程序已经完成了，但是在Controller中，我们定义了一些自定义的异常：

```java
public class BookNotFoundException extends RuntimeException {

    public BookNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
    // ...
}
```

那么怎么处理这些异常呢？我们可以使用@ControllerAdvice来拦截这些异常：

```java
@ControllerAdvice
public class RestExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler({ BookNotFoundException.class })
    protected ResponseEntity<Object> handleNotFound(
      Exception ex, WebRequest request) {
        return handleExceptionInternal(ex, "Book not found", 
          new HttpHeaders(), HttpStatus.NOT_FOUND, request);
    }

    @ExceptionHandler({ BookIdMismatchException.class, 
      ConstraintViolationException.class, 
      DataIntegrityViolationException.class })
    public ResponseEntity<Object> handleBadRequest(
      Exception ex, WebRequest request) {
        return handleExceptionInternal(ex, ex.getLocalizedMessage(), 
          new HttpHeaders(), HttpStatus.BAD_REQUEST, request);
    }
}
```

这种异常捕获也叫做全局异常捕获。

## 测试

我们的Book API已经写好了，接下来我们需要写一个测试程序来测试一下。

这里我们使用@SpringBootTest ：

```java
@Slf4j
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
public class SpringContextTest {

    @Test
    public void contextLoads() {
        log.info("contextLoads");
    }
}
```

webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT的作用是表示测试时候使用的Spring boot应用程序端口使用自定义在application.properties中的端口。

接下来我们使用RestAssured来测试BookController：

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
public class SpringBootBootstrapTest {

    private static final String API_ROOT
            = "http://localhost:8081/api/books";

    private Book createRandomBook() {
        Book book = new Book();
        book.setTitle(randomAlphabetic(10));
        book.setAuthor(randomAlphabetic(15));
        return book;
    }

    private String createBookAsUri(Book book) {
        Response response = RestAssured.given()
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .body(book)
                .post(API_ROOT);
        return API_ROOT + "/" + response.jsonPath().get("id");
    }


    @Test
    public void whenGetAllBooks_thenOK() {
        Response response = RestAssured.get(API_ROOT);

        assertEquals(HttpStatus.OK.value(), response.getStatusCode());
    }

    @Test
    public void whenGetBooksByTitle_thenOK() {
        Book book = createRandomBook();
        createBookAsUri(book);
        Response response = RestAssured.get(
                API_ROOT + "/title/" + book.getTitle());

        assertEquals(HttpStatus.OK.value(), response.getStatusCode());
        assertTrue(response.as(List.class)
                .size() > 0);
    }
    @Test
    public void whenGetCreatedBookById_thenOK() {
        Book book = createRandomBook();
        String location = createBookAsUri(book);
        Response response = RestAssured.get(location);

        assertEquals(HttpStatus.OK.value(), response.getStatusCode());
        assertEquals(book.getTitle(), response.jsonPath()
                .get("title"));
    }

    @Test
    public void whenGetNotExistBookById_thenNotFound() {
        Response response = RestAssured.get(API_ROOT + "/" + randomNumeric(4));

        assertEquals(HttpStatus.NOT_FOUND.value(), response.getStatusCode());
    }

    @Test
    public void whenCreateNewBook_thenCreated() {
        Book book = createRandomBook();
        Response response = RestAssured.given()
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .body(book)
                .post(API_ROOT);

        assertEquals(HttpStatus.CREATED.value(), response.getStatusCode());
    }

    @Test
    public void whenInvalidBook_thenError() {
        Book book = createRandomBook();
        book.setAuthor(null);
        Response response = RestAssured.given()
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .body(book)
                .post(API_ROOT);

        assertEquals(HttpStatus.BAD_REQUEST.value(), response.getStatusCode());
    }

    @Test
    public void whenUpdateCreatedBook_thenUpdated() {
        Book book = createRandomBook();
        String location = createBookAsUri(book);
        book.setId(Long.parseLong(location.split("api/books/")[1]));
        book.setAuthor("newAuthor");
        Response response = RestAssured.given()
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .body(book)
                .put(location);

        assertEquals(HttpStatus.OK.value(), response.getStatusCode());

        response = RestAssured.get(location);

        assertEquals(HttpStatus.OK.value(), response.getStatusCode());
        assertEquals("newAuthor", response.jsonPath()
                .get("author"));
    }

    @Test
    public void whenDeleteCreatedBook_thenOk() {
        Book book = createRandomBook();
        String location = createBookAsUri(book);
        Response response = RestAssured.delete(location);

        assertEquals(HttpStatus.OK.value(), response.getStatusCode());

        response = RestAssured.get(location);
        assertEquals(HttpStatus.NOT_FOUND.value(), response.getStatusCode());
    }
}
```

# Spring boot中修改默认端口

## 介绍

Spring boot为应用程序提供了很多属性的默认值。但是有时候，我们需要自定义某些属性，比如：修改内嵌服务器的端口号。

## 使用Property文件

第一种方式，也是最常用的方式就是在属性文件中，覆盖默认的配置。对于服务器的端口来说，该配置就是：`server.port`。

默认情况下，server.port值是8080。 我们可以在application.properties中这样修改为8081:

```properties
server.port=8081
```

如果你使用的是application.yml，那么需要这样配置：

```yml
server:
  port : 8081
```

这两个文件都会在Spring boot启动的时候被加载。

如果同一个应用程序需要在不同的环境中使用不同的端口，这个时候你就需要使用到Spring Boot的profile概念，不同的`profile`使用不同的配置文件。

比如你在application-dev.properties中：

```properties
server.port=8081
```

在application-qa.properties 中：

```properties
server.port=8082
```

## 在程序中指定

我们可以在程序中直接指定应用程序的端口，如下所示：

```java
@SpringBootApplication
public class CustomApplication {
    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(CustomApplication.class);
        app.setDefaultProperties(Collections
          .singletonMap("server.port", "8083"));
        app.run(args);
    }
}
```

另外一种自定义服务的方法就是实现WebServerFactoryCustomizer接口：

```java
@Component
public class ServerPortCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {
    @Override
    public void customize(ConfigurableWebServerFactory factory) {
        factory.setPort(8086);
//        factory.setAddress("");
    }
}
```

使用ConfigurableWebServerFactory可以自定义包括端口在内的其他很多服务器属性。

## 使用命令行参数

如果应用程序被打包成jar，我们也可以在命令行运行时候，手动指定 server.port 。

```shell
java -jar spring-5.jar --server.port=8083
```

或者这样：

```shell
java -jar -Dserver.port=8083 spring-5.jar
```

## 值生效的顺序

上面我们将了这么多修改自定义端口的方式，那么他们的生效顺序是怎么样的呢？

1. 内置的server配置
2. 命令在参数
3. property文件
4. @SpringBootApplication配置的主函数

# Spring Boot Starters介绍

对于任何一个复杂项目来说，依赖关系都是一个非常需要注意和消息的方面，虽然重要，但是我们也不需要花太多的时间在上面，因为依赖毕竟只是框架，我们重点需要关注的还是程序业务本身。

这就是为什么会有Spring Boot starters的原因。Starter POMs 是一系列可以被引用的依赖集合，只需要引用一次就可以获得所有需要使用到的依赖。

Spring Boot有超过30个starts, 本文将介绍比较常用到的几个。

## Web Start

如果我们需要开发MVC程序或者REST服务，那么我们需要使用到Spring MVC，Tomcat,JSON等一系列的依赖。但是使用Spring Boot Start,一个依赖就够了：

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

现在我们就可以创建REST Controller了：

```java
@RestController
public class GenericEntityController {
    private List<GenericEntity> entityList = new ArrayList<>();

    @RequestMapping("/entity/all")
    public List<GenericEntity> findAll() {
        return entityList;
    }

    @RequestMapping(value = "/entity", method = RequestMethod.POST)
    public GenericEntity addEntity(GenericEntity entity) {
        entityList.add(entity);
        return entity;
    }

    @RequestMapping("/entity/findby/{id}")
    public GenericEntity findById(@PathVariable Long id) {
        return entityList.stream().
                filter(entity -> entity.getId().equals(id)).
                findFirst().get();
    }
}

```

这样我们就完成了一个非常简单的Spring Web程序。

## Test Starter

在测试中，我们通常会用到Spring Test, JUnit, Hamcrest, 和 Mockito这些依赖，Spring也有一个starter集合：

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

> *注意，你并不需要指定artifact的版本号，因为这一切都是从spring-boot-starter-parent 的版本号继承过来的。后面升级的话，只需要升级parent的版本即可。具体的应用可以看下本文的例子。*

接下来让我们测试一下刚刚创建的controller：

这里我们使用mock。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
@WebAppConfiguration
public class SpringBootApplicationIntegrationTest {
    @Autowired
    private WebApplicationContext webApplicationContext;
    private MockMvc mockMvc;

    @Before
    public void setupMockMvc() {
        mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
    }

    @Test
    public void givenRequestHasBeenMade_whenMeetsAllOfGivenConditions_thenCorrect()
            throws Exception {
        MediaType contentType = new MediaType(MediaType.APPLICATION_JSON.getType(),
                MediaType.APPLICATION_JSON.getSubtype(), Charset.forName("utf8"));
        mockMvc.perform(MockMvcRequestBuilders.get("/entity/all")).
                andExpect(MockMvcResultMatchers.status().isOk()).
                andExpect(MockMvcResultMatchers.content().contentType(contentType)).
                andExpect(jsonPath("$", hasSize(4)));
    }
}
```

上面的例子，我们测试了/entity/all接口，并且验证了返回的JSON。

这里@WebAppConfiguration 和 MockMVC 是属于 spring-test 模块, hasSize 是一个Hamcrest 的匹配器, @Before 是一个 JUnit 注解.所有的一切，都包含在一个starter中。

## Data JPA Starter

如果想使用JPA，我们可以这样：

```java
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
```

我们接下来创建一个repository：

```java
public interface GenericEntityRepository extends JpaRepository<GenericEntity, Long> {}
```

然后是JUnit测试：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
public class SpringBootJPATest {

    @Autowired
    private GenericEntityRepository genericEntityRepository;

    @Test
    public void givenGenericEntityRepository_whenSaveAndRetreiveEntity_thenOK() {
        GenericEntity genericEntity =
                genericEntityRepository.save(new GenericEntity("test"));
        GenericEntity foundedEntity =
                genericEntityRepository.findById(genericEntity.getId()).orElse(null);

        assertNotNull(foundedEntity);
        assertEquals(genericEntity.getValue(), foundedEntity.getValue());
    }
}
```

这里我们测试了JPA自带的save, findById方法。 可以看到我们没有做任何的配置，Spring boot自动帮我们完成了所有操作。

## Mail Starter

在企业开发中，发送邮件是一件非常常见的事情，如果直接使用 Java Mail API会比较复杂。如果使用Spring boot:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

这样我们就可以直接使用JavaMailSender，前提是需要配置mail的连接属性如下：

```properties
spring.mail.host=localhost
spring.mail.port=25
spring.mail.default-encoding=UTF-8
```

接下来我们来写一些测试案例。

为了发送邮件，我们需要一个简单的`SMTP`服务器。在本例中，我们使用Wiser。

```xml
<dependency>
    <groupId>org.subethamail</groupId>
    <artifactId>subethasmtp</artifactId>
    <version>3.1.7</version>
    <scope>test</scope>
</dependency>
```

下面是如何发送的代码：

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class SpringBootMailTest {
    @Autowired
    private JavaMailSender javaMailSender;

    private Wiser wiser;

    private String userTo = "user2@localhost";
    private String userFrom = "user1@localhost";
    private String subject = "Test subject";
    private String textMail = "Text subject mail";

    @Before
    public void setUp() throws Exception {
        final int TEST_PORT = 25;
        wiser = new Wiser(TEST_PORT);
        wiser.start();
    }

    @After
    public void tearDown() throws Exception {
        wiser.stop();
    }

    @Test
    public void givenMail_whenSendAndReceived_thenCorrect() throws Exception {
        SimpleMailMessage message = composeEmailMessage();
        javaMailSender.send(message);
        List<WiserMessage> messages = wiser.getMessages();

        assertThat(messages, hasSize(1));
        WiserMessage wiserMessage = messages.get(0);
        assertEquals(userFrom, wiserMessage.getEnvelopeSender());
        assertEquals(userTo, wiserMessage.getEnvelopeReceiver());
        assertEquals(subject, getSubject(wiserMessage));
        assertEquals(textMail, getMessage(wiserMessage));
    }

    private String getMessage(WiserMessage wiserMessage)
            throws MessagingException, IOException {
        return wiserMessage.getMimeMessage().getContent().toString().trim();
    }

    private String getSubject(WiserMessage wiserMessage) throws MessagingException {
        return wiserMessage.getMimeMessage().getSubject();
    }

    private SimpleMailMessage composeEmailMessage() {
        SimpleMailMessage mailMessage = new SimpleMailMessage();
        mailMessage.setTo(userTo);
        mailMessage.setReplyTo(userFrom);
        mailMessage.setFrom(userFrom);
        mailMessage.setSubject(subject);
        mailMessage.setText(textMail);
        return mailMessage;
    }
}
```

在上面的例子中，@Before 和 @After 分别用来启动和关闭邮件服务器。

# Spring boot自定义parent POM

## 概述

在之前的Spring Boot例子中，我们都会用到这样的parent POM。

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

这个parent指定了spring-boot所需要的依赖。但是有时候如果我们的项目已经有一个parent了，这时候需要引入spring boot该怎么处理呢？

## 不使用Parent POM来引入Spring boot

parent pom.xml主要处理的是依赖和使用的插件管理。使用起来会非常简单，这也是我们在Spring boot中常用的方式。

在实际中，如果我们因为种种原因，不能使用Spring boot自带的parent,那么我们可以这样做：

```java
<dependencyManagement>
     <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.2.2.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

将spring-boot-dependencies作为一个依赖放入dependencyManagement标签即可。注意，这里的scope要使用`import`。

接下来，我们就可以随意使用spring boot的依赖了，例如：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

另一方面，如果不使用parent POM，Spring boot自带的plugin，需要我们自己引入：

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

## 覆盖依赖项版本

如果我们需要使用和parent POM中定义的不同的依赖项版本，则可以在

dependencyManagement中重写。

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
            <version>1.5.5.RELEASE</version>
        </dependency>
    </dependencies>
    // ...
</dependencyManagement>
```

当然，你也可以在每次引入依赖的时候，指定所需要的版本。

# 使用spring boot创建fat jar APP

## 介绍

在很久很很久以前，我们部署web程序的方式是怎么样的呢？配置好服务器，将自己写的应用程序打包成war包，扔进服务器中指定的目录里面。当然免不了要配置一些负责的xml和自定义一些servlet。

现在有了spring boot，一切都变了，我们可以将web应用程序打包成fat jar包，直接运行就行了。

添加如下依赖：

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
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
```

## build和run

有了上面的配置，只需要使用

```shell
mvn clean install
```

 就可以生成相应的jar包了。

如果要运行它，使用：

```shell
java -jar <artifact-name>
```

即可。非常简洁。

如果你要在服务器上面永久运行该服务，即使登录的用户退出服务器，则可以使用nohup命令：

```shell
nohup java -jar <artifact-name>
```

## fat jar和 fat war

在上面的例子中，所有的依赖jar包都会被打包进入这一个fat jar中，如果你使用了tomcat,那么tomcat也会被打包进去。

但有时候我们还是需要打包成war包，部署在服务器中，这种情况只需要将pom.xml中的packaging属性修改为war即可。

## 更多配置

大多情况下，我们不需要额外的配置，如果我们有多个main class,我们需要指定具体的哪个类：

```xml
    <properties>
        <start-class>com.flydean.FatJarApp</start-class>
    </properties>
```

如果你没有从spring-boot-starter-parent继承，那么你需要将main class添加到maven plugin中：

```xml
       <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.flydean.FatJarApp</mainClass>
                    <layout>ZIP</layout>
                </configuration>
            </plugin>
        </plugins>
```

有些情况下，你需要告诉maven来unpack一些依赖：

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <requiresUnpack>
            <dependency>
                <groupId>org.jruby</groupId>
                <artifactId>jruby-complete</artifactId>
            </dependency>
        </requiresUnpack>
    </configuration>
</plugin>
```

# spring boot 使用maven和fat jar/war运行应用程序的对比

## 简介

上一篇文章我们介绍了Spring boot的fat jar/war包，jar/war包都可以使用 java -jar 命令来运行，而maven也提供了mvn spring-boot:run 命令来运行应用程序，下面来看看两者有什么不同。

## Spring Boot Maven Plugin

上篇文章我们提到了Spring Boot Maven Plugin，通过使用该插件，可以有效的提高部署效率，并打包成为fat jar/war包。

在打包成fat jar/war包的时候，背后实际上做了如下的事情：

1. 管理了classpath的配置，这样我们在运行java -jar的时候不用手动指定-cp 。
2. 使用了自定义的ClassLoader来加载和定位所有的外部jar包依赖。并且所有的依赖jar包已经被包含在这个fat包里面了。
3. 通过manifest自动查找main() ，这样我们就不需要在java -jar中手动指定main方法。

## 使用Maven命令来运行应用程序

要使用maven命令来运行应用程序可以在程序的根目录下面执行

```shell
mvn spring-boot:run
```

它会自动下载所需要的依赖，并运行，运行日志如下：

```shell
mvn spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] -------------------< com.flydean:springboot-fatjar >--------------------
[INFO] Building springboot-fatjar 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] >>> spring-boot-maven-plugin:2.2.2.RELEASE:run (default-cli) > test-compile @ springboot-fatjar >>>
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ springboot-fatjar ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO] Copying 0 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ springboot-fatjar ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ springboot-fatjar ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.

[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ springboot-fatjar ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] <<< spring-boot-maven-plugin:2.2.2.RELEASE:run (default-cli) < test-compile @ springboot-fatjar <<<
[INFO] 
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.2.2.RELEASE:run (default-cli) @ springboot-fatjar ---
[INFO] Attaching agents: []
```

## 作为fat jar/war包运行应用程序

如果想打包成fat jar/war, 需要使用Maven Spring Boot plugin，如下所示，否则打包出来的jar包并不包含外部依赖：

```xml
<build>
    <plugins>
        ...
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        ...
    </plugins>
</build>
```

如果我们的代码包含了多个main class， 需要手动指定具体使用哪一个， 有两种设置方式：

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <configuration>
                <mainClass>com.flydean.FatJarApp</mainClass>
            </configuration>
        </execution>
    </executions>
</plugin>
```

或者设置star-class属性：

```xml
<properties>
    <start-class>com.flydean.FatJarApp</start-class>
</properties>
```

使用 mvn clean package 即可打包程序，然后使用java -jar target/springboot-fatwar-0.0.1-SNAPSHOT.war即可运行。

## 详解War文件

将打包好的war文件解压，我们看下War文件的结构：

<img src="/img/image-20220717193231181.png" alt="image-20220717193231181" style="zoom:80%;" />

里面有三部分：

- META-INF, 里面包含有自动生成的MANIFEST.MF
- WEB-INF/classes, 包含了编译好的class文件

- WEB-INF/lib,包含了war的依赖jar包和嵌入的Tomcat jar包。

- WEB-INF/lib-provided,包含了embedded模式运行所需要但是在部署模式不需要的额外的依赖包。

- org/springframework/boot/loader,里面是Spring boot自定义的类加载器，这些类加载器负责加载外部依赖，并且使他们在运行时可用。

我们再看下MANIFEST.MF文件的内容：

```makefile
Manifest-Version: 1.0
Implementation-Title: springboot-fatwar
Implementation-Version: 0.0.1-SNAPSHOT
Start-Class: com.flydean.FatWarApp
Spring-Boot-Classes: WEB-INF/classes/
Spring-Boot-Lib: WEB-INF/lib/
Build-Jdk-Spec: 1.8
Spring-Boot-Version: 2.2.2.RELEASE
Created-By: Maven Archiver 3.4.0
Main-Class: org.springframework.boot.loader.WarLauncher
```

主要关注两行：

```makefile
Start-Class: com.flydean.FatWarApp
Main-Class: org.springframework.boot.loader.WarLauncher
```

一个是启动类就是我们自己写的，一个是main类这个是Spring boot自带的。

## 详解jar文件

我们再来看下jar文件：

<img src="/img/image-20220717193650399.png" alt="image-20220717193650399" style="zoom:80%;" />

jar文件和war文件有一点不同，没有WEB-INF，改成了BOOT-INF。

- 我们所有的自己的class都在BOOT-INF/classes下面。
- 外部依赖在BOOT-INF/lib下。

我们再看下MANIFEST.MF文件有什么不同：

```makefile
Manifest-Version: 1.0
Implementation-Title: springboot-fatjar
Implementation-Version: 0.0.1-SNAPSHOT
Start-Class: com.flydean.FatJarApp
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Build-Jdk-Spec: 1.8
Spring-Boot-Version: 2.2.2.RELEASE
Created-By: Maven Archiver 3.4.0
Main-Class: org.springframework.boot.loader.PropertiesLauncher
```

我们可以看到Start-Class还是一样的，但是Main-Class是不一样的。

## 如何选择

既然有两种方式来运行应用程序，一种是使用mvn命令，一种是使用fat jar/war文件，那我们该怎么选择呢？

通常情况下，如果我们是在线下的开发环境，可以直接使用mvn命令，mvn命令需要依赖于源代码，我们可以不断的修改源代码，方便开发。

如果是在线上环境，那么我们就需要使用fat jar/war了，这样的外部依赖比较小，我们不需要在线上环境部署maven环境，也不需要源代码，只要一个java的运行时环境就可以了。

# Spring Boot注解

## 简介

Spring Boot通过自动配置让我们更加简单的使用Spring。在本文中我们将会介绍`org.springframework.boot.autoconfigure `和`org.springframework.boot.autoconfigure.condition `里面经常会用到的一些注解。

## @SpringBootApplication

```java
@SpringBootApplication
public class AnotationApp {
    public static void main(String[] args) {
        SpringApplication.run(AnotationApp.class, args);
    }
}
```

@SpringBootApplication被用在Spring Boot应用程序的Main class中，表示整个应用程序是Spring Boot。

@SpringBootApplication实际上是@Configuration, @EnableAutoConfiguration 和 @ComponentScan 的集合。

## @EnableAutoConfiguration

@EnableAutoConfiguration 意味着开启了自动配置。这意味着Spring Boot会去在classpath中查找自动配置的beans，并且自动应用他们。

注意， @EnableAutoConfiguration需要和@Configuration配合使用。

```java
@Configuration
@EnableAutoConfiguration
public class VehicleFactoryConfig {
}
```

## 条件自动配置

有时候，我们在自定义自动配置的时候，希望根据某些条件来开启自动配置，Spring Boot 提供了一些有用的注解。

这些注解可以和@Configuration 类 或者 @Bean 方法一起使用。

* @ConditionalOnClass 和 @ConditionalOnMissingClass

这两个注解的意思是，如果注解参数中的类存在或者不存在,存在则Spring会去实例化自动配置的bean。

```java
@Configuration
@ConditionalOnClass(DataSource.class)
public class MySQLAutoconfiguration {
}
```

* @ConditionalOnBean 和 @ConditionalOnMissingBean

这两个和上面的区别在于，这两个是判断是否有实例化的bean存在。

```java
 @Bean
@ConditionalOnBean(name = "dataSource")
LocalContainerEntityManagerFactoryBean entityManagerFactory() {
    // ...
}
```

* @ConditionalOnProperty

使用这个注解我们可以判断Property的某些属性是不是需要的值：

```java
@Bean
@ConditionalOnProperty(
    name = "usemysql", 
    havingValue = "local"
)
DataSource dataSource() {
    // ...
}
```

* @ConditionalOnResource

只有当某些resource存在的时候，才会起作用。

```java
@ConditionalOnResource(resources = "classpath:mysql.properties")
Properties additionalProperties() {
    // ...
}
```

* @ConditionalOnWebApplication 和 @ConditionalOnNotWebApplication

这两个注解通过判断是否web应用程序。

```java
    @Bean
    @ConditionalOnWebApplication
    HealthCheckController healthCheckController() {
        // ...
        return null;
    }
```

* @ConditionalExpression

这个注解可以使用SpEL构造更加复杂的表达式：

```java
@Bean
@ConditionalOnExpression("{usemysql} &&{mysqlserver == 'local'}")
DataSource dataSource() {
    // ...
}
```

* @Conditional

还有一种更加复杂的应用叫@Conditional，它的参数是一个自定义的condition类。

```java
    @Bean
    @Conditional(HibernateCondition.class)
    Properties newAdditionalProperties() {
        //...
        return null;
    }
public class HibernateCondition implements Condition {
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        return false;
    }
}
```

这个类需要实现matches方法。

# Spring Boot @EnableAutoConfiguration和 @Configuration的区别

在Spring Boot中，我们会使用@SpringBootApplication来开启Spring Boot程序。在之前的文章中我们讲到了@SpringBootApplication相当于@EnableAutoConfiguration，@ComponentScan，@Configuration三者的集合。

其中@Configuration用在类上面，表明这个是个配置类，如下所示：

```java
@Configuration
public class MySQLAutoconfiguration {
    ...
}
```

而@EnableAutoConfiguration则是开启Spring Boot的自动配置功能。什么是自动配置功能呢？简单点说就是Spring Boot根据依赖中的jar包，自动选择实例化某些配置。

接下来我们看一下@EnableAutoConfiguration是怎么工作的。

先看一下@EnableAutoConfiguration的定义：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    /**
     * Exclude specific auto-configuration classes such that they will never be applied.
     * @return the classes to exclude
     */
    Class<?>[] exclude() default {};

    /**
     * Exclude specific auto-configuration class names such that they will never be
     * applied.
     * @return the class names to exclude
     * @since 1.3.0
     */
    String[] excludeName() default {};

}
```

注意这一行： @Import(AutoConfigurationImportSelector.class)

AutoConfigurationImportSelector实现了ImportSelector接口，并会在实例化时调用selectImports。下面是其方法：

```java
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        if (!isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        }
        AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
                .loadMetadata(this.beanClassLoader);
        AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(autoConfigurationMetadata,
                annotationMetadata);
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
```

这个方法中的getCandidateConfigurations会从类加载器中查找所有的META-INF/spring.factories，并加载其中实现了@EnableAutoConfiguration的类。 有兴趣的朋友可以具体研究一下这个方法的实现。

```java
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
        MultiValueMap<String, String> result = cache.get(classLoader);
        if (result != null) {
            return result;
        }

        try {
            Enumeration<URL> urls = (classLoader != null ?
                    classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
                    ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
            result = new LinkedMultiValueMap<>();
            while (urls.hasMoreElements()) {
                URL url = urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                for (Map.Entry<?, ?> entry : properties.entrySet()) {
                    String factoryTypeName = ((String) entry.getKey()).trim();
                    for (String factoryImplementationName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
                        result.add(factoryTypeName, factoryImplementationName.trim());
                    }
                }
            }
            cache.put(classLoader, result);
            return result;
        }
        catch (IOException ex) {
            throw new IllegalArgumentException("Unable to load factories from location [" +
                    FACTORIES_RESOURCE_LOCATION + "]", ex);
        }
    }
```

我们再看一下spring-boot-autoconfigure-2.2.2.RELEASE.jar中的META-INF/spring.factories。

spring.factories里面的内容是key=value形式的，我们重点关注一下EnableAutoConfiguration：

```java
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
...
```

这里只列出了一部分内容，根据上面的代码， 所有的EnableAutoConfiguration的实现都会被自动加载。这就是自动加载的原理了。

如果我们仔细去看具体的实现：

```java
@Configuration(proxyBeanMethods = false)
@AutoConfigureAfter(JmxAutoConfiguration.class)
@ConditionalOnProperty(prefix = "spring.application.admin", value = "enabled", havingValue = "true",
        matchIfMissing = false)
public class SpringApplicationAdminJmxAutoConfiguration {
```

可以看到里面使用了很多@Conditional*** 的注解，这种注解的作用就是判断该配置类在什么时候能够起作用。

# 自定义spring boot的自动配置

上篇文章我们讲了spring boot中自动配置的深刻含义和内部结构，这篇文章我们讲一下怎么写出一个自己的自动配置。为了方便和通用起见，这篇文章将会实现一个mysql数据源的自动配置。

## 添加Maven依赖

我们需要添加mysql和jpa的数据源：

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.18</version>
        </dependency>
    </dependencies>
```

## 创建自定义 Auto-Configuration

我们知道 Auto-Configuration实际上就是一种配置好的@Configuration,所以我们需要创建一个MySQL 的@Configuration， 如下：

```java
@Configuration
public class MySQLAutoconfiguration {
}
```

下一步就是将这个配置类注册到resources下面的/META-INF/spring.factories作为org.springframework.boot.autoconfigure.EnableAutoConfiguration的一个实现：

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.flydean.config.MySQLAutoconfiguration
```

如果我们希望自定义的@Configuration拥有最高的优先级，我们可以添加@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE) 如下所示：

```java
@Configuration
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
public class MySQLAutoconfiguration {
}
```

> *注意， 自动配置的bean只有在该bean没有在应用程序中配置的时候才会自动被配置。如果应用程序中已经配置了该bean，则自动配置的bean会被覆盖。*

### Class Conditions

我们的mysqlConfig只有在DataSource这个类存在的时候才会被自动配置。则可以使用@ConditionalOnClass。 如果某个类不存在的时候生效则可以使用@ConditionalOnMissingClass。

```java
@Configuration
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
@ConditionalOnClass(DataSource.class)
public class MySQLAutoconfiguration {
}
```

### bean Conditions

如果我们需要的不是类而是bean的实例，则可以使用@ConditionalOnBean 和 @ConditionalOnMissingBean。

在本例中，我们希望当dataSource的bean存在的时候实例化一个LocalContainerEntityManagerFactoryBean：

```java
   @Bean
    @ConditionalOnBean(name = "dataSource")
    @ConditionalOnMissingBean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
        LocalContainerEntityManagerFactoryBean em
                = new LocalContainerEntityManagerFactoryBean();
        em.setDataSource(dataSource());
        em.setPackagesToScan("com.flydean.config.example");
        em.setJpaVendorAdapter(new HibernateJpaVendorAdapter());
        if (additionalProperties() != null) {
            em.setJpaProperties(additionalProperties());
        }
        return em;
    }
```

同样的，我们可以定义一个transactionManager， 只有当JpaTransactionManager不存在的时候才创建：

```java
@Bean
@ConditionalOnMissingBean(type = "JpaTransactionManager")
JpaTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
    JpaTransactionManager transactionManager = new JpaTransactionManager();
    transactionManager.setEntityManagerFactory(entityManagerFactory);
    return transactionManager;
}
```

### Property Conditions

如果我们想在Spring配置文件中的某个属性存在的情况下实例化bean，则可以使用@ConditionalOnProperty。 首先我们需要加载这个Spring的配置文件：

```java
@PropertySource("classpath:mysql.properties")
public class MySQLAutoconfiguration {
    //...
}
```

我们希望属性文件里usemysql=local的时候创建一个DataSource， 则可以这样写：

```java
    @Bean
    @ConditionalOnProperty(
            name = "usemysql",
            havingValue = "local")
    @ConditionalOnMissingBean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();

        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/myDb?createDatabaseIfNotExist=true");
        dataSource.setUsername("mysqluser");
        dataSource.setPassword("mysqlpass");
        return dataSource;
    }
```

### Resource Conditions

当我们需要根据resource文件是否存在来实例化bean的时候，可以使用@ConditionalOnResource 。

```java
    @ConditionalOnResource(
            resources = "classpath:mysql.properties")
    @Conditional(HibernateCondition.class)
    Properties additionalProperties() {
        Properties hibernateProperties = new Properties();

        hibernateProperties.setProperty("hibernate.hbm2ddl.auto",
                env.getProperty("mysql-hibernate.hbm2ddl.auto"));
        hibernateProperties.setProperty("hibernate.dialect",
                env.getProperty("mysql-hibernate.dialect"));
        hibernateProperties.setProperty("hibernate.show_sql",
                env.getProperty("mysql-hibernate.show_sql") != null
                        ? env.getProperty("mysql-hibernate.show_sql") : "false");
        return hibernateProperties;
    }
```

### Custom Conditions

除了使用@Condition** 之外，我们还可以继承SpringBootCondition来实现自定义的condition。 如下所示：

```java
public class HibernateCondition extends SpringBootCondition {

    private static String[] CLASS_NAMES
            = { "org.hibernate.ejb.HibernateEntityManager",
            "org.hibernate.jpa.HibernateEntityManager" };

    @Override
    public ConditionOutcome getMatchOutcome(ConditionContext context,
                                            AnnotatedTypeMetadata metadata) {

        ConditionMessage.Builder message
                = ConditionMessage.forCondition("Hibernate");
        return Arrays.stream(CLASS_NAMES)
                .filter(className -> ClassUtils.isPresent(className, context.getClassLoader()))
                .map(className -> ConditionOutcome
                        .match(message.found("class")
                                .items(ConditionMessage.Style.NORMAL, className)))
                .findAny()
                .orElseGet(() -> ConditionOutcome
                        .noMatch(message.didNotFind("class", "classes")
                                .items(ConditionMessage.Style.NORMAL, Arrays.asList(CLASS_NAMES))));
    }
}
```

## 测试

接下来我们可以测试了:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(
        classes = AutoconfigurationApplication.class)
@EnableJpaRepositories(
        basePackages = { "com.flydean.repository" })
public class AutoconfigurationTest {

    @Autowired
    private MyUserRepository userRepository;

    @Test
    public void whenSaveUser_thenOk() {
        MyUser user = new MyUser("user@email.com");
        userRepository.save(user);
    }
}
```

这里我们因为没有自定义dataSource所以会自动使用自动配置里面的mysql数据源。

## 停止自动配置

如果我们不想使用刚刚创建的自动配置该怎么做呢？在@SpringBootApplication中exclude MySQLAutoconfiguration.class即可：

```java
@SpringBootApplication(exclude={MySQLAutoconfiguration.class})
```

# 在Spring Boot中配置web app

## 添加依赖

如果要使用Spring web程序，则需要添加如下依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

## 配置端口

正如我们之前文章中提到的，要想配置端口需要在application.properties文件中配置如下：

```properties
server.port=8083
```

如果你是用的是yaml文件，则：

```yaml
server:
    port: 8083
```

或者通过java文件的形式：

```java
@Component
public class CustomizationBean implements
        WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {

    @Override
    public void customize(ConfigurableServletWebServerFactory container) {
        container.setPort(8083);
    }
}
```

## 配置Context Path

默认情况下，Spring MVC的context path是‘/’, 如果你想修改，那么可以在配置文件application.properties中修改：

```properties
server.servlet.contextPath=/springbootapp
```

如果是yaml文件：

```java
server:
    servlet:
        contextPath:/springbootapp
```

同样的，可以在java代码中修改：

```java
@Component
public class CustomizationBean
  implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {

    @Override
    public void customize(ConfigurableServletWebServerFactorycontainer) {
        container.setContextPath("/springbootapp");
    }
}
```

## 配置错误页面

默认情况下Spring Boot会开启一个whitelabel的功能来处理错误，这个功能本质上是自动注册一个BasicErrorController如果你没有指定错误处理器的话。同样的，这个错误控制器也可以自定义：

```java
@RestController
public class MyCustomErrorController implements ErrorController {

    private static final String PATH = "/error";

    @GetMapping(value=PATH)
    public String error() {
        return "Error haven";
    }

    @Override
    public String getErrorPath() {
        return PATH;
    }
}
```

当然，和之前讲过的自定义服务器信息一样，你也可以自定义错误页面，就像在web.xml里面添加error-page：

```java
@Component
public class CustomizationBean
  implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {

    @Override
    public void customize(ConfigurableServletWebServerFactorycontainer) {        
        container.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
        container.addErrorPages(new ErrorPage("/errorHaven"));
    }
}
```

## 在程序中停止Spring Boot

SpringApplication提供了一个静态的exit()方法，可以通过它来关停一个Spring Boot应用程序:

```java
    @Autowired
    public void shutDown(ApplicationContext applicationContext) {
        SpringApplication.exit(applicationContext, new ExitCodeGenerator() {
            @Override
            public int getExitCode() {
                return 0;
            }
        });
    }
```

第二个参数是一个ExitCodeGenerator的实现，主要用来返回ExitCode。

## 配置日志级别

我们可以在配置文件中这样配置日志级别：

```java
logging.level.org.springframework.web: DEBUG
logging.level.org.hibernate: ERROR
```

## 注册Servlet

有时候我们需要将程序运行在非嵌套的服务器中，这时候有可能会需要自定义servlet的情况，Spring Boot 也提供了非常棒的支持，我们只需要在ServletRegistrationBean中，注册servlet即可：

```java
    @Bean
    public ServletRegistrationBean servletRegistrationBean() {

        ServletRegistrationBean bean = new ServletRegistrationBean(
                new SpringHelloWorldServlet(), "/springHelloWorld/*");
        bean.setLoadOnStartup(1);
        bean.addInitParameter("message", "SpringHelloWorldServlet special message");
        return bean;
    }
```

## 切换嵌套服务器

默认情况下，Spring Boot会使用tomcat作为嵌套的内部服务器，如果想切换成jetty则可以这样：

```xml
<dependencies>
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
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
</dependencies>
```

exclude自带的Tomcat，并额外添加spring-boot-starter-jetty即可。

# 从Spring迁移到Spring Boot

Spring Boot给我们的开发提供了一系列的便利，所以我们可能会希望将老的Spring 项目转换为新的Spring Boot项目，本篇文章将会探讨如何操作。

> *请注意，Spring Boot并不是取代Spring，它只是添加了一些自动配置的东西，从而让Spring程序更快更好*

## 添加Spring Boot starters

要想添加Spring Boot，最简单的办法就是添加Spring Boot Starters。

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.2.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

## 添加应用程序入口

每一个Spring Boot程序都需要一个应用程序入口，通常是一个使用@SpringBootApplication注解的main程序：

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

@SpringBootApplication注解是下列注解的组合：

`@Configuration `，`@EnableAutoConfiguration`，`@ComponentScan `。

默认情况下@SpringBootApplication会扫描本package和子package的所有类。所以一般来说SpringBootApplication会放在顶层包下面。

## Import Configuration和Components

Spring Boot通常使用自动配置，但是我们也可以手动Import现有的java配置或者xml配置。

对于现有的配置，我们有两个选项，一是将这些配置移动到主Application同一级包或者子包下面，方便自动扫描。
二是显式导入。

我们看一下怎么显示导入：

```java
@SpringBootApplication
@ComponentScan(basePackages="com.flydean.config")
@Import(UserRepository.class)
public class Application {
    //...
}
```

如果是xml文件，你也可以这样使用@ImportResource导入：

```java
@SpringBootApplication
@ImportResource("applicationContext.xml")
public class Application {
    //...
}
```

## 迁移应用程序资源

默认情况下Spring Boot 会查找如下的资源地址：

```java
/resources
/public
/static
/META-INF/resources
```

想要迁移的话 我们可以迁移现有资源到上诉的资源地址，也可以使用下面的方法：

```java
spring.resources.static-locations=classpath:/images/,classpath:/jsp/
```

## 迁移应用程序属性文件

Spring Boot 会在如下的地方查找application.properties或者application.yml 文件：

```
* 当前目录
* 当前目录的/config子目录
* 在classpath中的/config目录
* classpath root
```

我们可以将属性文件移动到上面提到的路径下面。

## 迁移Spring Web程序

如果要迁移Spring Web程序，我们需要如下几步：

1. 添加spring-boot-starter-web依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

通过Spring Boot的自动配置，会自动检测classpath中的依赖包，从而自动开启@EnableWebMvc，同时创建一个DispatcherServlet。

如果我们在@Configuration类中使用了@EnableWebMvc注解，则自动配置会失效。

该自动配置同时自动配置了如下3个bean：
\* HttpMessageConverter用来转换JSON 和 XML。
\* /error mapping用来处理所有的错误
\* /static, /public, /resources 或者 /META-INF/resources的静态资源支持。

2. 配置View模板

对于web页面，通常不再推荐JSP，而是使用各种模板技术来替换：Thymeleaf, Groovy, FreeMarker, Mustache。 我们要做的就是添加如下依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

template文件在/resources/templates下面。

如果我们仍然需要是用JSP，则需要显示配置如下：

```java
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```













