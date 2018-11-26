**Groovy的特点
同时支持静态和动态类型。
支持运算符重载。
本地语法列表和关联数组。
对正则表达式的本地支持。
各种标记语言，如XML和HTML原生支持。
Groovy对于Java开发人员来说很简单，因为Java和Groovy的语法非常相似。
您可以使用现有的Java库。
Groovy扩展了java.lang.Object。**
- 范围运算符
  ```java
    class Example {
     static void main(String[] args) {
        def range = 5..10;  //..符号提供范围运算符
        println(range);
        println(range.get(2));
     }
  }
  ```
- Groovy方法
  > 1.Groovy中的方法是使用返回类型或使用def关键字定义的。方法可以接收任意数量的参数。定义参数时，不必显式定义类型。可以添加修饰符，如public，private和protected。默认情况下，如果未提供可见性修饰符，则该方法为public。
    2.如果没有值传递给参数的方法，则使用缺省值。 如果使用非默认和默认参数，则必须注意，默认参数应在参数列表的末尾定义
  ```java
    class Example {
    static def DisplayName() {
      println("This is an example of a simple method");
    }
    static void main(String[] args) {
        DisplayName();
      }
    }
  ```
- Groovy 列表
[11，12，13，14] - 整数值列表
['Angular'，'Groovy'，'Java'] - 字符串列表
[1，2，[3，4]，5] - 嵌套列表
- Groovy 映射
['TopicName'：'Lists'，'TopicName'：'Maps'] - 具有TopicName作为键的键值对的集合及其相应的值。
