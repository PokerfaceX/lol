# Maven知识点



### maven坐标

maven中的坐标就像是一个项目的唯一标识，通过坐标我们可以一个项目或者资源的位置

**group id**: 定义着当前maven项目隶属组织的名字，一般就是域名反着写

**artifact id**: 当前maven项目的名称，通常是模块名称，比如read-service

**version**: 项目版本号



**坐标**可以用来定义当前项目，或者说用来导入所需要的依赖



### maven依赖

这份文件声明了很多依赖，

1.在 pom.xml 中编写 <dependencies> 标签

2.在 <dependencies> 标签中 使用 <dependency> 引入坐标

3.定义坐标的 groupId，artifactId，version

4.点击刷新按钮，引入最新加入的坐标



如果引入的依赖，在本地仓库不存在，将会连接远程仓库/中央仓库，然后下载依赖，这样会下载到本地仓库里



### 依赖传递

当我们开发一个maven项目的时候，依赖是具有传递性的，且分为

- 直接依赖：在当前项目直接通过依赖声明建立的关系
- 间接依赖：假设当前项目依赖一个资源，而这个资源又依赖另一个资源，那么这俩资源都会成为当前项目的依赖，且都自动配置好了

![image-20231228235842998](/Users/jasonjin/Library/Application Support/typora-user-images/image-20231228235842998.png)

就像上面的图一样，只要A项目声明依赖B项目，那么C项目和俩jar包都会导入到A项目的依赖里面



但是如果我们只想取依赖到项目C，而2个间接依赖的jar包都不想要，那么就可以使用以下方法来断开依赖的资源，且不用指定版本

```xml
<dependency>
  <groupId>com.itheima</groupId> 
  <artifactId>maven-projectB</artifactId>  
  <version>1.0-SNAPSHOT</version>   
	<exclusions>    
    <exclusion>            
      <groupId>xxx</groupId>            
      <artifactId>xxx</artifactId>        
    </exclusion>    
  </exclusions>
</dependency>

```





### 依赖范围

一个jar包，我们可以通过**<scope></scope>**参数来设置这个jar包的有效范围，我们可以规定这个jar包在

- 主程序范围内有效(main文件夹)
- 测试范围内有效(test文件夹)
- 参与打包运行(通过package指令查看)，这个稍微解释一下，假如说一个maven项目，他的其中一个依赖被设置成不参与打包，那么当我们打包这个maven项目的时候，我们打开这个已经打包的maven项目的jar包，里面就找不到这个依赖的jar包了，反之我们就能找到

![image-20231228233802649](/Users/jasonjin/Library/Application Support/typora-user-images/image-20231228233802649.png)



### maven生命周期

maven中三套独立的生命周期，它们就是

- clean：清理工作
- default：核心工作，比如：编译，测试，打包，安装，部署等
- site：生成报告，发布站点



每一个生命周期包含很多阶段，而它们都是有顺序的

![image-20231229100715902](/Users/jasonjin/Library/Application Support/typora-user-images/image-20231229100715902.png)





我们重点关注的阶段，其实就以下几个

clean：清理target目录

compile：编译源代码

test：利用测试的框架来运行测试的代码

package：将项目打包并且放到target文件夹里面，可以是war或者jar形式

install：把项目安装到本地仓库，一半是放到～/.m2文件夹里面



注意的是，在**同一套**生命周期中，你运行一个阶段，前面的阶段，只要他们在相同的生命周期，那么也会运行，比如说你运行mvn install指令，其实compile，test，package也都被运行了。同样的道理，当你运行mvn install的时候，clean是不会运行的，因为他们属于不同的生命周期



- 可以在intellij maven选项里面，让maven工具跳过某个阶段
