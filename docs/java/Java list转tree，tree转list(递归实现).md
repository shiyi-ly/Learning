> 下班回家途中的风景。生活不止眼前的苟且，还有到不了的远方和看不见的风景。
> 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200718094108523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjE2Nzkx,size_16,color_FFFFFF,t_70#pic_center)

### 实现简易的留言板，list转换成树，树转出成list。
**表结构如下**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200718092402971.png)
根据不同的场景设计表结构，如果像微信公众号留言那种，可以添加一个文章id。

**表中的留言信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200718092443875.png)
**controller层**
```java
/**
 *
 * @author shiyi
 * @since 2020-07-16
 */
@RestController
@RequestMapping("/message")
public class MessageController {

    @Autowired
    private MessageService service;

    @RequestMapping("/queryMessageByCode")
    public JsonResult queryMessageByCode(@RequestParam("code") String code) {
        return JsonResult.success(service.queryMessage(code));
    }

}
```
**service接口层**
```java
/**
 *
 * @author shiyi
 * @since 2020-07-17
 */
public interface MessageService extends IService<MessageEntity> {

    List<MessageEntity> queryMessage(String code);
}
```
**service实现层**
```java
/**
 *
 * @author shiyi
 * @since 2020-07-17
 *
 */
@Service
@Transactional
public class MessageServiceImpl extends ServiceImpl<MessageMapper, MessageEntity> implements MessageService {

    @Override
    public List<MessageEntity> queryMessage(String code) {
        /*
         * 1.查询出所有留言
         * 2.查询出指定code的留言为msgList
         * 3.遍历msgList，找出它所有的子集
         * 4.如果需要返回的是集合不是树
         * 5.把树转换成集合
         */
        List<MessageEntity> messageList = new ArrayList<>();
        // 查询出所有留言
        List<MessageEntity> allList = this.list();

        if (allList != null && list().size() > 0) {
            // 筛选出要指定的人
            List<MessageEntity> msgList = allList.stream().filter(x -> x.getCode().equals(code)).collect(Collectors.toList());
            if (msgList.size() > 0) {
                // 遍历msgList，找出它所有的子集
                for (MessageEntity msgEntity : msgList) {
                    // 把集合转换成树结构
                    List<MessageEntity> treeList = messageListToTree(msgEntity, allList);
                    messageList.addAll(treeList);
                }
            }
        }

//        return treeToList(messageList);
        return messageList;
    }

    private List<MessageEntity> treeToList(List<MessageEntity> messageList) {
        List<MessageEntity> result = new ArrayList<>();
        for (MessageEntity entity : messageList) {
            result.add(entity);
            List<MessageEntity> childMsg = entity.getChildMsg();
            if (childMsg != null && childMsg.size() > 0) {
                List<MessageEntity> entityList = this.treeToList(childMsg);
                result.addAll(entityList);
            }
        }
        if (result.size() > 0) {
            for (MessageEntity entity : result) {
                entity.setChildMsg(null);
            }
        }
        return result;
    }

    private List<MessageEntity> messageListToTree(MessageEntity msgEntity, List<MessageEntity> allList) {
        List<MessageEntity> msgList = new ArrayList<>();
        for (MessageEntity messageEntity : allList) {
            if (messageEntity.getParentId().equals(msgEntity.getId())) {
                if (msgEntity.getChildMsg() == null) {
                    msgEntity.setChildMsg(new ArrayList<>());
                }
                msgEntity.getChildMsg().add(findChild(messageEntity, allList));
            }
        }
        msgList.add(msgEntity);
        return msgList;
    }

    private MessageEntity findChild(MessageEntity messageEntity, List<MessageEntity> allList) {
        for (MessageEntity entity : allList) {
            if (entity.getParentId().equals(messageEntity.getId())) {
               if (messageEntity.getChildMsg() == null) {
                   messageEntity.setChildMsg(new ArrayList<>());
               }
               messageEntity.getChildMsg().add(findChild(entity, allList));
            }
        }
        return messageEntity;
    }
}

```
**具体是返回树还是返回集合，和前端妹子协商，现在前端用的框架，返回集合自己会转换成树。postman访问返回树的结构**。
```java
{
    "status": 0,
    "message": "OK",
    "data": [
        {
            "id": 1,
            "parentId": 0,
            "code": "shiyi",
            "name": "失忆老幺",
            "content": "哇，美女",
            "childMsg": [
                {
                    "id": 2,
                    "parentId": 1,
                    "code": "zs",
                    "name": "张三",
                    "content": "渣男",
                    "childMsg": [
                        {
                            "id": 4,
                            "parentId": 2,
                            "code": "ww",
                            "name": "王五",
                            "content": "对，死渣男",
                            "childMsg": null
                        }
                    ]
                },
                {
                    "id": 3,
                    "parentId": 1,
                    "code": "ls",
                    "name": "李四",
                    "content": "曹贼",
                    "childMsg": null
                }
            ]
        },
        {
            "id": 5,
            "parentId": 0,
            "code": "shiyi",
            "name": "失忆老幺",
            "content": "大长腿",
            "childMsg": [
                {
                    "id": 6,
                    "parentId": 5,
                    "code": "zs",
                    "name": "张三",
                    "content": "呵，老色痞",
                    "childMsg": null
                }
            ]
        }
    ]
}
```

**吃不了自律的苦，就得受平庸的罪**

我是失忆，不善言辞又感性的一个人。

欢迎在下方留言。如果对您有帮助，别忘了三连，点赞，在看，留言。文章中有任何问题，您都可以在留言中指出。关注微信公众号，**一起学习，一起进步**。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200718093822635.png#pic_center)
