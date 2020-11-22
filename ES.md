#### 学习链接

Kibana介绍、安装和使用

https://blog.csdn.net/qq_18769269/article/details/80843810



##### 查看所有index

http://127.0.0.1:9200/_cat/indices?v





#### bug解决

**报错**：MaxDirectMemorySize may have been miscalculated due to JDK-8074459

修改elactive search中的config/jvm.options，结果发现启动elactive search闪退

原因：es版本过高，jdk版本过低，需要使用es自带的jdk

解决方案：https://blog.csdn.net/weixin_41601114/article/details/106939019

**报错**：kibana在IE浏览器中加载不出来

解决方法：更换浏览器即可，我换成了chrome，据说火狐也可以















