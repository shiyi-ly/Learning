### 客户问题：页面使用https，访问后端接口不成功
#### 详细描述：
    1. 前后端分离项目，后端是SpringBoot项目，内置的tomcat部署。
    2. 前端配置了https，访问后端接口不成功。

#### 问题原因：
前端页面配置了https，访问后端接口也要用https访问，但是SpringBoot默认是没有配置https的。

解决方案：使用nginx代理接口，把https转法成http请求。