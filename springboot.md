# spingboot 项目结构及配置方法

## 项目目录结构
首先来看看一个例子

``` python
blogsever
    +-src
        +- main
            +- java
                +- org      
                    +- bean
                    +- config
                    +- controller
                        +- admin
                    +- mapper
                    +- service
                    +- utils
            +- resources
                +- static
                +- templates
                +- application.yml/application.properties  
    +- test
    +- target
    +-pom.xml
```

在这个例子中，可以体现springboot的 **层级结构** (主要为四层):
- entity层：和model层一样，存放的是实体类，属性值与数据库值保持一致，实现setter和getter方法

- dao层：即mapper层，对数据库进行持久化操作，它的方法是针对数据库操作额，基本上用的就是增删改查，就是一个接口，只有方法名，具体实现在mapper.xml中。

- service层：业务层，存放业务逻辑处理，不直接对数据库进行操作，有接口和接口实现类，提供controller层调用方法。

- controller层：控制层，导入service层，调用你service方法，controller通过接收前端传来的参数进行业务操作，在返回一个指定的路径或数据表。


##### src.main.resources 目录下:

- static 目录存放 web 访问的静态资源，如 js、css、图片等；
- templates 目录存放页面模板；
- application.yml 存放项目的配置信息，例如数据库密码，配置端口等，也可使用application.properties

##### src.main.java 目录下:

- bean 目录下定义了需要实现功能用到的所有类和方法 
- config 目录
- controller 目录负责页面的访问控制
    例如admin下是有关登录的页面控制
- service 目录负责业务类代码 比如查询，删除，增加，分类等操作
- utils 目录下放置了一些工具类

##### test 目录存放单元测试的代码； pom.xml 用于配置项目依赖包，及其它配置

补充：   Java bean: 符合一定规范编写的Java类
- 所有属性为private
- 提供默认构造方法
- 提供getter和setter
- 实现serializable接口
<br>

## Springboot 一些常用的@注解
#### 2.1 @SpringBootApplication
最重要的注解，用来标注一个主程序类，说明这是一个springboot 应用
例如在这个项目中, BlogserverApplication 为主程序类：

``` Java
@SpringBootApplication
@EnableScheduling//开启定时任务支持
public class BlogserverApplication {

    public static void main(String[] args) {
        SpringApplication.run(BlogserverApplication.class, args);
    }
}
```
<br>

#### 2.2 请求及路径映射部分注解
用于目录controller下

| 注解名称 | 解释 | 
| -- | -- |
@Controller | （处理http请求）表示在tomcat启动的时候，把这个类作为一个控制器加载到Spring的Bean工厂
@RestController | Spring4之后新加的注解，原来返回json，需要@ResponseBody配合@Controller
@ResponseBody | 主要用来接收前端传递给后端的json字符串中的数据，前端只能用POST的方式进行提交
@RequestMapping	| 映射请求，也就是通过它来指定控制器可以处理哪些URL请求, 其中method定义接收浏览器发来的何种请求，GET（查）、POST（增）、PUT（改）、DELETE（删）.


**@Controller** 在这个项目中并没有用到，其功能可以用 **@RestController** 来完成

**@RestController** 在实际开发中应用广泛，与 **@controller** 相比，不依赖于模板，前后端分离式的开发，开发效率也很高。

**@RequestMapping** 注解既可以在类上方使用，又可以方法上使用，在类上方使用，如在该处添加，接口访问时采取路径拼接方式访问。

下面是一个例子（../controller/admin/AdminController)：

``` Java
/**
 * 超级管理员专属Controller
 */
@RestController
@RequestMapping("/admin")   //在类上方使用，可配置url
public class AdminController {
    @Autowired
    ArticleService articleService;

    //在方法上使用，接口访问采取路径拼接，method定义浏览器请求
    @RequestMapping(value = "/article/all", method = RequestMethod.GET)
    public Map<String, Object> getArticleByStateByAdmin(@RequestParam(value = "page", defaultValue = "1") Integer page, @RequestParam(value = "count", defaultValue = "6") Integer count, String keywords) {
        List<Article> articles = articleService.getArticleByState(-2, page, count, keywords);
        Map<String, Object> map = new HashMap<>();
        map.put("articles", articles);
        map.put("totalCount", articleService.getArticleCountByState(1, null, keywords));
        return map;
    }

    @RequestMapping(value = "/article/dustbin", method = RequestMethod.PUT)
    public RespBean updateArticleState(Long[] aids, Integer state) {
        if (articleService.updateArticleState(aids, state) == aids.length) {
            return new RespBean("success", "删除成功!");
        }
        return new RespBean("error", "删除失败!");
    }
}
```
<br>


#### 2.3 @mapper
用于dao层（mapper层中），其中定义一些对数据库进行持久化操作的方法，基本上用的就是增删改查，就是一个接口，只有方法名。

下面是一个例子(../mapper/UserMapper):
``` Java
@Mapper
public interface UserMapper {
    //下面定义的方法都是关于user类的增删查改
    User loadUserByUsername(@Param("username") String username);

    long reg(User user);

    int updateUserEmail(@Param("email") String email, @Param("id") Long id);

    List<User> getUserByNickname(@Param("nickname") String nickname);

    List<Role> getAllRole();

    int updateUserEnabled(@Param("enabled") Boolean enabled, @Param("uid") Long uid);

    int deleteUserById(Long uid);

    int deleteUserRolesByUid(Long id);

    int setUserRoles(@Param("rids") Long[] rids, @Param("id") Long id);

    User getUserById(@Param("id") Long id);
}
```
**@Param** 后面跟的是user这个entity的一个attribute, 例如"nickname",password", "email" 等等



#### 2.4 其它一些常用注解
 -  **@autowired**: 根据属性类型在容器中寻找bean类，并装配进去
     E.g @Autowired
         Newservice newservice
     就是中岛Newservice类型中的bean，自动匹配，并把bean装配到newservice中
 - **@service**:（用在业务层）告诉springboot创建一个实现类的实列，就不用配置bean了 （？不太确定）
 - **@Transcactional**: 声明式的注解业务管理
 - **@Override**: 表示此方法是一个覆写的方法, 调用时如果不满足覆写会报错

- @RequestMapping: 映射请求，也就是通过它来指定控制器可以处理哪些URL请求, 其中method定义接收浏览器发来的何种请求，GET（查）、POST（增）、PUT（改）、DELETE（删）.
- @Controller: （处理http请求）表示在tomcat启动的时候，把这个类作为一个控制器加载到Spring的Bean工厂
- @ResponseBody： 主要用来接收前端传递给后端的json字符串中的数据，前端只能用POST的方式进行提交
- @RestController: @controller 和 @RequestBody 的组合注解.
- @autowired: 根据属性类型在容器中寻找bean类，并装配进去
    E.g @Autowired
        Newservice newservice
    就是中岛Newservice类型中的bean，自动匹配，并把bean装配到newservice中
- @service：（用在业务层）告诉springboot创建一个实现类的实列，就不用配置bean了 （？不太确定）
- @Transcactional: 声明式的注解业务管理
- @Mapper: 该接口类的实现类对象交给mybatis底层创建，然后交由Spring框架管理


