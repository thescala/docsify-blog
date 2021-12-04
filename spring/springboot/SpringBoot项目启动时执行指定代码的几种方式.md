# SpringBoot 项目启动时执行指定代码的几种方式

> 版权声明：本文为 CSDN 博主「墩墩分墩」的原创文章，遵循 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/qq877728715/article/details/110860306

## 前言

在 Spring 应用服务启动时，需要提前加载一些数据和执行一些的初始化工作。例如：删除临时文件，清除缓存信息，读取配置文件，获取数据库连接，这些工作在开机启动之后只要执行一次就行了

## 1. `ApplicationListener`

容器刷新成功意味着所有的 Bean 初始化已经完成，当容器刷新之后 Spring 将会调用容器内所有实现了`ApplicationListener<ContextRefreshedEvent>`的 Bean 的`onApplicationEvent`方法，应用程序可以以此达到监听容器初始化完成事件的目的。

```java
@Component
@Slf4j
public class StartupApplicationListener implements ApplicationListener<ContextRefreshedEvent> {

    public static int counter;

    @Override public void onApplicationEvent(ContextRefreshedEvent event) {
        log.info("Increment counter");
        counter++;
    }
}
```

易错的点
在 web 容器中的时候需要额外注意，在 web 项目中（例如 spring mvc），系统会存在两个容器，一个是 root application context,另一个就是我们自己的 context（作为 root application context 的子容器）。如果按照上面这种写法，就会造成**onApplicationEvent 方法被执行两次**。解决此问题的方法如下：

```java
@Component
@Slf4j
public class StartupApplicationListener implements ApplicationListener<ContextRefreshedEvent> {

    public static int counter;

    @Override public void onApplicationEvent(ContextRefreshedEvent event) {
        if (event.getApplicationContext().getParent() == null) {
            // root application context 没有parent
            LOG.info("Increment counter");
            counter++;
        }
    }
}
```

高阶玩法：自定义事件，可以借助 Spring 以最小成本实现一个观察者模式：

自定义一个事件：

```java
@Data
public class NotifyEvent extends ApplicationEvent {
    private String email;
    private String content;
    public NotifyEvent(Object source) {
        super(source);
    }
    public NotifyEvent(Object source, String email, String content) {
        super(source);
        this.email = email;
        this.content = content;
    }
}
```

注册一个事件监听器

```java
@Component
public class NotifyListener implements ApplicationListener<NotifyEvent> {
    @Override
    public void onApplicationEvent(NotifyEvent event) {
        System.out.println("邮件地址：" + event.getEmail());
        System.out.println("邮件内容：" + event.getContent());
    }
}
```

