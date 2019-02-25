## 一、创建项目  
1. 新建快速maven项目  
> File->New->Project->Maven->maven-archetype-quickstart->Next
2. 命名习惯  
>* GroupId：倒着的域名，如：com.miaoshaproject  
>* ArtifactId:项目名，如miaosha  
>* Project name：项目名，如miaosha  
3. 标记文档  
>* Java目录右键->Mark Directory As->Sources Root  
>* test目录右键->Mark Directory As->Test Sources Root
>* 在main文件中创建Directory为resources，标记为Resources Root  
此时即可run运行APP.java  
## 二、集成Springboot项目  
1. 登录官网，在Build a RESTful Web Service中点击Build with Maven
>* 拷贝<parent>标签内容到pom.xml的<properties>标签前  
>* 拷贝<dependencies>中第一个<dependency>内容到pom文件第一个<dependency>前  
2. 在右侧刷新重新导入Maven  
3. 在App.Java的APP类前面加上@EnableAutoConfiguration，实现自动配置，在APP的main函数中加上SpringApplication.run(App.class,args);再次运行，即可在8080端口启动Tomcat容器，可在浏览器进入localhost:8080查看页面。
4. 在App.Java的APP类前面加上@RestController，在APP类内加上一个输出HelloWorld的函数，函数前加上@RequestMapping("/")配置URL路径，即可在浏览器输入localhost:8080/时在页面输出HelloWorld，此时完成了后端与前端的连接    
## 三、集成Mybatis控件，实现读取数据库  
1. 在resources目录下创建application.properties文件，即可在其中指定端口等，如server.port=8090  
2. 在pom文件内的<dependencies>标签内引入mysql的依赖，alibaba的Druid连接池，mybatis支持文件，然后更新maven项目jar包  
3. 配置mybatis:  
  >* 在application.properties文件中配置：mybatis.mapperLocations=classpath:mapping/*.xml  
  >* 在resources文件下创建新的Directory为mapping  
  >* 在pom文件中的<plugins>标签最后加上org.mybatis.generator的插件，在此<plugin>标签内加上<dependencies>标签，引入mybatis、mysql依赖，在<dependencies>标签后加上<executions>标签，其中添加<execution>标签，定义id（名字）、phase（package）、goals，在<goals>标签内定义<goal>为generate，最后可以在<executions>标签后用<configaration>进行一些配置，如<verbose>(是否允许移动生成文件）、<overwrite>（是否允许覆盖），最后在<configurationFile>中添加mybatis-generator的配置文件路径（一般在resources文件下），在此路径新建mybatis-generator.xml文件（可下载）。  
4. 创建数据库，使用Navicat for MySQL创建数据表  
5. 配置mybatis-generator.xml文件：  
  >* 在mybatis-generator.xml的<jdbcConnection>中填写数据库的connectionURL="jdbc:mysql://127.0.0.1:3306/数据库名"，以及数据库的userId、password值   
  >* 在com.miaoshaproject目录下新建dataobject的package，将mybatis-generator.xml的<javaModelGenerator>中的targetPackage指定为com.miaoshaproject.dataobject，实现将数据库中的表分别建立dataobject到此路径下  
  >* 在<sqlMapGenerator>标签内，将属性targetPackage改为mapping，targetProject为“src/main/resources”  
  >* 在com.miaoshaproject目录下新建dao的package，<javaClientGenerator>的targetProject为com.miaoshaproject.dao  
  >* 在<sqlMapGenerator>标签后添加<table>标签，设置
  
