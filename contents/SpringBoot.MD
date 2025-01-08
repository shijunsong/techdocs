# SpringMVC

### 常用注解

@Bean、@Import、@ImportResource、@Autowired、@Resource、@Inject、@Component、@Controller  
@RestController、@Service、@Repository、@RequestMapping、@ResponseBody、@Value、@PathVariable  
@ControllerAdvice、@Profiles、@ExceptionHandler、@Async、@ConfigurationProperties

### Spring MVC 工作流程

- Spring MVC 核心组件：
  - DispatcherServlet：前端控制器，Spring MVC 的核心。接收所有的请求并分发给相应的处理器。
  - HandlerMapping：处理器映射器，负责根据请求的 URL 查找对应的处理器。
  - HandlerAdapter：处理器适配器，负责调用 Controller 中的具体方法。
  - Controller：控制器，处理具体的用户请求。一个 Controller 通常对应于一个或多个 URL。
  - ViewResolver：视图解析器，根据 ModelAndView 中的视图名解析出具体的 View。
  - View：视图，负责渲染返回给用户的页面。

- MVC流程：

<img src="../pictures/spring/mvc process.png" alt="img" style="zoom:70%;"/>

### IOC & AOP

IOC（Inversion of Control，控制反转）

> - 定义
>   指创建对象的控制权转移给Spring来进行管理。  
>   简单来说，就是应用不用去new对象了，而全部交由Spring自动生产。
> - 三种注入方式：1、 构造器注入  2、setter方法注入 3、根据注解注入。

AOP （Aspect Oriented Programming，面向切面编程）

> - 定义
>   用于将那些与业务无关，但却对多个对象产生影响的公共行为。  
>   抽取并封装成一个可重用的模块。
>
> - 核心
>
>   动态代理。JDK的动态代理  和  CGLIB动态代理。

### Spring 获取bean方法

> new AnnotationConfigApplicationContext(). getBean()

### Spring 容器的启动流程

使用AnnotationConfigApplicationContext 来跟踪一下启动流程：  

> 1. **this**();  初始化reader和scanner
>
> 2. **scan**(basePackages); 使用scanner组件扫描basePackage下的所有对象，将配置类的BeanDefinition注册到容器中。
>
> 3. **refresh**();  刷新容器。
>
>    > **prepareRefresh**：刷新前的预处理  
>    > **obtainFreshBeanFactory**： 获取在容器初始化时创建的BeanFactory  
>    > **prepareBeanFactory**: BeanFactory的预处理工作，会向容器中添加一些组件。  
>    > **postProcessBeanFactory**：子类重写该方法，可以实现在BeanFactory创建并预处理完成后做进一步的设置。
>    > **invokeBeanFactoryPostProcessors**： 在BeanFactory初始化之后执行BeanFactory的后处理器。
>    > **registerBeanPostProcessors**： 向容器中注册Bean的后处理器，他的主要作用就是干预  
>    > Spring初始化Bean的流程，完成代理、自动注入、循环依赖等这些功能。  
>    > **initMessageSource**： 初始化messagesource组件，主要用于国际化。  
>    > **initApplicationEventMulticaster**： 初始化事件分发器  
>    > **onRefresh**： 留给子容器，子类重写的方法，在容器刷新的时候可以自定义一些逻辑。  
>    > **registerListeners**: 注册监听器。  
>    > **finishBeanFactoryInitialization**： 完成BeanFactory的初始化，主要作用是初始化所有剩下的单例Bean。  
>    > **finishRefresh**： 完成整个容器的初始化，发布BeanFactory容器刷新完成的事件。

### Spring Bean 创建流程

- 四阶段：实例化 > 属性赋值 > 初始化 > 销毁

