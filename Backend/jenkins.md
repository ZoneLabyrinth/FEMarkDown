## Jenkins + gitlab自动发布

1. **安装插件：Gitlab plugin, Gitlab Hook plugin**

2. **配置项目**

   ![image-20200227200342237](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200227200342237.png)

3. **配置Gitlab：找到项目 -->setting--> integrations->添加地址(需要用户名密码) --> add webhook**![image-20200227200819746](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200227200819746.png)

   ![image-20200227201016112](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200227201016112.png)

4. 测试push,成功可以查看jenkins是否正在构建

5. 错误处理：以下错误可能由于没有用户密码，或者跨站请求没有配置。![image-20200227201254272](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200227201254272.png)

**解决方法：**

全局安全配置-->勾选匿名用户 --> 取消跨站伪造请求 --> 保存

![image-20200227201526253](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200227201526253.png)

![image-20200227201538356](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200227201538356.png)



