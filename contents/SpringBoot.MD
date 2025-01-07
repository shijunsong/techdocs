# SpringMVC

### 常用注解

常规注解

@Bean、@Import、@ImportResource、@Autowired、@Resource、@Inject、@Component、@Controller
@RestController、@Service、@Repository、@RequestMapping、@ResponseBody、@Value、@PathVariable
@ControllerAdvice、@Profiles、@ExceptionHandler、@Async、@ConfigurationProperties

AOP面向切面编程
控制反转 与 依赖注入



### 自定义注解

### Spring Bean 创建流程

### spring bean 生命周期

Bean类文件
调用无参构造函数 
生成对象 
依赖注入（给属性赋值（Autowired））	
初始化前执行（Postconstruct）
初始化
初始化后（AOP，生成代理对象）
	代理对象增加个被代理对象的私有属性，将原被代理对象的值赋值给该属性。
	查找是否被切面，虽有切面方法缓存，执行直接查找缓存的方法	

### Spring 事务原理

#### 1. @Transaction基于AOP创建代理对象

创建数据库链接

conn.autocommit = false

`执行业务方法逻辑`

conn.commit() 异常 则  conn.rollback()

#### 2. 事务传播

事务失效原因： trans注解必须调用代理的方法，传播属性才能生效

#### 3. 为什么要加@Configuration注解？

保证jdbcTemplate 和 代理中创建的链接使用同一个Datasource， 这样事务才能生效。

该注解使用代理，在bean注入时，查看bean缓存中是否已经有bean， 如果有则返回，由此保证同一个datasource

#### 4. spring事务失效

#### 5. 事务失效的原因

#### 6. 事务的传播特性



# SpringBoot

### 原理



### 常用注解

- @SpringBootApplication
- @SpringBootConfiguration
- @EnableAutoConfiguration
- @ComponentScan
- @Configuration