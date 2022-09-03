# 秒杀案例

## 需求分析

1. 顾客点击按钮，购买商品
2. 可能有多个同时购买
3. 商品数量固定
4. 库存不足时不能购买

## v1.0

### 概要设计

1. 粗略的版本：

   - 用户如果余额不足不能购买，如果购买成功需要对应的减少账户金额
   - 库存要相应减少

### 1.1 建模

1. 用户表：用于记录个人信息，该网站的唯一标识
2. 用户账户表： 用于记录一个时刻用户的余额
3. 产品表：用于记录商品的基本信息，秒杀目标
4. 订单表：用于用户购买商品后，记录商品与用户的联系

### 1.2 概要设计

- 将需求翻译成开发人员自己的语言
- 界面原型：axure,ui,前端
- 后台程序接口：后端
- 为什么用接口
  - 接口可以通过 swagger、伪接口语言，将接口转化为其他岗位能看懂的内容
- 前端用户 api：用户需要用的 api,用户在操作时需要用的阿皮

```java
/**
 * <p>前端用户接口，该类主要用于前端请求直接调用的</p>
 */
public interface SecondKillApi {

    // 我们首先把所有的有可能要调用的方法都汇总在这里，后面再根据需要划分为不同的模块

    /**
     * <p>主要用来呈现秒杀某商品的首页，主要用来呈现以下信息：</p>
     * <ul>
     *     <li>商品名称</li>
     *     <li>商品库存</li>
     *     <li>商品单价</li>
     * </ul>
     * @param productId 商品的主键
     * @return 包含以上字段的一条商品记录
     */
    Product findBy(Integer productId);

    /**
     * <p>该方法是用户秒杀入口</p>
     * @param productId 商品的id
     * @param userId 登录用户的id
     */
    void kill(Integer productId, Integer userId);

}

```

- 用户接口：要实现用户功能，所需要实现的 api

```java
/**
 * <p>订单相关操作</p>
 */
public interface OrderApi {
    /**
     * <p>新增一条购买记录</p>
     * @param order 订单信息
     */
    void saveOrder(Order order);
}

/**
 * <p>商品相关操作</p>
 */
public interface ProductApi {
    /**
     * <p>查询商品基本信息</p>
     * @param productId 商品id
     * @return 商品基本信息
     */
    Product findProductById(Integer productId);
    /**
     * <p>更新商品的库存</p>
     * @param product 商品实例
     */
    void updateProduct(Product product);
}

/**
 * <p>用户账户相关操作</p>
 */
public interface UserAccountApi {
    /**
     * <p>查询商品基本信息</p>
     * @param userId 用户账户的id
     * @return 用户账户信息
     */
    UserAccount findUserAccountById(Integer userId);




    /**
     * <p>更新账户的余额，实现扣款</p>
     * @param userAccount 用户账户实例
     */
    void updateUserAccount(UserAccount userAccount);

}
```

### 1.3 详细设计

- 将笼统的用户接口继续细化拆分，最终将所有的方法都大致定义出来，然后将这些方法要做什么，都写好文档
  ![01](../img/01.PNG)
- web 层：处理前端用户的请求
  - api 中的接口：用于于前端对接，交付
  - controller：api 的实现类
- service 层：业务逻辑层，用于对模型，特定的领域，表做一些聚合度高的操作

### 1.4 编码

1. 根据接口和实现类的规划，完成 v1.0.0 的需求
2. 在没有特殊的情况下，可以将都往外抛，最后在进行集中分类处理

### 1.5 事务处理

1. 在一次请求中，出现了多次的查询与修改，按以前的方法只能将 con 上升至 controller 层上，或者将 controller 下潜的 dao 层中，但这样并不符合它们的定位，controller 应该负责转发请求，dao 层则负责数据库相关操作
2. 多次 sql 操作需要在一次请求中，要在最开始时开启事务，在最后一步 sql 操作才进行提交

## v2.0 切面处理

1. 切面的来由：在项目中发现有部分逻辑与业务无关，分布在各个模块间，代码形态基本固定，他们出现的位置有先后顺序和位置，他们每个代码段中出现的形态像一个切面。
2. 将这些公共的部分提炼出去，让所有模块共用，这种编程方式我们称之为面向切面编程`aop`
3. 在秒杀的案例之中，可以将与代码没有直接关联的部分，独立出来，形成切面`aspect`(事务管理的 getConnection,commit,rollback)

```java
  public class TransactionManager {

    // Aspect-Oriented Programming
    // 但凡是与具体业务逻辑不是直接关联的代码，我们称之为切面(aspect)
    // 这样的逻辑一般都具有平台通用性

    // 以下三个方法都是事务管理当中的分解动作

    public void beginTransaction() throws SQLException {
        Connection con = DbUtils.getConnectionInLocal();
        con.setAutoCommit(false);
    }

    public void commit() throws SQLException {
        DbUtils.getConnectionInLocal().commit();
    }

    public void rollback() throws SQLException {
        DbUtils.getConnectionInLocal().rollback();
    }

}
```

4. 在 dao 中，上述方法出现的位置基本固定，可以用`模板方法模式`来封装一个类

