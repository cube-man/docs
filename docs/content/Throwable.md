# Throwable

1. throw:用于手工抛出异常
2. throws:用来声明该方法可能会产生异常（跟在方法后）
3. try catch finally 中都返回值最后会返回 finally 里的值
4. 最重要的是异常的名称

## Exception

### RuntimeExcetion

1. 不受检测的异常，可以不用处理，也不需要抛出
2. 可以继承 RuntimeException 编写自己的运行时异常类

### 其他

1. 受检测的异常，需要 try(){}catch(xxException e){}处理，或者向上抛出异常

## 异常的处理

1. try catch 解决
2. throws 抛出

## 自定义异常

1. 继承异常类
2. 构造器使用父类的 String 构造器,传一个错误消息

## Error

1. jvm 的错误