- 具体步骤：

  > 1、**实例化： new xxx();** 
  >
  > > 两个时机:
  > >
  > > - 当客户端向容器申请一个Bean时，
  > > - 当容器在初始化一个Bean时发现还需要依赖另一个Bean。
  >
  > 2、**设置对象属性**(依赖注入)：BeanDefinition找到对象依赖的其他对象，并赋予当前对象。
  >
  > > - 构造函数选择：(推断机制)
  > >
  > >   > 如果一个类**只有一个构造方法**，那没的选择，只能用这个构造方法  
  > >   > 如果一个类有**多个构造方法**，首**先**会判断是否有**@Autowired指定**构造方法，**再**看是否有**无参构造**方法。如果**都没有就会报错** No default constructor found
  > >
  > > - Bean查找方式：在缓存map里先按照Class类型匹配（instanceof），然后按照bean name
  >
  > 3、**处理Aware接口**
  >
  > > Spring会检测对象是否实现了xxxAware接口，如果实现了，就会调用对应的方法。  
  > > `BeanNameAware`、`BeanClassLoaderAware`、`BeanFactoryAware`、`ApplicationContextAware`
  >
  > 4、**BeanPostProcessor前置处理**
  >
  > > 调用BeanPostProcessor的postProcessBeforeInitialization方法
  >
  > 5、**InitializingBean**
  >
  > > Spring检测对象如果实现了这个接口，就会执行他的afterPropertiesSet()方法，定制初始化逻辑。
  >
  > 6、**init-method**
  >
  > > 执行初始化逻辑。 1. @PostConstruct注解   2. <bean init-method=xxx> 
  >
  > 7、**BeanPostProcessor后置处理**：  调用BeanPostProcessor的**postProcessAfterInitialization**方法  
  >
  > > 到这里，这个Bean的创建过程就完成了， Bean就可以正常使用了。  
  >
  > 8、**DisposableBean**   
  >
  > > 当Bean实现了这个接口，在对象销毁前就会调用destory()方法。
  >
  > 9、**destroy-method**
  >
  > > 1.<bean destroy-method=xxx>配置    2.@PreDestroy注解

### Spring bean 生命周期

- Bean类文件

- 调用无参构造函数 

- 生成对象 

- 依赖注入（给属性赋值（Autowired））

  > Spring通过BeanDefinition找到对象依赖的其他对象，并将这些对象赋予当前对象。

- 初始化前执行（Postconstruct）

- 初始化

- 初始化后（AOP，生成代理对象）
  	代理对象增加个被代理对象的私有属性，将原被代理对象的值赋值给该属性。  
  	查找是否被切面，虽有切面方法缓存，直接查找执行缓存的方法

### Spring Bean作用域

| sington（默认） | 单例                                          |
| --------------- | --------------------------------------------- |
| prototype       | 为每个Bean请求创建给实例                      |
| request         | 为每个request请求创建一个实例，请求完成后失效 |
| session         | 与request是类似的                             |
| global-session  | 全局作用域                                    |

### Spring 事务

#### 1. 事务分类

- 编程式事务：可以使用TransactionTemplate

- 声明式事务：@Transaction基于AOP创建代理对象

  ```
  {
  	创建数据库链接
  	conn.autocommit = false
  	`执行业务方法逻辑`
  	conn.commit() 
  } 异常 {
  	conn.rollback()
  }
  ```

#### 2. 事务传播特性(Propagation)

| 1    | PROPAGATION_REQUIRED(默认) | 没有就创建后续加入（只一个）                                 |
| ---- | -------------------------- | ------------------------------------------------------------ |
| 2    | PROPAGATION_SUPPORTS       | 存在事务则加入，没有则以非事务运行                           |
| 3    | PROPAGATION_MANDATORY      | 存在事务则加入，否则抛异常                                   |
| 4    | PROPAGATION_REQUIRES_NEW   | 无论存在，直接创建                                           |
| 5    | PROPAGATION_NOT_SUPPORTED  | 以非事务方式运行，挂起当前存在事务                           |
| 6    | PROPAGATION_NEVER          | 以非事务方式运行。存在事务抛出异常                           |
| 7    | PROPAGATION_NESTED         | 存在事务，则在**嵌套事务**内执行；<br />没有事务，则按REQUEIRED属性执行。<br /> |

