# IO

## File 类

1. 文件操作对象（表示操作硬盘上的文件或文件夹）
2. 常用方法
   1. createNewFile() 创建新文件，有则覆盖
   2. mkdir() 创建文件夹，没有父文件夹则不创建
   3. mkdirs() 创建文件夹，没有父文件夹则将父文件夹一起创建
   4. delete() 删除文件或空文件夹
   5. exists() 判断是否文件或文件夹存在
   6. lenght() 获取文件大小（字节）
   7. ....

## FileNameFilter 接口

1. 只有 boolean accept(File pathname)
2. 当 file[] = list.listFile(FileNameFilter 接口实现类（匿名内部类也可）),对文件进行过滤，true 才可以进入 file[]

## 流的分类

1. 按方向（以应用程序为方向）
   1. 输入流
   2. 输出流
2. 按单位
   1. 字节流
   2. 字符流
3. 按功能
   1. 节点流：具有实际传输数据的读写功能
   2. 处理流：在节点流的基础上增强功能

## 一些流

1. In/OutputStream(): 抽象输入/输出流
2. FileIn/OutputStream(): 文件输入/输出流
3. BufferedIn/OutputStream(): 提高 IO 效率，减少硬盘访问次数
