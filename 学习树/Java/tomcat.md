- catalina.out、catalina.log、localhost.log </br>
1. catalina.out 是tomcat的标准输出(stdout)和标准出错(stderr)
这是在tomcat的启动脚本里指定的，如果没有修改的话stdout和stderr会重定向到这里。所以我们在应用程序中使用System.out/err打印的东西都会到这里来。另外，我们所有输出到控制台的日志也会记录进来。比如我们常用的log4j，当我们配置log4j.appender.stdout=org.apache.log4j.ConsoleAppender时，所有输出到控制台的log4j日志也会记录到Catalina.out中。所以一般情况下ConsoleAppender的日志级别需要配高一点，不然Catalina.out会显得特别臃肿，查找信息时也不太方便。比如测试的时候我一般用DEBUG级别，发生产用ERROR级别。当然你也可以关掉它 在tomcat/bincatalina.sh中配置
2. listener, filter, servlet初始化时的日志是需要看localhost.{yyyy-MM-dd}.log这个日志的。比如现在我们使用Spring，Spring的初始化我们往往是使用Spring提供的一个listener进行的，而如果Spring初始化时因为某个bean初始化失败，导致整个应用没有启动，这个时候的异常日志是输出到localhost中的，而不是cataina.out中。所以有的时候我们应用无法启动了，然后找catalina.out日志，但最后也没有定位根本原因是什么，就是因为我们找的日志不对。但有的时候catalina.out里也有我们想要的日志，那是因为我们的应用或使用的一些组件自己捕获了异常，然后将其打印了，这个时候如果恰好这些日志被我们配置成输出到console，则这些日志也会在catalina.out里出现了
3. localhost_access_log 可以利用这个日志查询访问最耗时的接口
> 总结：catalina.out即标准输出和标准出错，这里包含tomcat运行自己输出的日志以及程序向控制台输出的日志，一般程序错误我们都能在这里面找到。catalina.log是tomcat自己运行的一些日志，这些日志还会输出到catalina.out，但是应用向控制台输出的日志不会输出到catalina.log里。localhost.log主要是应用初始化(listener, filter, servlet)未处理的异常最后被tomcat捕获而输出的日志，而这些未处理异常最终会导致应用无法启动

  解决catalina文件过大问题;
  1. 代码本身考虑 Tomcat输出catalina.out文件，数据主要来源为：System.out 和 System.err 在控制台上直接输出的信息。为了减少日志输出的压力，规范代码习惯，编码时应避免使用System.out.println()和e.printStackTrace()。
  2.  Tomcat配置考虑: 通过cronolog工具进行日切