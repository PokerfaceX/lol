## 什么是spring

![image-20240428084515388](/Users/jasonjin/Library/Application Support/typora-user-images/image-20240428084515388.png)

spring可以说是一套全家桶，它提供了若干个项目，用来解决不同的问题，**而其中的核心就是这个Spring Framework**，它提供了体层的核心技术比如依赖注入，web开发支持等。但是如果我们直接使用spring的核心框架进行开发，那么就会非常难以入门，因为spring核心框架需要进行很多繁琐的配置，入门难度极大。因此我们直接使用springboot进行开发，这个spring家族的项目可以帮助我们快速进行开发



### springboot父工程

```xml
  <parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.0.0</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
```

所有的springboot项目都继承于这个父工程，现在先这么理解，以后在了解下

- 像是这里我的父工程是3.0.0，那么所有起步依赖都会是和3.0.0对应的起步依赖



![image-20240106230122241](/Users/jasonjin/Library/Application Support/typora-user-images/image-20240106230122241.png)

在我们一开始创建的springboot starter project中，我们制定了springboot-starter-web依赖，这也被称为起步依赖，包含了使用springboot开发web程序最常见的依赖，同时这里面通过**依赖传递**，也拥有tomcat的依赖。所以说当我们启动springboot项目的时候，里面内置的tomcat程序也被启动了



