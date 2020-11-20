# java-api-doc
 零代码入侵，零注释入侵

# 页面布局

如下图：
![](https://upload-images.jianshu.io/upload_images/2833665-53f414cfad38ceeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 左侧为菜单，菜单分为两级，一级表示模块，二级表示接口信息，一级菜单就是你定义的模块名称，二级菜单是对外接口的方法名，如果你的方法上有注释，这里会自动解析方法的注释作为二级菜单。菜单拖拽可以排序。

2. 右侧为接口详细信息，主要包括：模拟测试功能，接口详细信息说明，请求参数说明，响应参数说明，参数的详细信息也是默认解析代码，如有注释优先展示注释，支持对象的泛型，多维数组，自嵌套，互相嵌套，并根据参数信息生成一个演示的例子表明接口的使用方式

# 页面操作

1. 左侧二级菜单可以双击修改，失去焦点时自动保存

2.  左侧一级，二级菜单可以拖拽排序

3. 所有带 “编辑”按钮的地方都可以编辑保存，textarea编辑时可以带回车换行，自动记录你的文本格式

4. 请求参数和响应参数，本身是一个树结构，所有编辑的时候跟普通的编辑树一样操做，包括添加一级数据，添加子数据，修改，删除，保存等等，鼠标移入会有提示，如下图

![](https://upload-images.jianshu.io/upload_images/2833665-8926fbc4848bbca2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.如果需要给接口提供默认值，修改参数的默认值后，会自动重构json参数，告诉你接口怎么使用，清晰明了

![image.png](https://upload-images.jianshu.io/upload_images/2833665-0839a094d98a7ad2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 工具提供自动化测试，支持默认数据填充，前后台都抛弃PostMan等第三方测试工具吧

![](https://upload-images.jianshu.io/upload_images/2833665-46fa3b026be91aa4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# 代码使用

1. 引入jar包
```
       <!--Maven jar包引入方式-->
        <dependency>
            <groupId>com.apidoc</groupId>
            <artifactId>apidoc</artifactId>
            <version>1.0.0</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/resources/lib/apidoc-1.0.0.jar</systemPath>
        </dependency>
    </dependencies>
```
2. 修改配置文件
```
#是否开启apidoc
apidoc=true

# datasource 数据源配置，目前仅支持mysql，如果需要其他数据库，请自行修改com.apidoc.dao数据库操作层的sql，或者联系我修改（需要付费）
#配置好数据源，运行程序会自动生成4张表，用来存储api的接口信息，不需要开发人员处理，默认会自动生成
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/apidoc?useUnicode=true&zeroDateTimeBehavior=convertToNull&autoReconnect=true&characterEncoding=UTF-8&characterSetResults=UTF-8&allowMultiQueries=true&&useSSL=false
spring.datasource.username=root
spring.datasource.password=root
```
2. 增加springboot快速配置类
```
/**
 * 配置类
 * 让springboot自动扫描并管理apidoc工具下的所有class
 */
@Configuration
@EnableTransactionManagement//启动事务
@ComponentScan("com.apidoc")//扫描组件类
@MapperScan("com.apidoc.dao")//扫描数据库操作层的类
@EntityScan("com.apidoc.entity")//扫描实体类
public class ApiDocConfig {
    @Bean
    public ApiDocService generator() {
        //所有的常量都在Const类下，需要修改常量的在这里配置即可
        //配置代码的绝对路径，方便扫描代码的注释，因为注释编译之后就被jvm剔除了，只能扫描源码，不配置则不扫描注释
        //默认路径为{项目路径}+src/main/java
        Const.codePath = Const.projectPath + "apidoc-demo" + File.separator + "src" + File.separator + "main" + File.separator + "java" + File.separator;
        return new ApiDocService();
    }
}

```
3. 在你的controller类上增加注解`@Api("这里写模块名称")`，这里一个@Api对应页面的一个一级菜单，即模块，模块可以由多个类组成，只需设置每个类的模块名称一样，程序会自动把模块名称一样的class组装成同一模块。
4. 浏览器访问地址`http://localhost:8080/apidoc/index.html?packageName=com.demo`即可。如果一个项目有多个文档，比如前端页面一个文档，后台管理一个文档等等，可以用java的包区分。一个包代表一个文档，不同的文档使用不同的包即可。即url后的参数：`packageName=你的包名`

# 特别说明&&高级模式
###### 目前仅支持SpringMVC/SpringBoot+Mybatis+MySQL技术架构，如果您使用其他技术，可以下载我的源码稍作修改，或联系我修改（需要付费）
工具是标准的前后台分离开发架构，后台为MVC开发模式，如果你的技术跟我使用的不一样选择性替换相应的代码层即可。
- 路由控制层：替换这一个类即可com.apidoc.controller.ApiDocController
- 数据库操作层：修改这个包的sql即可com.apidoc.dao
- 页面中所有的常量都可以配置，包括路由等等，具体见常量类com.apidoc.common.Const
- 嫌弃前端页面丑陋：apidoc/front这个路径下是我的所有前端代码，我才用的是最新版的angualr做的，你可以选择你喜欢的任何前端技术重构自己的页面，然后对接后台接口即可。总共如下接口，采用标准的RESTful风格
```
//api接口
export const apis={
  isOpenApiDoc:"/apidoc/isOpenApiDoc",
  info:"/apidoc/info",
  modules:"/apidoc/modules",
  actions:"/apidoc/actions",
  detail:"/apidoc/detail",
  updateInfo: "/apidoc/updateInfo",
  updateAction: "/apidoc/updateAction",
  updateActionDescription: "/apidoc/updateActionDescription",
  updateDetail: "/apidoc/updateDetail",
  updateModulesSort: "/apidoc/updateModulesSort",
  updateActionsSort: "/apidoc/updateActionsSort",
  addParam:"/apidoc/addParam",
  updateParam:"/apidoc/updateParam",
  deleteParam:"/apidoc/deleteParam",
};
```

