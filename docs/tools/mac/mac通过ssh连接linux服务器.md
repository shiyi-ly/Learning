# 方法一 自带 terminal 常规连接

1、 打开 Mac 终端，通过 ssh 命令连接 linux 服务器
`ssh root@127.0.0.1`
root是账户名，@后面的是连接的ip地址

然后根据提示输入密码即可成功

# 方法二：使用别名连接(无法记住密码)

1、设置一个别名快捷访问，编辑别名配置文件，使用 vi 命令编辑一下 ~/.ssh/config 这个文件（如果目录下没有这个文件，可以新建一个）
`vi ~/.ssh/config`
2、按格式添加内容：

```shell
# 服务器1
host 别名
    hostname IP地址
    port 22
    user 用户名
    
# 服务器2
host 别名
    hostname IP地址
    port 22
    user 用户名
```

3、查看已设置的别名
`cat ~/.ssh/config | grep "host"`
4、使用方式
打开终端输入`ssh 别名`按照提示输入密码即可

# 方法三：记住密码方式

1、新建 vm001.ssh 文件
`vi vm001.ssh`
2、添加如下内容

```shell
#!/usr/bin/expect -f
set user thinkey 
set host 10.1.190.92
set password 000000 
set timeout -1

spawn ssh $user@$host
expect "password:*"
send "$password\r"
interact
```

3、使用方法
`expect vm001.ssh`

4、使用终端的描述文件功能，实现点击直接连接

4.1、打开终端，进入偏好设置 > 描述文件

4.2、选择你想要使用的终端样式，点击下方小齿轮图标，选择复制描述文件，给复制后的描述文件设置一个通俗易懂的名称



[![Alt text](https://cdn.wangtwothree.com/imgur/ZSwyixN.png)](https://cdn.wangtwothree.com/imgur/ZSwyixN.png)



4.3、点击复制后的描述文件，在 shell 标签页下，选择运行命令，输入以下命令

```
expect vm001.ssh
```

> expect 后最好跟vm001.ssh的全路径



[![Alt text](https://cdn.wangtwothree.com/imgur/c9hOQCY.png)](https://cdn.wangtwothree.com/imgur/c9hOQCY.png)



4.4、以后再连接服务器就可以直接点击连接了，终端菜单中选择shell > 新建窗口或新建标签页 > 选择你自己添加的终端即可



[![Alt text](https://cdn.wangtwothree.com/imgur/lx6Zmed.png)](