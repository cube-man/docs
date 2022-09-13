# 面试题

1. java 之中有值传递(求值策略)
   1. java 传递参数时：
      - 基本类型传递的是值，当修改了方法内的值时，对方法外原来的值不会有影响
      - 引用类型传递的是地址的拷贝，而非引用，当对传入的地址值重新赋值之后，不会改变的传入的变量的地址，与原来对象的关系会被切断，对方法内的变量修改不会影响到外部的变量。
   - 值传递，传递的是值，如地址值，而非地址的引用
   - 引用传递，传递的是地址的引用，如指针，而非值
2. 类的初始化
   1. 先父类静态变量 -> 然后子类静态变量 -> 父类构造器 -> 子类构造器
3. try catch finally
   1. 执行顺序
      - 正常：try return 前先执行 finally,若 finally 有 return,则会提前执行，finally 中改变返回的值，try 中 返回的仍然是原来的值,细节看 class,jvm,进入 finally 前会使用 iload,istore 将结果暂存。直接在 finally 返回可以改变
      - try catch finally

## time 相关

1. 时间日期 api
   1. java.util.Date 和 java.util.Calendar 易用性差，不支持时区，线程不安全(但观察多数项目仍使用次类)
      1. java.text.DateFormat（线程不安全）是抽象类，需要实例化 simpledateFormat 处理时间格式化问题
      2. Canlendar 获取月份要+1
   2. jsr-310 引入的 joda-time
      1. jdk 的时间类包括（不可变的，线程安全的）
         - LocalDate: 年月日
         - LocalTime: 精度纳秒，时分秒
         - Instant: 时间线的瞬间点，涉及润秒
         - Duration: 基于时间的时间量
         - Period: 时间量：如 2 年 3 个月 4 天，表示一段时期

## 多态

## 网络相关

### http & https

1. 概念
   - Http: Hypertext Transfer Protocol
   - Https: Sercure Hypertext Transfer Protocol
2. https 是什么
   1. 基于 http 开发，用于客户端与服务器交换信息，使用安全套接字层（SSL）进行信息交换，简单来说 http 的安全版

## 线程相关

1. ThradLocal 能在同一线程共享数据,是一个 kv 结构，k 与线程对象绑定在一起，能在同一个线程中共享数据

## IO 相关

1. 同步阻塞的 bio,同步非阻塞的 nio,异步非阻塞的 aio
2. bio：1.4 之前的建立网络连接采用 bio,需要在服务端先启用一个 ServerSocket,然后客户端启用 socket 进行通信
