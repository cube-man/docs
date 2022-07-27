# 高并发编程

## 线程池（jdk1.5）

1. 防止对象的频繁创建与销毁（耗时）
2. 当线程执行完后，会重新放回线程池中，使得该线程在后续的任务中重用
3. 线程池中线程相对固定和有限，不会因为太多的任务而创建过多的线程而导致 OOM
4. 当线程繁忙时，任务会放在队列中，直到有线程空闲时间，再将队列中的任务分配给他

## 线程池的常见类

1. Executor：线程池的顶级接口
2. ExecutorService：线程池接口
3. Excutor 工厂类：可以通过此类获得一个线程池
4. Executors：线程池工具类

## Executors

### 常用方法

- 线程池对象用 ExecutorService 接收

1. newFixecThreadPool(线程数（一般是处理器数-1，个别组件会处理器数+1）)

   1. submit(Runnable): 用来提交任务，线程数达到上限时，后面的任务需等待线程
   2. shutdown(): 等来所有任务执行完成后，才会关闭线程池

- 两个时间表类，可做定时任务

2. newCachedThreadPool()：懒汉式，随着任务提交数增多，闲置时回收销毁，不适用变化较快的场景
3. newScheduledThreadPool(线程数)：创建具有指定线程数的定时任务线程池，即使空闲也不会回收销毁
   1. scheduleAtFixedRate(Runnable command,long initDelay,long period, TimeUnit unit): 延迟 delay 后，开始提交任务，间隔 period 个 unit 单位后，再提交下一个任务，如果任务执行时长超过间隔，那么下一个任务延迟到此任务完成后立即执行
   2. scheduleWithFixedDelay(Runnable command,long initDelay,long period, TimeUnit unit):延迟 delay 后，开始提交任务,任务完成后，间隔 period 个 unit 单位后，再提交下一个任务。

## Callable & Future

1. submit(Callable):提交一个有返回值的任务，返回一个 Future<T>对象
2. future.get(): 获取返回的值，阻塞的，任务执行完则返回，没执行完则阻塞直至任务执行完获取值。

## 异步

- 同步，要等耗时任务结束以后，才能继续往下执行
- 异步，提交一个耗时任务以后，程序依然会往下执行

### CompleteFuture

1. java.util.concurrent 包下
2. runAsync(Runnable): 以异步提交一个无返回值任务，后可以接 thenRunAsync(Runnable),接着下一步执行，这样可以严格保持顺序
3. supplyAsync(): 以异步提交一个有返回值的任务
4. allOf(task1,task2,...).thenRun(Runnable)：其中的 task 都完成后，执行 thenRun 内的方法
5. anyOf(task1,task2,...)：返回先完成的任务的值
6. get(long timeout,TimeUnit unit):在时间内等待，超时抛出 TimeoutException()异常，try catch 包裹，在 catch 中写补偿逻辑
