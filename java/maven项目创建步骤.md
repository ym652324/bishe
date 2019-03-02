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
## 三、集成Mybatis控件，实现查询数据库    
1. 在resources目录下创建application.properties文件，即可在其中指定端口等，如server.port=8090  
2. 在pom文件内的<dependencies>标签内引入mysql的依赖，alibaba的Druid连接池，mybatis支持文件，然后更新maven项目jar包  
3. 配置mybatis:  
  >* 在application.properties文件中配置：mybatis.mapperLocations=classpath:mapping/*.xml  
  >* 在resources文件下创建新的Directory为mapping  
  >* 在pom文件中的<plugins>标签最后加上org.mybatis.generator的插件，在此<plugin>标签内加上<dependencies>标签，引入mybatis、mysql依赖，在<dependencies>标签后加上<executions>标签，其中添加<execution>标签，定义id（名字）、phase（package）、goals，在<goals>标签内定义<goal>为generate，最后可以在<executions>标签后用<configaration>进行一些配置，如<verbose>(是否允许移动生成文件）、<overwrite>（是否允许覆盖），最后在<configurationFile>中添加mybatis-generator的配置文件路径（一般在resources文件下），在此路径新建mybatis-generator.xml文件（可下载）。    
4. 创建数据库，使用Navicat for MySQL创建数据表  
5. 配置mybatis-generator.xml文件：  
 >* 在mybatis-generator.xml的jdbcConnection标签中填写数据库的connectionURL="jdbc:mysql://127.0.0.1:3306/数据库名"，以及数据库的userId、password值  
 >* 在com.miaoshaproject目录下新建dataobject的package，将mybatis-generator.xml的javaModelGenerator标签中的targetPackage指定为com.miaoshaproject.dataobject，实现将数据库中的表分别建立dataobject到此路径下  
 >*  在sqlMapGenerator标签内，将属性targetPackage改为mapping，targetProject为“src/main/resources”  
 >* 在com.miaoshaproject目录下新建dao的package，javaClientGenerator标签的targetProject为com.miaoshaproject.dao  
 >* 在sqlMapGenerator标签后添加table标签，填写表名和映射文件名          
6. 在run->edit Configurations->"+"新建->maven  
>* Name:mybatis-generator  
>*  Command line:mybatis-generator:generate  
7. Run->Run "mybatis-generator",在mapping自动生成数据库的表的对应文件，在dataobject中生成表中的对应字段、方法    
8. 在application.properties文件中配置：   
 >* spring.datasources.name=miaosha  （数据库信息）
 >* spring.datasource.url=jdbc:mysql://127.0.0.1:3306/miaosha  
 >* spring.datasource.username=root  
 >* spring.datasource.password=root  
 >* spring.datasource.type=com.alibaba.druid.pool.DruidDataSource（使用Druid数据源）
 >* spring.datasource.driverClassName=com.mysql.jdbc.Driver  
9. 修改APP文件：  
  >* 将@EnableAutoConfiguration改为@SpringbootApplication(scanBasePackages={"com.miaoshaproject"}),扫描此文件  
  >* 在@RestController下添加@MapperScan("com.miaoshaoproject.dao") 
 10. 在com.miaoshapoproject文件下创建新的package：controller、service  
 11. 写用户controller：  
  >* 在controller文件夹下创建新class：UserController，在UserController类前面添加@Controller("user"),用来被Spring扫描到，Controller名字叫user，下一行添加@RequestMapping("/user"),指定访问路径  
  >* 在UserController类中新建一个getUser(@RequestParam（name="id"）Integer id)方法,用来调用service服务获取对应id的用户对象并返回给前端，在这个方法前添加@RequestMapping("/get")，指定访问路径  
 12. 写用户service：  
  >* 在service文件下新建Interface：UserService，以及package：impl（具体的实现），在impl文件夹下新建class：UserServiceImpl  
  >* 定义接口，在UserService接口内定义getUserByID(Integer id)方法，即通过用户id获取用户对象的方法  
  >* 在UserServiceImpl类中使用implements关键字实现UserService，选中UserServiceImpl后右键，选择generate可自动生成需要实现的getUserByID等方法基本框架。
  >* 在getUserById方法前添加@Autowired，以及private UserDOMapper userDOMapper；将UserDOMapper引入进来
  >* 在getUserById中用UserDO userDO=userDOMapper.selectByPrimaryKey（id）实现调用userDOMapped获取用户dataobject的功能
  >* 在文件最上方添加@Service标注业务层组件  
13. 在service文件下新建package：model，在model中新建class：UserModel  
  >* 在UserModel中添加用户表中的字段属性，然后generate->getter and setter,生成对应的setter和getter方法  
  >* 将UserService接口内定义的getUserByID(Integer id)方法返回值改为UserModel，UserServiceImpl实现中getUserById方法返回值也改为UserModel  
  >* 在UserServiceImpl文件中，写一个返回值为UserModel的convertFromDataObject(UserDO userDO,UserPasswordDO userPasswordDO)方法，用于将UserDOMapper和UserPasswordDO组装为一个UserModel对象（用户的信息字段和密码是两张表），这个方法内通过BeanUtils.copyProperties(userDO,userModel)方法将userDO内的属性拷贝到userModel对象中，然后通过调用setPassword（userPasswordDO.getPassword()）方法将UserModel的Password属性设置为userPassword的密码属性值  
  >* 使用@Autowired注解将UserPasswordDOMapper引入进来  
14. 因为默认只为UserPasswordDOMapper提供了通过主键查找用户密码的方法，但实际需要通过UserId查找用户密码，所以改造一下  
  >* 在UserPasswordDOMapper.xml中模仿selectByPrimaryKey写一个selectByUserID方法  
  >* 在UserPasswordDOMapper文件中定义selectByUserId(Integer userId)方法  
  >* 在UserServiceImpl文件的getUserById方法中使用UserPasswordDOMapper调用selectByUserId(userDO.getId()),获取到用户密码。（先通过userDOMapper调用selectByPrimaryKey方法获取到用户对象，然后获取对象的id后在密码表通过用户id而不是主键来获取用户密码）最后调用convertFromDataObject函数将userDO和UserPasswordDO组装成一个UserModel对象返回  
15. 在UserController中通过UserServiceImpl调用getUserById(id)写好getUser方法，返回一个UserModel,实现调用service服务获取对应id的对象并返回给前端。此时启动，修改路径为localhost:8090/user/get?id=1，此时访问即可输出json串。但是此时，用户密码也直接暴露在了前端，这是因为将自己业务逻辑的领域模型直接全部返回给了前端，前端应该只需要它要展示的属性数据即可，而不是领域模型全部。所以要在controller层再加一层模型对象  
16. 添加模型对象：  
  >* 在controller文件下新建package：viewobject，在其中新建class：UserVO，添加和UserModel一样的字段，删除掉前端不需要展示的字段如password，再生成getter和setter方法  
  >* 将UserController文件的getUser函数的返回值改为UserVO  
  >* 新加一个convertFromModel（UserModle UserModel）方法，将UserModel中的信息以UserVO的形式返回出去  
  >* 将getUser的return值改为convertFromModel（UserModle),实现将核心领域模型对象转化为可供前端使用的viewobject，此时再次启动之后，浏览器显示的字段就没有password了  
17. 定义通用的返回对象--返回正确信息  
  >* 在com.miaoshaproject下新建package：response，在其中新建class：CommonReturnType，这个类包括一个String类型的status（表明对应请求的返回处理结果success或fail）和一个Object类型的data（若status为success则data返回前端需要的json数据，若status为fail，则data内使用通用的错误码格式）属性，生成他们的get和set方法    
  >* 定义一个通用的创建方法create（Object result），处理如果不带任何status的话则将status置为success，创建新的CommonReturnType并返回，再使用函数重载的方法定义create（Object result，String status），处理带status的情况。  
  >* 将UserController中的getUser函数的返回值改为CommonReturnType，通过convertFromModel方法获得一个UserVO对象，并且通过CommonReturnType调用create方法返回通用对象。此时前端的输出是一个通用对象，包括status和data  
18.定义通用的返回对象--返回正确信息   
  >* 在com.miaoshaproject中新建package：error，在其中新建Interface：CommonError，其中定义方法getErrCode()、getErrMsg()、setErrMsg()，再在error中新建Enum：EmBusinessError，去用implements关键字实现CommonError，选中EmBusinessError右键->Implement methods,选中所有方法自动生成基本结构，定义errCode和errMsg。  
  >* 定义错误码USER_NOT_EXIST(10001,"用户不存在")，使用10000开头为用户信息相关错误定义，写一个EmBussinessError的构造函数，传入errCode和errMsg，修改getErrCode函数和getErrorMsg、setErrMsg函数返回值  
  >* 定义通用错误类型00001，PARAMETER_VALIDATION_ERROR(00001,"参数不合法") 
  >* 在error文件下新建class：BusinessException，继承（extend）自Exception类并实现（implements）了CommonError方法，选中BusinessException右键->Implement methods,选中所有方法自动生成基本结构，声明commonError，定义构造函数BusinessException，用来直接接收EmBusinessException的传参用于构造业务异常  
  >* 实现函数重写BusinessException(CommonError commonError,String errMsg)，实现接收自定义errMsg的方式构造业务异常。改写其他函数返回值
  
