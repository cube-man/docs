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
4. 时间日期 api
   1. java.util.Date 和 java.util.Calendar 易用性差，不支持时区，线程不安全
      1. java.text.DateFormat（线程不安全）是抽象类，需要实例化 simpledateFormat 处理时间格式化问题
      2. Canlendar 获取月份要+1
   2. jsr-310 引入的 joda-time
