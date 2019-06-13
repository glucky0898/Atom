
<!-- TOC -->

- [第一部分：Spring的核心](#%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86%EF%BC%9Aspring%E7%9A%84%E6%A0%B8%E5%BF%83)
  - [第一章:Spring之旅](#%E7%AC%AC%E4%B8%80%E7%AB%A0spring%E4%B9%8B%E6%97%85)
  - [第三章:自动化装配Bean](#%E7%AC%AC%E4%B8%89%E7%AB%A0%E8%87%AA%E5%8A%A8%E5%8C%96%E8%A3%85%E9%85%8Dbean)

<!-- /TOC -->
### 第一部分：Spring的核心

  #### 第一章:Spring之旅
  > spring采取4种策略：  
  1.基于POJO的轻量级和最小侵入性编程：如不继承或实现spring接口，是spring类和应用的类绑定  
  2.通过依赖注入和面向接口编程实现松耦合    
 
 概念：  
 1.装配：创建组件与组件之间协作的行为  
 2.DI:对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候设定，对象无需自行创建和管理他们的依赖关系 注入方式：xml、注解、java代码
 3.AOP:允许遍布应用各处的功能分离出来形成可重用的组件。切面关注点：日志、事务、安全。与业务逻辑分离
 ```JAVA
   <aop:config>
    <aop:aspect ref="minstrel">
      <aop:pointcut id="embark"
          expression="execution(* *.embarkOnQuest(..))"/>//定义切入点
      <aop:before pointcut-ref="embark" 
          method="singBeforeQuest"/>//前置通知 ，调用embarkOnQuest(..)前 执行singBeforeQuest()
      <aop:after pointcut-ref="embark" 
          method="singAfterQuest"/>//同上
    </aop:aspect>
  </aop:config>
 ```
 4.应用上下文：通过Spring应用上下文，装载bean的定义，把他们组装起来。Spring应用上下文全权负责对象的创建和组装  
  ```JAVA
   public static void main(String[] args) throws Exception {
    ClassPathXmlApplicationContext context = 
        new ClassPathXmlApplicationContext(
            "META-INF/spring/knight.xml");//加载Spring上下文
    Knight knight = context.getBean(Knight.class);//获取bean
    knight.embarkOnQuest();
    context.close();
  }

  ```
  #### 第三章:自动化装配Bean
  > 组件扫描+自动装配

  > 1.自动装配  
  ```java    
    //xml配置扫描包
      <context:component-scan base-package="soundsystem" /> //组件扫描
    package soundsystem;
    import org.springframework.stereotype.Component;
      @Component
    public class SgtPeppers implements CompactDisc {
    private String title = "Sgt. Pepper's Lonely Hearts Club Band";  
    private String artist = "The Beatles";
    public void play() {
      System.out.println("Playing " + title + " by " + artist);
    }
    }
    //自动装配
    @Autowired
    private SgtPeppers sp;
  ```
  > 2.JavaConfig实现注入：JavaConfig 可以看成一个用于完成 Bean 装配的 Spring 配置文件，即 Spring 容器，只不过该容器不是 XML文件，而是由程序员使用 Java 自己编写的 Java 类
  ```java
  //xml配置自动扫描
    <context:component-scan base-package="com.lzl.spring" />
  public class Car {
    //属性
  }

  public class Person {
    //属性
  }

  //定义 JavaConfig类 对于一个 POJO 类，在类上使用@Configuration 注解，将会使当前类作为一个 Spring 的容器来使用，用于完成 Bean 的创建。在该 JavaConfig 的方法上使用@Bean，将会使一个普通方法所返回的结果变为指定名称的 Bean 实例。
    @Configuration
    public class MyConfig {
    //该注解表示：向容器中注册一个叫做myCar的对象
    @Bean("myCar")
    public Car getCar() {
      return new Car("保时捷","911",300);
  }
    //该注解表示：向容器中注册一个叫做person的对象
    //并且通过byType的方式注入car
    @Bean(name="person",autowire=Autowire.BY_TYPE)
    public Person getPerson() {
      return new Person(1001,"望穿秋水见伊人");
    }
  }
    //测试
    ApplicationContext ctx=new ClassPathXmlApplicationContext("spring-config.xml");
    Car car = ctx.getBean("myCar", Car.class);
    Person person = ctx.getBean("person", Person.class);
  ```
  > 3.XML装配
  ```xml
    <bean id="knight" class="sia.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="sia.knights.SlayDragonQuest">
    <constructor-arg value="#{T(System).out}" />
  </bean>
  ```