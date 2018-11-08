[TOC]

#### Junit 测试工具
 - 针对类
 > @RunWith(value = SpringJUnit4ClassRunner.class) 在使用所有注释前必须使用，让测试运行于Spring测试环境,用以更改测试运行器
  @ContextConfiguration(locations = {"classpath:spring/spring-ds.xml"}) 用来指定加载的Spring配置文件的位置

 - 针对方法
>  @Test:将一个普通方法修饰成一个测试方法
  @Test(excepted=xx.class): xx.class表示异常类，表示测试的方法抛出此异常时，认为是正常的测试通过的
  @Test(timeout=毫秒数) :测试方法执行时间是否符合预期
  @BeforeClass： 会在所有的方法执行前被执行，static方法
  @AfterClass：会在所有的方法执行之后进行执行，static方法
  @Before：会在每一个测试方法被运行前执行一次
  @After：会在每一个测试方法运行后被执行一次
  @Ignore：所修饰的测试方法会被测试运行器忽略
  @Parameters：参数化注解

 - 注意
  > 测试方法必须使用public void进行修饰，不能带参数
  测试单元中的每个方法必须可以独立测试，方法间不能有任何依赖
  测试类一般使用Test作为类名的后缀
  测试方法使一般用test作为方法名的前缀
  测试方法必须使用@Test修饰
```java
@ContextConfiguration(locations = {"classpath:spring/spring-ds.xml"})
//不加会报java.lang.IllegalStateException: Failed to load ApplicationContext，classpath 到spring-ds.xml中间要加什么路径，需要去classes文件夹下到配置文件的路径，此时不需要@component来扫描注解@Autowired
@RunWith(value = SpringJUnit4ClassRunner.class)
public class test {
  @Autowired
  private InterfaceBizServiceImpl interfaceBizServiceImpl;

  @Test
  public void testInterface() throws Exception{
      interfaceBizServiceImpl.test(126,1);
  }
}
```
