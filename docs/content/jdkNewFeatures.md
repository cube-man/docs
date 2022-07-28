# jdk 新特性

## lambda 表达式

```java
//R 任意类(可以不一致)
([R] a,[R] b) -> {
  //逻辑片段
  ...
  [return xx]
}
//相当于
new Xxx(){
  public xx method(R a,R b){
  ...
  [return xx]
  }
}
//一行代码时
(a, b) -> xxx
//一个形参时
a -> {xxx}
```

### 方法引用

1. 函数式接口(一个类只有一个抽象方法)
   ```java
    Function<T,R>
      R apply(T t)  //接受 T类型,返回R类型
        -- BiFunction<T,U,R>
      R apply(T t,U u) //接受T类型 & U类型,返回R类型
    Consumer<T>
      void accept(T) //接受T,无返回值
      -- Consumer<T, U>> //接受T类型 & R类型,无返回值
      void accept(T,U)
    Supplier<T>
      T get(); //无入参，返回T类型
    Predicate<T>
      boolean <T> //接受T类型,返回布尔值
   ```
2. ```java
   //也算自定义函数式接口
   new Xxx(){
     public static R method(T a){
     ...
     [return R]
     }
   }
   //相当于
   Function<T, R> var = 当前类::method
   var.apply();
   //非静态时
   实例名::method
   ```
3. 当 labmda 只有一行时，可用方法引用代替 labmda

## stream

### 特点

1. 不会自己储存元素，会沿着流一个一个执行，而非执行完后进入下一步
2. 属于声明式编程（只在意逻辑省略形参类型，返回值类型等，看方法名）
3. stream 不会改变源对象，相反会返回一个持有新结果的流
4. stream 是延迟执行的，会等到需要结果时才执行

### 使用步骤

1. 创建一个流
2. 从此状态转到下一个中间操作
3. 直至转化到终止操作获得一个结果，会强制使之前的操作强制执行，并且 stream 之后不能在使用

### 创建流

1. Collection 对象的 stream()或 parallelStream()
2. Arrays 的 stream()方法
3. Stream 的 of()、iterate()、generate()

### 中间操作

1. filter(Predicate)
2. map(Function<T,R>): 将 t 映射成 R
3. limit(int maxsize): 最多保留 size 个
4. skip(int size): 跳过 size 个
5. distinct():获取集合中不重复的元素
6. srted(\[Comparator\]): 利用比较器规则排序
7. parallel(): 使用多线程操作

### 终止操作

1. foreach(consumer): 遍历
2. min(): 求最小
3. max(): 最大
4. collect(): 转化为 list,set,map
5. count(): 获取数量
6. reduce(int init,BiConsumer): 计算