**嵌套事务**

>  内层事务依赖于外层事务, 外层事务回滚会导致内层事务回滚，反之则不影响

#### 3. 事务隔离级别

| ISOLATION_DEFAULT（默认）  | 使用数据库默认的事务隔离级别                                 |
| -------------------------- | ------------------------------------------------------------ |
| ISOLATION_READ_UNCOMMITTED | 读未提交。允许事务在执行过程中，读取其他事务未提交的数据。   |
| ISOLATION_READ_COMMITTED   | 读已提交。允许事务在执行过程中，读取其他事务已经提交的数据。 |
| ISOLATION_REPEATABLE_READ  | 可重复读。 在同一个事务内，任意时刻的查询结果是一致的。      |
| ISOLATION_SERIALIZABLE     | 所有事务依次执行。                                           |

#### 4. 为什么要加@Configuration注解？

保证jdbcTemplate 和 代理中创建的链接使用同一个Datasource， 这样事务才能生效。  
该注解使用代理，在bean注入时，查看bean缓存中是否已经有bean，   
如果有则返回，由此保证同一个datasource

#### 5. 事务失效的原因

- 直接new 出来的对象

- @Transaction未加到public方法

- 异常类型错误，只有runtimeException 才进行回滚

- catch到的异常没有抛出

- A中加了事务的方法调用了A中没有加事务的方法

  > trans注解必须调用代理的方法，传播属性才能生效，  
  > 未加注解的代理类只是普通调用，并非在同一个事务里边

- mysql myisam引擎不支持事务	

### 【面试题】

#### 1. Bean是否线程安全？如果线程不安全，如何处理？

- Bean **默认 sington作用域** 线程非安全，大部分开发Bean无状态的，不需要保证线程安全。

  > 无状态表示这个实例没有属性对象，不能保存数据，是不变的类。比如：controller、service、dao  
  > 有状态表示示例是有属性对象，可以保存数据，是线程不安全的。比如 pojo.

- 保证线程安全
  - 可以将Bean的作用域改为prototype
  - 采用ThreadLocal来解决线程安全问题
  - 设计成无状态的，不要保存数据，可以引用无状态的service 或 dao

#### 2. Spring如何处理循环依赖问题？

- 现象：多个对象之间存在循环的引用关系

- 原因：

  一级缓存：缓存最终的单例池对象  
  `private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);`  

  二级缓存：缓存初始化的对象  
  `private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);`    

  三级缓存：缓存对象的ObjectFactory  

  `private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);`

  - 对象之间普通引用：

    > 1.二级缓存会保存new出来的不完整对象，  
    > 2.当单例池中找到不依赖的属性时，就可以先从二级缓存中获取到不完整对象，完成对象创建，  
    > 3.在后续的依赖注入过程中，将单例池中对象的引用关系调整完成。

  - 如果引用的对象配置了AOP:

    > 单例池中最终就会需要注入动态代理对象，而不是原对象。  
    > 而生成动态代理是要在对象初始化完成之后才开始的。  
    > 于是Spring增加三级缓存，保存所有对象的动态代理配置信息。  
    > 在发现有循环依赖时，将这个对象的动态代理信息获取出来，提前进行AOP，生成动态代理。

  - 核心代码DefaultSingletonBeanRegistry.getSingleton()方法

- 解决办法：

  - **@Lazy注解**： 解决构造方法造成的循环依赖问题
  - **使用三级缓存**

# SpringBoot

### 特性

#### 1. starter及使用

#### 2. 为什么只扫描核心启动类及其子包

3启动过程如何完成自动装配

内嵌tomcat 如何创建启动

引入starter，springmvc如何完成自动装配

### 原理



### 常用注解

- @SpringBootApplication
- @SpringBootConfiguration
- @EnableAutoConfiguration
- @ComponentScan
- @Configuration