### 模拟面试小白：
需求：有一台没有网络的服务器，使用docker部署war包，没有tomcat镜像，限时30分钟。

小白：没网部署war包干啥，是不是工作不饱和让你们太闲了。

我：傻瓜，怪我没表达清楚，是有专用的vpn，无法访问外网。

小白：巴拉巴拉...话都说不清楚

我：别皮了，快说，你思路是啥，要怎么做

小白：能百度吗..我看你像百度，能docker pull tomcat吗，不能...那我不会了。

我：hhh，你要会我岂不是显的很尴尬，我讲给你听。

### 思路

首先部署war包，使用我们最常用的web容器tomcat，把war包放到webapps目录下，启动tomcat时会自动解压解析。我们可以通过ip+port+path访问。

小白：你说的这些我也想到了，但现在的问题是服务器访问不了外网，不能直接通过docker pull的方式拉取tomcat镜像，那怎么办呢？

别急，听我说：我们可以在别的电脑上制作好镜像，然后把镜像转存并上传到无法访问外网的服务器，直接运行就可以了。

#### 前置准备：
#### 一、下载jdk包
#### 二、下载tomcat包（一定下载tar.gz包，踩坑的教训）
#### 三、 把jdk和tomcat上传到同一目录。然后编写Dockerfile文件。 目录为/shiyi/mydocker  (目录可自定义)
#### 四、创建Dockerfile文件，并编写内容

Dockerfile文件内容
```shell
# vim Dockerfile

FROM centos
MAINTAINER shiyi<1282760680@qq.com>
# 会自动解压到指定目录，不需要人为去解压
ADD jdk-8u333-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-8.5.81.tar.gz /usr/local/
RUN chmod a+x /usr/local
ENV MYPATH /usr/local
WORKDIR $MYPATH
ENV JAVA_HOME $MYPATH/jdk1.8.0_333
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME $MYPATH/apache-tomcat-8.5.81
ENV CATALINA_BASH $MYPATH/apache-tomcat-8.5.81
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
# 把war包放到webapps目录下
ADD xxxxs.war $CATALINA_HOME/webapps
EXPOSE 8080
WORKDIR $CATALINA_HOME/bin
RUN chmod a+x $CATALINA_HOME/bin/startup.sh
VOLUME ["$CATALINA_HOME/logs/","$CATALINA_HOME/webapps"]
# 注意：tail -F ,这里如果用小写的f，启动容器的时候打印完日志后会退出，用大写-F容器才会停留
CMD $CATALINA_HOME/bin/startup.sh && tail -F $CATALINA_HOME/logs/catalina.out
```
#### 五、根据Dockerfile制作tomcat镜像
```shell
docker build -f /shiyi/mydocker/Dockerfile -t shiyitomcat:1.0 /shiyi/mydocker
```
看到successful则代表打包成功
查看镜像
```shell
docker images
```
启动tomcat
```shell
docker run -itd -p 8080:8080 shiyitomcat:1.0
```
查看tomcat启动日志
```shell
docker logs xxx(容器id)
```
访问：ip+port+path访问项目

小白，是不是很简单呢..

小白：我要放到 没网的那台服务器跑呀，你别在这里部署！！！

把镜像转存就可以了，是不是傻。

#### 六、转存镜像
```shell
docker save -o mytomcat_image.docker mytomcat:1.0
```
#### 七、还原镜像
```shell
docker load -i  mytomcat_image.docker mytomcat
```
##### 我是失忆，一个热爱技术的宅男，文章有任何问题您都可以在留言中指出。欢迎留言。也可以加我个人微信一起学习，一起进步！ 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201021180201743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)
