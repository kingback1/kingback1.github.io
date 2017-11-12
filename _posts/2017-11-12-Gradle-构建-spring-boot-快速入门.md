---
layout: post
title: 'Gradle 构建 spring boot 快速入门'
date: '2017-11-12'
header-img: "img/home.jpg"
tags:
     - Java web
author: 'Kingback'
---

在人工智能大火的当下，很多都是开发程序员转机器学习岗位，而我恰恰相反，毕业找工作的时候放弃了度厂无人车的机会选择了菊厂，其中和家庭，地域，现实有关。在菊厂做的是java web开发，是以前自己没有接触过的，加上自己有些懒散，学起来有些吃力。发现自己学的东西蛮杂的，什么都会一点，网络编程，C/C++，Linux，MFC，C#，Python，后台服务器，机器学习等等，现在又学前端，还真是往全栈工程师发展了。这是写前端博客的背景，吐吐槽。

## 简介
大厂现在都推崇微服务，模块解耦，`spring boot`就是这样的一个开源微服务框架，是`spring mvc`的升级版，开发更简便，摒弃了xml配置依赖注入的方式，所有配置文件可使用易编写的yaml文件，开发效率高。

## 快速入门
本章主要目标是用gradle完成`spring Boot`基础项目的构建，并且实现简单的Http请求处理，通过这个例子对`Spring Boot`有一个初步的了解，体检简单，开发快的特性。

## 系统要求
- Java 7及以上
- Spring Framework 4.1.5及以上
- Eclipse Mars

本文采用Java 1.8.0_111、Spring Boot 1.5.8, Eclipse Mars.2 Release (4.5.2)调试通过。

## 使用Gradle构建项目

* ### 1.通过`SPRING INITIALIZR`工具产生基础项目
![img](/img/2017/11/20171112125142.png)

点击Generate Project下载项目压缩包,并解压

* ### 2.目录结构
- `src/main/java/com/example/demo`下的程序入口：`DemoApplication`
- `src/main/resources`下的配置文件：`application.properties`
- `src/test/`下的测试入口：`DemoApplicationTests`

`build.gradle`是gradle的配置文件,包括了安装java插件，依赖包等

* ### 3.gradle构建工程
- `gradlew build` 命令，编译，下载依赖文件
- `gradlew eclipse` 命令，将生产eclipse工程

## 编写微服务
在eclipse中导入工程，在入口类下创建一个Controller和一个业务类HelloWorld
HelloController:

```
@RestController
public class HelloController {

    private HelloWorld helloWorld;

    public HelloController(HelloWorld helloWorld) {
        this.helloWorld = helloWorld;
    }

    @RequestMapping("/helloworld")
    public String hello1() {
        return helloWorld.hello();
    }

}

```

HelloWorld:
```
@Component
public class HelloWorld {
    public String hello() {
        // TODO Auto-generated method stub
        return "hello world i love u";
    }
}

```

工程运行，在浏览器中输入`localhost:8080/helloworld`就可以看到`hello world i love u`了。

### 参考资料
[博客1](https://www.cnblogs.com/davenkin/p/gradle-spring-boot.html)

[博客2](http://www.jianshu.com/p/d24bceea7665)