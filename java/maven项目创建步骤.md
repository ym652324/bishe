## 创建项目  
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
## 集成Springboot项目  
1. 登录官网，在Build a RESTful Web Service中点击Build with Maven
>* 拷贝<parent>标签内容到pom.xml的<properties>标签前  
>* 拷贝<dependencies>中第一个<dependency>内容到pom文件第一个<dependency>前  
2. 在右侧刷新重新导入Maven  
3. 在App.Java的APP类前面加上@EnableAutoConfiguration，实现自动配置，在APP的main函数中加上SpringApplication.run(App.class,args);再次运行，即可在8080端口启动Tomcat容器，可在浏览器进入localhost:8080查看页面。
4. 在App.Java的APP类前面加上@RestController，在APP类内加上一个输出HelloWorld的函数，函数前加上@RequestMapping("/")配置URL路径，即可在浏览器输入localhost:8080/时在页面输出HelloWorld，此时完成了后端与前端的连接    
## 集成Mybatis控件，实现读取数据库
