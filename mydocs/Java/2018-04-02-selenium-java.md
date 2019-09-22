---
layout: index
title: "Selenium"
category: java
date: 2018-04-02 15:17:55
---

# Selenium Grid Setup

> Tips: 执行脚本的时候千万不要通过系统自带的记事本直接复制,使用notepadd++,或者sublime文本编辑工具!!!

> 如何启动selenium grid  

```java
1.启动Hub  
/**
*指定启动的角色是hub
*/
java -jar selenium-server-standalone-3.7.1.jar -role hub -newSessionWaitTimeout 30 -sessionTimeout 30 

2.启动Node  
/**
*指定启动的角色是node
*指定测试浏览器对应的driver路径
*chrome driver version: chromedriver_win32_237
*gecko driver version: geckodriver-v0.19.1-win32
*ie driver version: IEDriverServer_x64_3.9.0
*指定相应的hub
*/
java -Dwebdriver.gecko.driver="./selenium-java-3.7.1/geckodriver.exe" -Dwebdriver.chrome.driver="./selenium-java-3.7.1/chromedriver.exe" -Dwebdriver.ie.driver="./selenium-java-3.7.1/IEDriverServer.exe" -jar selenium-server-standalone-3.7.1.jar -role node -hub http://localhost:4444/grid/register/ -browserTimeout 60 -sessionTimeout 30
```

> 查看node启动参数配置  

```java
java -jar selenium-server-standalone-3.7.1.jar -role node -h
```

