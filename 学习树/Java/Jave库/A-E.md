[TOC]

#### Lombok
- @builder
  ```java
  UserInfo userInfo = UserInfo.builder()
        .name("zzl")
        .email("bgood@sina.com")
        .build();
  ```
  > Builder声明实体，表示可以进行Builder方式初始化
- @Value
  > 注解，表示只公开getter，对所有属性的setter都封闭，即private修饰，所以它不能和@Builder现起用
- @Data
  > 注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual  、hashCode、toString 方法
- @Setter ：注解在属性上；为属性提供 setting 方法
- @Log4j ：注解在类上；为类提供一个 属性名为log 的 log4j 日志对象
- @NoArgsConstructor ：注解在类上；为类提供一个无参的构造方法
- @AllArgsConstructor ：注解在类上；为类提供一个全参的构造方法
- @Cleanup : 可以关闭流
- @Builder ： 被注解的类加个构造者模式
- @Synchronized ： 加个同步锁
- @SneakyThrows : 等同于try/catch 捕获异常
- @NonNull : 如果给参数加个这个注解 参数为null会抛出空指针异常
- @Value : 注解和@Data类似，区别在于它会把所有成员变量默认定义为private final修饰，并且不会生成set方法。


#### BeanUtils
  > public static void copyProperties(Object dest, Object orig) 如果存在名称不相同
   的属性，则BeanUtils不对那些属性进行处理