发布事件

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ListenerTest {
    @Autowired
    private WebApplicationContext webApplicationContext;

    @Test
    public void testListener() {
        NotifyEvent event = new NotifyEvent("object", "abc@qq.com", "This is the content");
        webApplicationContext.publishEvent(event);
    }
}
```

执行单元测试可以看到邮件的地址和内容都被打印出来了

## 2.`ServletContextAware`接口

实现`ServletContextAware`接口重写`setServletContext`方法

实现该接口的类可以直接获取 Web 应用的`ServletContext`实例(Servler 容器)

```java
@Component
public class ServletContextAwareImpl implements ServletContextAware {
    /**
     * 在填充普通bean属性之后但在初始化之前调用
     * 类似于initializing Bean的afterpropertiesset或自定义init方法的回调
     *
     */
    @Override
    public void setServletContext(ServletContext servletContext) {
        System.out.println("===============ServletContextAwareImpl初始化================");
    }
}
```

## 3.`ServletContextListener`接口

实现`ServletContextListener`接口重写`contextInitialized`方法

- 它能够监听 `ServletContext`对象的生命周期，即监听 Web 应用的生命周期。
- 当 Servlet 容器启动或终止 Web 应用时，会触发`ServletContextEvent` 事件，该事件由 `ServletContextListener`的 2 个方法两处理:
  - `contextInitialized(ServletContextEvent sce)` ：当 Servlet 容器启动 Web 应用时调用该方法。在调用完该方法之后，容器再对 Filter 初始化，并且对那些在 Web 应用启动时就需要被初始化的 Servlet 进行初始化。
  - `contextDestroyed(ServletContextEvent sce)`：当 Servlet 容器终止 Web 应用时调用该方法。在调用该方法之前，容器会先销毁所有的 Servlet 和 Filter 过滤器。

```java
@Component
public class ServletContextListenerImpl implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        //ServletContext servletContext = sce.getServletContext();
        System.out.println("===============ServletContextListenerImpl初始化(contextInitialized)================");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) { 			            System.out.println("=========ServletContextListenerImpl(contextDestroyed)=========");
    }
}
```

## 4.`@PostConstruct`注解

- 被`@PostConstruct`修饰的方法会在服务器加载 Servlet 的时候运行，并且在构造函数之后，init()方法之前运行。
- 使用 `@PostConstruct`注解进行初始化操作的顺序是最快的，前提是这些操作不依赖其它 Bean 的初始化完成。

```java
@Component
public class PostConstructInit {
    //静态代码块会在依赖注入后自动执行,并优先执行
    static{
        System.out.println("---static--");
    }
    /**
     *  @Postcontruct’在依赖注入完成后自动调用
     */
    @PostConstruct
    public static void haha(){
        System.out.println("===========@Postcontruct初始化（在依赖注入完成后自动调用）===========");
    }
}
```

> `@PreDestroy`
>
> `@PreDestroy`修饰的方法会在服务器卸载 Servlet 的时候运行，并且只会被服务器调用一次，类似于 Servlet 的 destroy()方法。被`@PreDestroy`修饰的方法会在 destroy()方法之后运行，在 Servlet 被彻底卸载之前。

## 5.`CommandLineRunner`接口

实现`CommandLineRunner` 接口重写 run 方法

- 可以接收字符串数组的命令行参数

- 在构造`SpringApplication`实例完成之后调用

- 可以通过 Order 注解或者使用 Ordered 接口来指定调用顺序，`@Order()`中的值越小，优先级越高

- 同时使用`ApplicationRunner`和`CommandLineRunner`，默认情况下`ApplicationRunner`比`CommandLineRunner`先执行

```java
@Component
public class CommandLineRunnerImpl implements CommandLineRunner {
    /**
     * 用于指示bean包含在SpringApplication中时应运行的接口。可以在同一应用程序上下文中定义多个commandlinerunner bean，并且可以使用有序接口或@order注释对其进行排序。
     * 如果需要访问applicationArguments而不是原始字符串数组，请考虑使用applicationRunner。
     *
     */
    @Override
    public void run(String... args) throws Exception {
        System.out.println("===============CommandLineRunnerImpl初始化================");
    }
}
```

## 6.`ApplicationRunner`接口

实现`ApplicationRunner`接口重写 run 方法

- `ApplicationRunner` 是使用`ApplicationArguments`来获取参数的

- 在构造`SpringApplication`实例完成之后调用 run()的时候调用

- 可以通过@Order 注解或者使用 Ordered 接口来指定调用顺序，@Order()中的值越小，优先级越高

- 同时使用`ApplicationRunner`和`CommandLineRunner`，默认情况下`ApplicationRunner`比`CommandLineRunner`先执行

- `ApplicationRunner` 和 `CommandLineRunner` 功能一致，唯一的区别主要体现在对参数的处理上，`ApplicationRunner `可以接收更多类型的参数

```java
@Component
public class ApplicationRunnerImpl implements ApplicationRunner {
    /**
     * 用于指示bean包含在SpringApplication中时应运行的接口。可以定义多个applicationRunner Bean
     * 在同一应用程序上下文中，可以使用有序接口或@order注释对其进行排序。
     */
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("===============ApplicationRunnerImpl初始化================");
    }
}
```

> - `args.getSourceArgs()`：获取命令行中的所有参数。
> - `args.getNonOptionArgs()`：获取命令行中的无 key 参数（和`CommandLineRunner`一样）。
> - `args.getOptionNames()`：获取所有 key/value 形式的参数的 key。
> - `args.getOptionValues(key)`：根据 key 获取 key/value 形式的参数的 value。

## 7.`InitializingBean`接口

在对象的所有属性被初始化后之后才会调用`afterPropertiesSet()`方法

```java
public class InitializingBeanImpl implements InitializingBean {
    @Override
    public void afterPropertiesSet() {
        System.out.println("===============InitializingBeanImpl初始化================");
    }
}
```

## 8.`SmartLifecycle`接口

`SmartLifecycle` 不仅仅能在初始化后执行一个逻辑，还能再关闭前执行一个逻辑，并且也可以控制多个 `SmartLifecycle` 的执行顺序，就像这个类名表示的一样，这是一个智能的生命周期管理接口。

```java
import org.springframework.context.SmartLifecycle;
import org.springframework.stereotype.Component;

/**
 * SmartLifecycle测试
 *
 */
@Component
public class TestSmartLifecycle implements SmartLifecycle {

    private boolean isRunning = false;

    /**
     * 1. 我们主要在该方法中启动任务或者其他异步服务，比如开启MQ接收消息<br/>
     * 2. 当上下文被刷新（所有对象已被实例化和初始化之后）时，将调用该方法，默认生命周期处理器将检查每个SmartLifecycle对象的isAutoStartup()方法返回的布尔值。
     * 如果为“true”，则该方法会被调用，而不是等待显式调用自己的start()方法。
     */
    @Override
    public void start() {
        System.out.println("start");

        // 执行完其他业务后，可以修改 isRunning = true
        isRunning = true;
    }

