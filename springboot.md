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

##### test 目录存放单元测试的代码； pom.xml 用于配置项目依赖包，及其它配置

##### src.main.resources 目录下:

- static 目录存放 web 访问的静态资源，如 js、css、图片等；
- templates 目录存放页面模板；
- application.yml 存放项目的配置信息，例如数据库密码，配置端口等，也可使用application.properties

##### src.main.java 目录下:

- bean 目录下定义了需要实现功能用到的所有类和方法；
- config 目录
- controller 目录负责页面的访问控制
    例如admin下是有关登录的页面控制
- service 目录负责业务类代码 比如查询，删除，增加，分类等操作
- utils 目录下放置了一些工具类

补充：   Java bean: 符合一定规范编写的Java类
- 所有属性为private
- 提供默认构造方法
- 提供getter和setter
- 实现serializable接口

## Springboot 一些常用的@注解

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


