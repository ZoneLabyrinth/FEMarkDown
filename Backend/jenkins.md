## Jenkins + gitlab自动发布

1. **安装插件：Gitlab plugin, Gitlab Hook plugin**

2. **配置项目**

   ![image-20200227201930517](https://tva1.sinaimg.cn/large/0082zybply1gcb855nmcrj30pz0gi76j.jpg)

3. **配置Gitlab：找到项目 -->setting--> integrations->添加地址(需要用户名密码) --> add webhook**![image-20200227202000978](https://tva1.sinaimg.cn/large/0082zybply1gcb85oajtzj30pd0jw41o.jpg)

   ![image-20200227202017640](https://tva1.sinaimg.cn/large/0082zybply1gcb85yn4k9j30bi04274b.jpg)

4. 测试push,成功可以查看jenkins是否正在构建

5. 错误处理：以下错误可能由于没有用户密码，或者跨站请求没有配置。![image-20200227202140274](https://tva1.sinaimg.cn/large/0082zybply1gcb87e5aulj312h03tjst.jpg)

**解决方法：**

全局安全配置-->勾选匿名用户 --> 取消跨站伪造请求 --> 保存

![image-20200227202154964](https://tva1.sinaimg.cn/large/0082zybply1gcb87lnbw2j30a906r74e.jpg)

![image-20200227202212624](/Users/zz_rui/Library/Application Support/typora-user-images/image-20200227202212624.png)