```java
public class TransactionTemplate {

    private final TransactionManager manager = new TransactionManager();

    public Result doInTransaction(DoInTransaction task) {
        Result result = null;
        try {
            manager.beginTransaction();// 开始事务
            // 具体的DML操作
            result = task.doInTransaction();
            manager.commit();// 提交
        } catch (Exception e) {
            e.printStackTrace();
            try {
                manager.rollback();// 回滚
            } catch (SQLException ex) {
                e.printStackTrace();
            }
        } finally {
            // 释放资源
        }

        return result;
    }

}
```

5. 只有中间 task 执行的部分不确定，通过接口传入需要执行的方法

```java
public interface DoInTransaction {

    Result doInTransaction() throws Exception;

}
```

6. 需要执行一整个完整事务时，通过实现接口中的 doInTransaction()方法
   传入事务

```java
@Override
    public Result kill(Integer productId, Integer userId) throws SQLException {

        Result result = template.doInTransaction(() -> {
            // 在此处编写秒杀各个环节所需要做的事情
            return null;
        });
        return result;
    }
```

## v3.0 多线程竞争

### 1. 造数

1. 批处理造数

### 2. 线程多开的模式

1. 事务多开的模式
   - Thred 子类
   - 实现 Runnable 接口
2. 实现 Runnable 接口的形式
   1. 当开辟线程时，会雇佣一个工人来为我们工作，这个工人对应到了 cpu 的 process,当程序运行时，我们会把一个具体的工作(task)交个具体的工人(thread)执行，
3. 写法
   1. 匿名内部类
   2. lambda

### 3. 线程池

1. 在多开线程时，我们要在执行时 new 一个新的线程，在任务执行后将线程释放掉，当线程多时这样反复的申请释放会相当的消耗资源,`线程池`就是为了解决这样线程频繁创建与销毁的问题
2. 线程池：先创建一个容器，申请好内存空间，申请足够的工人，当有人提交了任务时，将任务交个空闲的工人去执行，若所有工人在忙，将任务存在一个等待队列中，直到有工人空闲，则将队列中的任务交给该空闲的工人执行，工人即使没有任务，也不会释放掉，而是在线程池中等待下个任务
3. 创建线程池的几种方法

```java
Executors.newFixedThreadPool(number);//固定线程数
Executors.newSingleThreadExecutor();//单个线程
Executors.newCachedThreadPool();//带缓存

Executors.newScheduledThreadPool(3)//定时任务线程池
scheduled.schedule(()->{}, 3 , TimeUnit.SECONDS); // 3秒后执行
scheduled.scheduleAtFixedRate(); // 按照固定的频率来执行
scheduled.scheduleWithFixedDelay();// 按照固定的延时来执行

CompletableFuture//有复杂的衔接关系适用
```

4. 多任务等待
5. 竞争问题
6. 超时任务：任务执行超过一定时间后的补偿

### 4. 多线程考点

1. 线程的生命周期 ( 5 个状态 )
   - 初始 -> 就绪/阻塞 <-> 执行 -> 终止
2. 线程的实例化方法，优缺点
   1. Thread 子类
   2. Runnable 实现类
   3. 线程池
3. sleep & wait
   - sleep 是休眠，在休眠期间不会释放锁
   - wait 是等待，若执行等待会将锁给释放掉，其他线程能够获取到锁
4. sync 的工作原理
5. 显式锁和隐式锁
   1. 因为是多线程操作，一个事务中多个 sql 查询与更新之间有间隔，有可能会有其他的线程在查询与插入中间执行了插入，会导致一个商品被两个人买了的情况，所以需要加锁来保证操作的原子性
6. DB 的执行写操作不是并行的，是序列化的，有先后顺序的

## v4.0 悲观锁与乐观锁

1. 使用同步锁后，线程没有结束方法时，其他线程无法执行该方法，保证了操作的原子性
2. 悲观锁:以悲观的态度来防止冲突，会在操作数据时，将数据锁住，只有当它主动释放了锁，下一个人才可以再次操作，一般数据库都是基于悲观锁机制实现的
   - 特点：可以保证数据的独占性和完整性，每次请求都会对数据加锁，然后对数据库操作，方法完成后才会释放锁，加锁和释放锁的过程都会造成消耗，所以效率不高
   - 缺点：加锁与释放锁消耗的资源，会将大量的资源一并锁住
3. 乐观锁：乐观锁对数据冲突采取一种乐观态度，不会在运行期间对数据加锁，使得多个任务可以并行进行，只有在修改时才会通过一种机制确认数据是否一致，一致则可以修改（一般是通过比对版本号来确定，或者是 CAS 算法）
   - 乐观锁是一种并发类型的锁，不对数据进行加锁，而是通过业务实现了锁的功能，不加锁实现多个请求同时访问，也省去了加锁和释放锁的消耗，可以一定程度提高操作的性能，不过在并发非常高的情况下，会导致大量的请求冲突，导致整个情求的大部分资源的浪费，在高并发中反而不如悲观锁
   - 可能会导致 ABA 的情况: A 查询，B 改数据 a 为 b,B 改数据 b 为 a,因为数据任然为 a,所以下一步 A 的修改操作仍然可以成功
   - 只能对单一变量加锁
   - 自旋会导致额外的开销

## v5.0 重试

1. 在多线程操作中，为了用户体验，冲突之后，可以进行一定次数的重试
2. 重试的实现，在失败后，记录次数，若次数小于规定重试次数，可以 new 一个线程让它重试