    /**
     * 如果项目中有多个实现接口SmartLifecycle的类，则这些类的start的执行顺序按getPhase方法返回值从小到大执行。默认为 0<br/>
     * 例如：1比2先执行，-1比0先执行。 stop方法的执行顺序则相反，getPhase返回值较大类的stop方法先被调用，小的后被调用。
     */
    @Override
    public int getPhase() {
        // 默认为0
        return 0;
    }

    /**
     * 根据该方法的返回值决定是否执行start方法。默认为 false<br/>
     * 返回true时start方法会被自动执行，返回false则不会。
     */
    @Override
    public boolean isAutoStartup() {
        // 默认为false
        return true;
    }

    /**
     * 1. 只有该方法返回false时，start方法才会被执行。    默认返回 false <br/>
     * 2. 只有该方法返回true时，stop(Runnable callback)或stop()方法才会被执行。
     */
    @Override
    public boolean isRunning() {
          // 默认返回false
          return isRunning;
    }

    /**
     * SmartLifecycle子类的才有的方法，当isRunning方法返回true时，该方法才会被调用。
     */
    @Override
    public void stop(Runnable callback) {
        System.out.println("stop(Runnable)");

        // 如果你让isRunning返回true，需要执行stop这个方法，那么就不要忘记调用callback.run()。
        // 否则在你程序退出时，Spring的DefaultLifecycleProcessor会认为你这个TestSmartLifecycle没有stop完成，程序会一直卡着结束不了，等待一定时间（默认超时时间30秒）后才会自动结束。
        // PS：如果你想修改这个默认超时时间，可以按下面思路做，当然下面代码是springmvc配置文件形式的参考，在SpringBoot中自然不是配置xml来完成，这里只是提供一种思路。
        // <bean id="lifecycleProcessor" class="org.springframework.context.support.DefaultLifecycleProcessor">
        //      <!-- timeout value in milliseconds -->
        //      <property name="timeoutPerShutdownPhase" value="10000"/>
        // </bean>
        callback.run();

        isRunning = false;
    }

    /**
     * 接口Lifecycle的子类的方法，只有非SmartLifecycle的子类才会执行该方法。<br/>
     * 1. 该方法只对直接实现接口Lifecycle的类才起作用，对实现SmartLifecycle接口的类无效。<br/>
     * 2. 方法stop()和方法stop(Runnable callback)的区别只在于，后者是SmartLifecycle子类的专属。
     */
    @Override
    public void stop() {
          System.out.println("stop");

        isRunning = false;
    }

}
```

- start()：bean 初始化完毕后，该方法会被执行。

- stop()：容器关闭后，spring 容器发现当前对象实现了 SmartLifecycle，就调用 stop(Runnable)， 如果只是实现了 Lifecycle，就调用 stop()。

- isRunning()：当前状态，用来判你的断组件是否在运行。

- getPhase()：控制多个 SmartLifecycle 的回调顺序的，返回值越小越靠前执行 start() 方法，越靠后执行 stop() 方法。

- isAutoStartup()：start 方法被执行前先看此方法返回值，返回 false 就不执行 start 方法了。

- stop(Runnable)：容器关闭后，spring 容器发现当前对象实现了 SmartLifecycle，就调用 stop(Runnable)， 如果只是实现了 Lifecycle，就调用 stop()。

## 9.控制 Bean 初始化顺序

如果想要指定 Spring 容器加载 Bean 的顺序，可以通过实现`org.springframework.core.Ordered`接口或者使用`org.springframework.core.annotation.Order`注解来实现。

- order 值越小，该初始化类也就越早执行

### 9.1.实现 Ordered 接口

```java
/**
 * Created by pangkunkun on 2017/9/3.
 */
@Component
public class MyApplicationRunner implements ApplicationRunner,Ordered{


    @Override
    public int getOrder(){
        return 1;//通过设置这里的数字来知道指定顺序
    }

    @Override
    public void run(ApplicationArguments var1) throws Exception{
        System.out.println("MyApplicationRunner1!");
    }
}
```

### 9.2.使用@Order 注解

```java
@Component
@Order(value = 1)
public class MyApplicationRunner implements ApplicationRunner{

    @Override
    public void run(ApplicationArguments var1) throws Exception{
        System.out.println("MyApplicationRunner1!");
    }
}
```

## 10.原始初始化方式

如果不依赖于 Spring 的实现，我们可以在静态代码块，在类构造函数中实现相应的逻辑

- Java 类的初始化顺序依次是 **静态变量 > 静态代码块 > 全局变量 > 初始化代码块 > 构造方法**。
