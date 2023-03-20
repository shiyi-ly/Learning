### 因为工作需要，学习了Netty的基本用法，使用Netty接收设备发来的数据。
##### 官网介绍
> Netty is a NIO client server framework which enables quick and easy development of network applications such as protocol servers and clients. It greatly simplifies and streamlines network programming such as TCP and UDP socket server.

Netty是一个NIO客户端服务器框架，它能够快速、轻松地开发网络应用程序，如协议服务器和客户端。它极大地简化和流线网络编程，如TCP和UDP套接字服务器。

#### Netty的基础知识自行学习，这篇文章主要是实践
>  代码已做过测试，引入maven依赖，复制粘贴到本地可直接运行。
### Netty Client 客户端
```java
/**
 * @author shiyi
 * @version 1.0
 * @date 2020-7-9 17:12
 */
public class MyClient {


    public static void main(String[] args) throws InterruptedException {
        NioEventLoopGroup group = new NioEventLoopGroup();

        try {
            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(group)
                    .channel(NioSocketChannel.class)
                    .handler(new MyClientInitializer()); //自定义一个初始化类

            ChannelFuture channelFuture = bootstrap.connect("localhost", 9000);

            channelFuture.channel().closeFuture().sync();
        } finally {
            group.shutdownGracefully();
        }
    }
}
```
##### MyClientInitializer 初始化类
```java
/**
 * @author shiyi
 * @version 1.0
 * @date 2020-7-9 18:00
 */
public class MyClientInitializer extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        pipeline.addLast(new MyClientHandler());
    }
}
```
#####  处理器  MyClientHandler
```java
/**
 * @author shiyi
 * @version 1.0
 * @date 2020-7-9 18:02
 */
public class MyClientHandler extends SimpleChannelInboundHandler<ByteBuf> {

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        String buffer = "header" + "=" + "今天天气真好" + "," + "date" + "=" + new Date() +
                // 包尾
                "\r\n";
        ctx.writeAndFlush(Unpooled.copiedBuffer(buffer, StandardCharsets.UTF_8));
    }

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) throws Exception {
        byte[] bytes = new byte[msg.readableBytes()];
        msg.readBytes(bytes);
        String message = new String(bytes, StandardCharsets.UTF_8);
        System.out.println("客户端接收到的消息\r\t" + message);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911162405118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)
换行符，先加上，往下看，一探究竟为什么加。
### Netty Server 服务端
```java
/**
 * @author shiyi
 * @version 1.0
 * @date 2020-7-9 17:05
 */
public class MyServer {

    public static void main(String[] args) throws InterruptedException {
        NioEventLoopGroup bossGroup = new NioEventLoopGroup(1);
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new MyServerInitializer());
            ChannelFuture channelFuture = serverBootstrap.bind(9000).sync();

            channelFuture.channel().closeFuture().sync();

        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}
```
##### MyServerInitializer 初始化类
```java
/**
 * @author shiyi
 * @version 1.0
 * @date 2020-7-9 18:04
 */
public class MyServerInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        // 加入行解码器解决粘包，拆包问题（因为客户端发送信息后加了换行符）
        pipeline.addLast(new LineBasedFrameDecoder(Integer.MAX_VALUE));
        pipeline.addLast(new MyServerHandler());
    }
}
```
##### MyServerHandler 处理器
```java
/**
 * @author shiyi
 * @version 1.0
 * @date 2020-7-9 18:05
 */
public class MyServerHandler extends SimpleChannelInboundHandler<ByteBuf> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) throws Exception {
        byte[] bytes = new byte[msg.readableBytes()];
        msg.readBytes(bytes);
        // 转出字符串
        String message = new String(bytes, StandardCharsets.UTF_8);
        System.out.println("服务器接收到数据： " + message);

        ByteBuf responseByteBuf = Unpooled.copiedBuffer(UUID.randomUUID().toString(), StandardCharsets.UTF_8);
        ctx.writeAndFlush(responseByteBuf);
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        super.exceptionCaught(ctx, cause);
        ctx.close();
    }
}
```
### 测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911163539753.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)
##### 加入LineBasedFrameDecoder 行解码器，控制台打印的数据

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911163630965.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)
可以看到每次接收的数据都是完整的。
##### 不加LineBasedFrameDecoder 行解码器，控制台打印的数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911164058603.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)
可以看到数据接收不完整。

### 我是失忆，一个热爱技术的宅男，文章有任何问题您都可以在留言中指出。欢迎留言。

