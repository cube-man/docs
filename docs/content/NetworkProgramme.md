# 网络编程

## OSI 七层和 TCP/IP 四层

![osi&tcp/ip](../img/osi%26tcpip.png)

## TCP

- 安全，低效，有序
- 有主次之分，分为服务方和客户方
- TCP
  - Transform 传输
  - Control 控制
  - Protocol 协议

1. 服务器接收：ServerScocket
2. 客户端发送: Socket
3. ServerSocket 可以产生多个 socket 对象与 c 端通信
4. 发送数据帧

## UDP

- 不安全，高效的，无序

1. DatagramSocket: 创建报文的 socket

2. DatagramPacket: 存放数据的数据包裹，满时返回数据

   1. receiver

   ```java
    try (
                   DatagramSocket ds = new DatagramSocket(8080);
           ) {
               byte[] buffer = new byte[1024]; // 1kb
               // 真正用来盛放数据的数据包裹
               DatagramPacket dp = new DatagramPacket(buffer, buffer.length);
               // 当buffer被接收的数据填充满的时候，就会return
               ds.receive(dp); // 当没有接收到消息的时候，会一直阻塞

               // 服务器端是可以获取到客户端的地址信息
   //            dp.getAddress(); // 获取对方IP
   //            dp.getPort(); // 获取端口

               int len = dp.getLength(); // 真正的接收到数据的长度
               byte[] data = dp.getData(); // 它的长度与buffer的长度是一样
               String msg = new String(data, 0, len);
               System.out.println(msg);
           } catch (Exception e) {
               e.printStackTrace();
           }
   ```

   2. sender

      ```java
      try (
                      DatagramSocket socket = new DatagramSocket()
              ) {
                  byte[] msg = "hello world".getBytes();
                  // 在发送的数据报文中，封装目标的地址信息
                  DatagramPacket dp = new DatagramPacket(msg, msg.length, InetAddress.getByName("127.0.0.1"), 8080);
                  // while
                  socket.send(dp);

                  // 发送的数据传输完毕的时候，发送一个类似于Q的表示
              } catch (Exception e) {
                  e.printStackTrace();
              }
          }
      ```
