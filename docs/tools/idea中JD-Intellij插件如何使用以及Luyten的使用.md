> 我用双手成就你的梦想
###  反编译工具使用
##### 一、IDEA中JD-Intellij插件的使用
* file->Settings->Plugins
* 因为我下载过，所以显示是灰色的，下载后需重启IDEA![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526142617337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70)
* 使用很简单，一条命令就可以实现反编译
```java
java -cp "C:\shiyi\software\IDEA\IntelliJ IDEA 2019.3.3\plugins\java-decompiler\lib\java-decompiler.jar" org.jetbrains.java.decompiler.main.dec
ompiler.ConsoleDecompiler -dgs=true beaver-ms-business-1.0-SNAPSHOT.jar data
```
* **语句解析**
* -cp  <目录和 zip/jar 文件的类搜索路径>
* "C:\shiyi\software\IDEA\IntelliJ IDEA 2019.3.3\plugins\java-decompiler\lib\java-decompiler.jar"  IDEA软件安装目录中找到插件的jar包
* ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526143514861.png)
* beaver-ms-business-1.0-SNAPSHOT.jar 要反编译Jar包的名字
* data 反编译后文件的位置
* 执行后会出现图中所示即反编译成功
* ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526143730494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70)
### 二、Luyten的下载使用
* [github地址](https://github.com/deathmarine/Luyten)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526143934286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70)
* 选择exe下载并使用![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052614395763.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70)
* File -> open File -》找到要反编译的jar包
* ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526144235562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70)
* **反编译成功**