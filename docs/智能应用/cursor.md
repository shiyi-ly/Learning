![在这里插入图片描述](https://img-blog.csdnimg.cn/787a80c8fa08473d9bf72ffb9f949841.png)
**官网** ：https://www.cursor.so

**IDE作者**： https://twitter.com/amanrsanger

**github地址**：https://github.com/getcursor/cursor

**工欲善其事必先利其器**，无意间在github上发现的超好用的代码生成软件（免费！但愿收费来的迟亿点）


### 基本使用
Ctrl+k  编写代码
Ctrl+l 聊天模式

### 小试牛刀
1. 基于SpringBoot帮我写一个用户管理
![在这里插入图片描述](https://img-blog.csdnimg.cn/26b0076bbb7245ab996e4d1ef13e824a.png)

```java
// 基于SpringBoot帮我写一个用户管理
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/")
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.getUserById(id);
    }

    @PostMapping("/")
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.updateUser(id, user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
    }
}

```
2. 基于springboot,mybatis plus帮我写一个根据名称模糊查询用户信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/72ed6fac28d2432b95750d524a433933.png)

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/search")
    public List<User> searchUsers(@RequestParam("name") String name) {
        return userService.searchUsersByName(name);
    }
}

@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {

    @Override
    public List<User> searchUsersByName(String name) {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.like("name", name);
        return list(queryWrapper);
    }
}

public interface UserMapper extends BaseMapper<User> {
}

```
3. Ctrl+l 基于springboot，帮我写一个集成阿里oos文件上传和下载的功能
```java
Sure, I can help you with that! To integrate with Alibaba Cloud Object Storage Service (OSS) for file upload and download in a Spring Boot application, you can use the official Alibaba Cloud OSS SDK for Java.

First, you need to add the SDK dependency to your pom.xml file:

<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.10.2</version>
</dependency>


Then, you can create a com.aliyun.oss.OSS object to interact with the OSS service. You will need to provide your AccessKeyId, AccessKeySecret, and Endpoint information to create the object. You can store these values in your application.properties file and retrieve them using the @Value annotation.

Here's an example of how to upload a file to OSS using the SDK:

import com.aliyun.oss.OSS;
import com.aliyun.oss.OSSClientBuilder;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;
import java.io.IOException;
import java.util.UUID;

@Service
public class OssService {
    @Value("${aliyun.oss.file.endpoint}")
    private String endpoint;
    @Value("${aliyun.oss.file.keyid}")
    private String keyId;
    @Value("${aliyun.oss.file.keysecret}")
    private String keySecret;
    @Value("${aliyun.oss.file.bucketname}")
    private String bucketName;

    public String uploadFile(MultipartFile file) {
        // Create OSS client
        OSS ossClient = new OSSClientBuilder().build(endpoint, keyId, keySecret);

        try {
            // Generate a unique file name
            String fileName = UUID.randomUUID().toString().replaceAll("-", "") + file.getOriginalFilename();

            // Upload file to OSS
            ossClient.putObject(bucketName, fileName, file.getInputStream());

            // Return the URL of the uploaded file
            return "https://" + bucketName + "." + endpoint + "/" + fileName;
        } catch (IOException e) {
            e.printStackTrace();
            return null;
        } finally {
            // Close the OSS client
            ossClient.shutdown();
        }
    }
}


To download a file from OSS, you can use the com.aliyun.oss.model.OSSObject
```

**最后一问：你这么智能，我有点怕失业。**

Thank you for the compliment, but I don't think you have to worry about losing your job to me. Programming is a collaborative effort, and we all have our strengths and weaknesses. If you have a specific question or problem you need help with, I'm happy to assist in any way I can.

谢谢你的夸奖，但我认为你不必担心我会丢掉你的工作。编程是一项协作工作，我们都有自己的长处和短处。如果您有具体问题需要帮助，我很乐意尽我所能提供帮助。


##### 亦喜亦忧，多个学科领域挑战与机遇并存。
##### 这么强大的IDE，快去试试吧。
##### 我是失忆，一个热爱技术的宅男，文章有任何问题您都可以在留言中指出。欢迎留言。也可以加我个人微信一起学习，一起进步！ 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201021180201743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)