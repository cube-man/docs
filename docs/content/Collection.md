# Collection framework

## 为什么要集合

1. 数组的优缺点
   1. 优点：
      - 查询速度很快，根据偏移量访问，唯一受限于内存主频
      - 唯一可以存储多个基本元素的容器
   2. 缺点：
      - 增删慢
      - 长度初始化固定
      - 可操作方法太少，基本只能通过索引赋值
      - 只能存固定一个类型

## List

### ArrayList

1. 数组实现
2. 动态扩容
3. 查找快，增删慢（后移，前移）

### LinkedList

1. 链表实现，双向链表
2. 随机访问速度慢，增删快
3. 常用方法
   1. add(index,xx)
   2. addFirst(xx)   addLast(xx)
   3. removeFirst(xx)   removeLast(xx)

## Set

不能存放重复元素

### HashSet

1. 相当于hashMap的一半 HashMap(e,Object)
2. 查询速度相当于hashMap
3. 插入无序
4. 常用方法
   1. Object[]  toArray()

### LinkedHashSet

1. hashSet子类
2. 有序

## Map

### HashMap

1. key,value的结构
2. 查询速度快
3. key不可以重复
4. 排序无法保障
5. 一些常用方法
   1. put(k,y)
   2. get(k)
   3. getSet()      获取全部k