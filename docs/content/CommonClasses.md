### Object 类

#### 概念

1. 是所有 java 类的直接或间接父类，没有 extends 的类在编译会继承 Object 类
2. 基类，超类
3. 任何类都可以使用 Object 类的方法
4. Object 可以代表任何类

#### 常用方法

1. getClass()：返回一个类的真实类型
   - instanceof 判断不一定精确,子类也会返回 true
   - 想精确可以在 if 时按特殊到普通排列
2. hashCode():返回计算后的 hash 值（hash 算法），不同对象不同
3. toString():返回全限定类名+@+hash 值
4. equals():判断地址值是否相等，基本类型则判断值是否相等

### 基本类型的包装类

### 常用方法

1. valueOf():返回包装类
2. parseXX():返回对应对的基本类型
3. 自动装箱和自动拆箱
4. jdk 为了节约内存，有常量池[-128~128) 实例化的数字都可以直接从常量池获取,不会去 new

### Number

1. 包装类的父类
2. xxValue()：转换成对应的包装类型
3. 包装类.MAX_VALUE/MIN_VALUE：常用属性

### String 类

#### 常用方法

1. int indexOf(str\[,fromIndex\])：获取从指定位置开始第一次出现的 str 的位置
2. boolean contains(str)：字符串是否包含
3. charAt(index)：获取字符串上指定位置的字符
4. equals()：比较字符串内容是否相等
5. equalsIgnoreCase(str)：忽略大小写比较字符串内容是否相等

#### 注意事项

1. String str = "ss" 相当于 new String("ss"),不可修改
2. str = "ss"+"s" 相当于 str = new String("sss")
3. StringBuilder 类线程不安全，快
   - append()：方法调用链
4. StringBffer 类线程安全，慢

### 比较器

1. 实现 Comparable<T>接口,实现 compareTo(T o)方法
2. 新建一个比较器类，继承 Comparator<T>,调用 Arrays.sort(arr,自己写的比较类)

### 其他

1. equals()和 hashCode()一般一起重写，equals 可以用到 hash 比较，更加严谨，以防内存溢出（数据创建后，程序访问不到，地址丢失）
2. 链式调用，返回 this( append().append().append )
