## 在MacOS平台，使用ssh登录linux[服务器](https://cloud.tencent.com/product/cvm?from=10680)后，在后台放置一段时间，就会自动断开，解决的方法如下 
在MacOS平台，使用ssh登录linux[服务器](https://cloud.tencent.com/product/cvm?from=10680)后，在后台放置一段时间，就会自动断开，解决的方法如下 ：

```js
vim /etc/ssh/ssh_config
```

复制

添加下面两条设置:

```js
ServerAliveCountMax 3
ServerAliveInterval 5
```

复制

- `ServerAliveCountMax 3` 表示服务器发出请求后，客户端没有响应的次数达到一定值, 就自动断开。正常情况下, 客户端不会不响应。
- `ServerAliveInterval 5` 指定了服务器端向客户端请求消息的时间间隔，默认是0，不发送。而`ServerAliveInterval 5`表示每5秒向服务器发送一次，这样就保持长连接了。

`/etc/ssh/` 目录下除了`ssh_config`之外，还有一个`sshd_config`，二者的区别在于，前者是针对**客户端**的配置文件，后者是针对**服务端**的文件，因为我们是作为客户端去远程连接其他服务器，所以修改ssh_config文件就好了。