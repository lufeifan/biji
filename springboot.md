

# springboot

## 常用配置

```yml
server:
  port: 9000

spring:
  datasource:
    name: test
    url: jdbc:mysql://127.0.0.1:3307/vueblog?useUnicode=true&useSSL=false&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: root

# mybatis config
mybatis-plus:
  mapper-locations: classpath:mappers/*.xml
  type-aliases-package: com.vueblog.demo.model
  global-config:
    db-config:
      id-type: auto
      #日志
#  configuration:
#    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

# Mybatis 

## 依赖

### pom.xml

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

```xml
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <configuration>
                    <!-- 在控制台打印执行日志 -->
                    <verbose>true</verbose>
                    <!-- 重复生成时会覆盖之前的文件-->
                    <overwrite>true</overwrite>
                    <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                </configuration>
<!--                &lt;!&ndash; 数据库连接选择8.0以上的，因为用的mysql8.0&ndash;&gt;-->
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.16</version>
                    </dependency>
                </dependencies>
            </plugin>
```

## 代码生成器配置

### generatorConfig.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!-- 引入配置文件 -->
    <properties resource="generator.properties"/>
    <!-- context 是逆向工程的主要配置信息 -->
    <!-- id：起个名字 -->
    <!-- targetRuntime：设置生成的文件适用于那个 mybatis 版本 -->
    <context id="default" targetRuntime="MyBatis3">
        <!--optional,指在创建class时，对注释进行控制-->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
<!--        jdbc的数据库连接 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3307/mall?useUnicode=true&amp;characeterEncoding=utf-8&amp;serverTimezone=UTC"
                        userId="${userId}"
                        password="${password}">
<!--        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"-->
<!--                        connectionURL="jdbc:mysql://localhost:3307/mall?useUnicode=true&amp;characeterEncoding=utf-8&amp;serverTimezone=UTC" userId="root"-->
<!--                        password="root">-->
            <!--解决mysql驱动升级到8.0后不生成指定数据库代码的问题-->
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>
        <!--非必须，类型处理器，在数据库类型和java类型之间的转换控制-->
        <javaTypeResolver>
            <!-- 默认情况下数据库中的 decimal，bigInt 在 Java 对应是 sql 下的 BigDecimal 类 -->
            <!-- 不是 double 和 long 类型 -->
            <!-- 使用常用的基本类型代替 sql 包下的引用类型 -->
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- targetPackage：生成的实体类所在的包 -->
        <!-- targetProject：生成的实体类所在的硬盘位置 -->
        <javaModelGenerator targetPackage="com.mybatis.demo.entity"
                            targetProject="src/main/java">
            <!-- 是否允许子包 -->
            <property name="enableSubPackages" value="false"/>
            <!-- 是否对modal添加构造函数 -->
            <property name="constructorBased" value="true"/>
            <!-- 是否清理从数据库中查询出的字符串左右两边的空白字符 -->
            <property name="trimStrings" value="true"/>
            <!-- 建立modal对象是否不可改变 即生成的modal对象不会有setter方法，只有构造方法 -->
            <property name="immutable" value="false"/>
        </javaModelGenerator>
        <!-- targetPackage 和 targetProject：生成的 mapper 文件的包和位置 -->
        <sqlMapGenerator targetPackage="mapper"
                         targetProject="src/main/resources">
            <!-- 针对数据库的一个配置，是否把 schema 作为字包名 -->
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!-- targetPackage 和 targetProject：生成的 interface 文件的包和位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.mybatis.demo.dao" targetProject="src/main/java">
            <!-- 针对 oracle 数据库的一个配置，是否把 schema 作为字包名 -->
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!-- tableName是数据库中的表名，domainObjectName是生成的JAVA模型名，后面的参数不用改，要生成更多的表就在下面继续加table标签 -->
        <table tableName="%" schema="SCOTT" enableCountByExample="false"
               enableUpdateByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false" >

        </table>
<!--        <table tableName="cms_help" domainObjectName="CmsHhelp"-->
<!--               enableCountByExample="false" enableUpdateByExample="false"-->
<!--               enableDeleteByExample="false" enableSelectByExample="false"-->
<!--               selectByExampleQueryId="false"></table>-->
<!--        <table tableName="ums_role" domainObjectName="UmsRole"-->
<!--               enableCountByExample="false" enableUpdateByExample="false"-->
<!--               enableDeleteByExample="false" enableSelectByExample="false"-->
<!--               selectByExampleQueryId="false"></table>-->
    </context>
</generatorConfiguration>
```

# Mybatis - plus

## 依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.3.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-generator -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.2</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
<!--        模板引擎需要-->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.0</version>
</dependency>
<dependency>
     <groupId>org.freemarker</groupId>
     <artifactId>freemarker</artifactId>
</dependency>
```

## 配置

```yml
# mybatis config
mybatis-plus:
  mapper-locations: classpath:mappers/*.xml
  type-aliases-package: com.example.demo.model
  global-config:
    db-config:
      id-type: auto
     #日志
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## 分页配置

```java
package com.cache.demo.config;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MybatisPlusConfig {

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

## 分页测试

```java
	@Resource
    private UserMapper userMapper;
    @Test
    public void queryUserForPage(){
        IPage<User> userPage = new Page<>(2, 2);//参数一是当前页，参数二是每页个数
        userPage = userMapper.selectPage(userPage, null);
        List<User> list = userPage.getRecords();
        for(User user : list){
            System.out.println(user);
        }
    }
```



## 代码生成器配置

（MyBatisPlusGenerator.java）

```java
package com.vueblog.demo;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringPool;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.FileOutConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.TemplateConfig;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

/**
 * <p>
 * MyBatis Plus Generator 配置执行类示例
 * </p>
 */
public class MyBatisPlusGenerator {

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator autoGenerator = new AutoGenerator();

        // 全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        globalConfig.setOutputDir(projectPath + "/src/main/java");
        globalConfig.setAuthor("lululua");
        globalConfig.setServiceName("%sService");
        globalConfig.setControllerName("%sController");
        globalConfig.setEntityName("%sEntity");
        globalConfig.setMapperName("%sMapper");
        globalConfig.setServiceImplName("%sServiceImpl");
        globalConfig.setSwagger2(true);
        globalConfig.setOpen(false);
        globalConfig.setBaseResultMap(true);
        globalConfig.setBaseColumnList(true);
        autoGenerator.setGlobalConfig(globalConfig);

        // 数据源配置 需配置
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setUrl("jdbc:mysql://127.0.0.1:3307/vueblog?serverTimezone=UTC");
        // dataSourceConfig.setSchemaName("public");
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("root");
        dataSourceConfig.setDbType(DbType.MYSQL);
        autoGenerator.setDataSource(dataSourceConfig);

        // 生成包配置
        PackageConfig packageConfig = new PackageConfig();

        //如果需要手动输入模块名
        //packageConfig.setModuleName(scanner("模块名"));
        packageConfig.setModuleName(null);
        packageConfig.setParent("com.vueblog.demo");
        packageConfig.setEntity("entity");
        packageConfig.setMapper("mapper");
        packageConfig.setService("service");
        packageConfig.setServiceImpl("service.impl");
        packageConfig.setController("controller");
        autoGenerator.setPackageInfo(packageConfig);
//
//        // 自定义配置
//        InjectionConfig injectionConfig = new InjectionConfig() {
//            @Override
//            public void initMap() {
//                // to do nothing
//            }
//        };
//
//        // 如果模板引擎是 freemarker
//        String templatePath = "/templates/mapper.xml.ftl";
//
//        // 自定义输出配置
//        List<FileOutConfig> focList = new ArrayList<>();
//
//        // 自定义配置会被优先输出
//        focList.add(new FileOutConfig(templatePath) {
//            @Override
//            public String outputFile(TableInfo tableInfo) {
//
//                // 自定义输出文件名
//                return projectPath + "/src/main/resources/mapper/" + packageConfig.getModuleName()
//                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
//            }
//        });
//
//        injectionConfig.setFileOutConfigList(focList);
//        autoGenerator.setCfg(injectionConfig);
//
//        // 配置模板
        TemplateConfig templateConfig = new TemplateConfig();
//
//        // 配置自定义输出模板
//        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别
//        // templateConfig.setEntity("templates/entity-test.java");
//        // templateConfig.setService();
//        // templateConfig.setController();
//
        templateConfig.setXml(null);
        autoGenerator.setTemplate(templateConfig);

        // 策略配置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        // strategyConfig.setSuperEntityClass("com.baomidou.ant.common.BaseEntity");
        strategyConfig.setEntityLombokModel(true);
        strategyConfig.setRestControllerStyle(true);
        // strategyConfig.setSuperControllerClass("com.baomidou.ant.common.BaseController");

        // 如果 setInclude() 不加参数, 会自定查找所有表
        // 如需要制定单个表, 需填写参数如: strategyConfig.setInclude("user_info);
        strategyConfig.setInclude();
        // strategyConfig.setSuperEntityColumns("id");
        strategyConfig.setControllerMappingHyphenStyle(true);
        
        //自动将数据库中表名为 user_info 格式的专为 UserInfo 命名
        strategyConfig.setTablePrefix(packageConfig.getModuleName() + "_");
        autoGenerator.setStrategy(strategyConfig);
//        autoGenerator.setTemplateEngine(new FreemarkerTemplateEngine());

        System.out.println("===================== MyBatis Plus Generator ==================");

        autoGenerator.execute();

        System.out.println("================= MyBatis Plus Generator Execute Complete ==================");
    }

}
```

## 实例

### entity实体

```java
package com.example.demo.entity;


import lombok.Data;

@Data
public class Plus {
    private String name;
    private int id;
}
```

### mapper

```java
package com.example.demo.dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.example.demo.entity.Plus;

public interface PlusMapper extends BaseMapper<Plus> {

}
```

### service

```java
package com.example.demo.lll.service;

import com.example.demo.lll.entity.Plus;
import com.baomidou.mybatisplus.extension.service.IService;

/**
 * <p>
 *  服务类
 * </p>
 *
 * @since 2020-08-09
 */
public interface IPlusService extends IService<Plus> {

}
```

### serviceImpl

```java
package com.example.demo.lll.service.impl;

import com.example.demo.lll.entity.Plus;
import com.example.demo.lll.mapper.PlusMapper;
import com.example.demo.lll.service.IPlusService;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import org.springframework.stereotype.Service;

/**
 * <p>
 *  服务实现类
 * </p>
 *
 * @since 2020-08-09
 */
@Service
public class PlusServiceImpl extends ServiceImpl<PlusMapper, Plus> implements IPlusService {

}
```

### controller

```java
package com.example.demo.controller;

import com.example.demo.entity.Plus;
import com.example.demo.service.PlusServiceImpl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class PlusController {

    @Autowired
    private PlusServiceImpl plusService;

    @RequestMapping("/plusnn")
    public List<Plus> getpluss(){
        return plusService.list(null);
    }
}
```

### 自定义模板引擎

![image-20200809114857217](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200809114857217.png)

#### controller.java.vm

```
package ${package.Controller};

import ${package.Entity}.${entity};
import ${package.ServiceImpl}.${entity}ServiceImpl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;


/**
 * <p>
 * ${tablecomment}  前端控制器
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */

@RestController
public class ${table.controllerName}{

    @Autowired
    private ${entity}ServiceImpl ${table.entityPath}Service;

    @RequestMapping("/get${entity}")
    public List<${entity}> get${entity}(){
        return ${table.entityPath}Service.list(null);
    }

}
```



# Swagger

## 依赖

```xml
<!--swagger-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

## Swagger2API文档的配置

```java
package com.example.demo.config;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * Swagger2API文档的配置
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //为当前包下controller生成API文档
                .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
                //为有@Api注解的Controller生成API文档
//                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                //为有@ApiOperation注解的方法生成API文档
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("SwaggerUI演示")
                .description("mall-tiny")
                .contact("macro")
                .version("1.0")
                .build();
    }
}
```

## 实例

```java
package com.example.demo.controller;

import com.example.demo.entity.User;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiParam;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping
//Api注解，描述信息 可通过tag进行分类
@Api(value = "HelloController", description = "HelloController")
public class HelloController {

    @RequestMapping("/addPerson")
    @ApiOperation(notes = "添加人员", value = "addPerson")
    public User addPerson(
            @ApiParam(name = "name", value = "姓名") @RequestParam("name") String name,
            @ApiParam(name = "age", value = "年龄")  @RequestParam("age") Integer age) {
        User person = new User("swagger",1);
        return person;
    }
}
```

## 访问

http://localhost:9000/swagger-ui.html#/

# Redis

## 依赖

```xml
<!--redis依赖配置-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

## 配置

```yml
redis:
  host: localhost # Redis服务器地址
  database: 0 # Redis数据库索引（默认为0）
  port: 6379 # Redis服务器连接端口
  password: # Redis服务器连接密码（默认为空）
  jedis:
    pool:
      max-active: 8 # 连接池最大连接数（使用负值表示没有限制）
      max-wait: -1ms # 连接池最大阻塞等待时间（使用负值表示没有限制）
      max-idle: 8 # 连接池中的最大空闲连接
      min-idle: 0 # 连接池中的最小空闲连接
  timeout: 3000ms # 连接超时时间（毫秒）

  key:
    prefix:
      authCode: "portal:authCode:"
    expire:
      authCode: 120 # 验证码超期时间
```

## 实例

### mapper

```java
package com.example.demo.dao;

import org.apache.ibatis.annotations.Mapper;

/**
 * redis操作Service,
 * 对象和数组都以json形式进行存储
 */
@Mapper
public interface RedisService {
    /**
     * 存储数据
     * @return
     */
    boolean set(String key, String value);

    /**
     * 获取数据
     */
    String get(String key);

    /**
     * 设置超期时间
     */
    boolean expire(String key, long expire);

    /**
     * 删除数据
     */
    void remove(String key);

    /**
     * 自增操作
     * @param delta 自增步长
     */
    Long increment(String key, long delta);

}
```

### serviceImpl

```java
package com.example.demo.service;

import com.example.demo.dao.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

/**
 * redis操作Service的实现类
 */
@Service
public class RedisServiceImpl implements RedisService {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public boolean set(String key, String value) {
        stringRedisTemplate.opsForValue().set(key, value);
        return false;
    }

    @Override
    public String get(String key) {
        return stringRedisTemplate.opsForValue().get(key);
    }

    @Override
    public boolean expire(String key, long expire) {
        return stringRedisTemplate.expire(key, expire, TimeUnit.SECONDS);
    }

    @Override
    public void remove(String key) {
        stringRedisTemplate.delete(key);
    }

    @Override
    public Long increment(String key, long delta) {
        return stringRedisTemplate.opsForValue().increment(key,delta);
    }
}
```

### controller

```java
package com.example.demo.controller;

import com.example.demo.service.RedisServiceImpl;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

/**
 * @program: springbootdemo
 */
@Slf4j
@RequestMapping("/redis")
@RestController
public class RedisController {

    private static int ExpireTime = 60;   // redis中存储的过期时间60s

    @Resource
    private RedisServiceImpl redisUtil;

    @RequestMapping("set")
    public boolean redisset(String key, String value){
        return redisUtil.set("time","120");
    }

    @RequestMapping("get")
    public Object redisget(String key){
        return redisUtil.get("k");
    }

    @RequestMapping("expire")
    public boolean expire(String key){
        return redisUtil.expire("time",ExpireTime);
    }
}
```

# Security

## 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 配置

```java
package com.security.learn.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
class SecurityConfig extends WebSecurityConfigurerAdapter {
//    认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("admin").password(new BCryptPasswordEncoder().encode("1020aA..")).roles("vip1")
                .and()
                .withUser("root").password(new BCryptPasswordEncoder().encode("root")).roles("vip2");
    }
//授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
//     /auth 需要vip1登录才能使用
        http.authorizeRequests().antMatchers("/auth").hasRole("vip2")
//                其他地址只要登录就可以使用
//                .anyRequest().authenticated()
                .and()
                .formLogin()
                .and()
                .logout();

        http.authorizeRequests().antMatchers("/out").hasRole("vip1")
            .and()
            .formLogin();

        //登录成功的处理器
//        .successHandler(new AuthenticationSuccessHandler() {
//            @Override
//            public void onAuthenticationSuccess(HttpServletRequest req, HttpServletResponse resp, Authentication authentication) throws IOException, ServletException {
//                resp.setContentType("application/json;charset=utf-8");
//                PrintWriter out = resp.getWriter();
//                out.write("success");
//                out.flush();
//            }
//        })
//                .failureHandler(new AuthenticationFailureHandler() {
//                    @Override
//                    public void onAuthenticationFailure(HttpServletRequest req, HttpServletResponse resp, AuthenticationException exception) throws IOException, ServletException {
//                        resp.setContentType("application/json;charset=utf-8");
//                        PrintWriter out = resp.getWriter();
//                        out.write("fail");
//                        out.flush();
//                    }
//                })
//                .permitAll()//和表单登录相关的接口统统都直接通过
//                .and()
//                .logout()
//                .logoutUrl("/logout")
//                .logoutSuccessHandler(new LogoutSuccessHandler() {
//                    @Override
//                    public void onLogoutSuccess(HttpServletRequest req, HttpServletResponse resp, Authentication authentication) throws IOException, ServletException {
//                        resp.setContentType("application/json;charset=utf-8");
//                        PrintWriter out = resp.getWriter();
//                        out.write("logout success");
//                        out.flush();
//                    }
//                })
//                .permitAll()
//                .and()
//                .httpBasic()
//                .and()
//                .csrf().disable();

    }
//不需要授权
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/err");
//        .ignoring().antMatchers("out");
    }
}
```

# Task

## 依赖

SpringTask已经存在于Spring框架中，所以无需添加依赖。

## 配置

```java
package com.security.learn.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableScheduling;

/**
 * 定时任务配置
 */
@Configuration
@EnableScheduling
public class SpringTaskConfig {
}
```

## 实例

```java
package com.security.learn.controller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class OrderTimeOutCancelTask {
    private Logger LOGGER = LoggerFactory.getLogger(OrderTimeOutCancelTask.class);

    /**
     * cron表达式：Seconds Minutes Hours DayofMonth Month DayofWeek [Year]
     * 1分钟打印一次日志
     */
    @Scheduled(cron = "0 0/1 * ? * ?")
    private void cancelTimeOutOrder() {
        LOGGER.info(" 1分钟打印一次日志");
    }
}
```

# mongodb

## 依赖

```xml
<!---mongodb相关依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

## 配置

```yml
mongodb:
  host: localhost
  port: 27017
  database: test
```

## 关闭_class字段

```
package com.leilei.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationListener;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.event.ContextRefreshedEvent;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.convert.DefaultMongoTypeMapper;
import org.springframework.data.mongodb.core.convert.MappingMongoConverter;
import org.springframework.data.mongodb.core.convert.MongoConverter;


@Configuration
public class MongoConfig implements ApplicationListener<ContextRefreshedEvent> {

  @Autowired
  MongoTemplate oneMongoTemplate;
  
  private static final String TYPEKEY = "_class";

  @Override
  public void onApplicationEvent(ContextRefreshedEvent contextRefreshedEvent) {
    MongoConverter converter = oneMongoTemplate.getConverter();
    if (converter.getTypeMapper().isTypeKey(TYPEKEY)) {
      ((MappingMongoConverter) converter).setTypeMapper(new DefaultMongoTypeMapper(null));
    }
  }
}
```

## 实例

### entity

```java
package com.security.learn.entity;

import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;

@Data
@Document(indexName = "product")
public class MongoEntity {

    @Id
    private Long id;

    private String title;

    private String description;

    private String by;

    private String url;
    
}
```

### mapper

```java
package com.security.learn.dao;

import com.security.learn.entity.MongoEntity;

public interface MongoMapper {

    void saveDemo(MongoEntity demoEntity);

    void removeDemo(Long id);

    void updateDemo(MongoEntity demoEntity);

    MongoEntity findDemoById(Long id);
}
```

### serviceImpl

```java
package com.security.learn.service.Impl;

import com.security.learn.dao.MongoMapper;
import com.security.learn.entity.MongoEntity;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.data.mongodb.core.query.Update;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;

/**
 * 描述: Demo DAO 实现
 **/
@Service
public class MongoServiceImpl implements MongoMapper {

    @Autowired
    private MongoTemplate mongoTemplate;

    @Override
    public void saveDemo(MongoEntity demoEntity) {
        mongoTemplate.save(demoEntity);
    }

    @Override
    public void removeDemo(Long id) {
        MongoEntity mongoEntity = new MongoEntity();
        mongoEntity.setId(id);
        mongoTemplate.remove(mongoEntity);
    }

    @Override
    public void updateDemo(MongoEntity demoEntity) {
        Query query = new Query(Criteria.where("id").is(demoEntity.getId()));

        Update update = new Update();
        update.set("title", demoEntity.getTitle());
        update.set("description", demoEntity.getDescription());
        update.set("by", demoEntity.getBy());
        update.set("url", demoEntity.getUrl());

        mongoTemplate.updateFirst(query, update, MongoEntity.class);
    }

    @Override
    public MongoEntity findDemoById(Long id) {
        Query query = new Query(Criteria.where("id").is(id));
        MongoEntity demoEntity = mongoTemplate.findOne(query, MongoEntity.class);
        return demoEntity;
    }

}
```

### controller

```java
package com.security.learn.controller;

import com.security.learn.entity.MongoEntity;
import com.security.learn.service.Impl.MongoServiceImpl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MongoController {

    @Autowired
    private MongoServiceImpl mongoService;
    @RequestMapping("mongo")
    public MongoEntity findDemoByIdTest() {

        MongoEntity demoEntity = mongoService.findDemoById(1L);
//      将实体对象转换成Json字符串
//        return JSONObject.toJSONString(demoEntity);
        return demoEntity;
    }
    @RequestMapping("mongosave")
    public MongoEntity mongosave() {
        MongoEntity demoEntity = new MongoEntity();
        demoEntity.setId(5L);
        demoEntity.setTitle("Spring Boot 中使用 MongoDB");
        demoEntity.setDescription("关注公众号，搜云库，专注于开发技术的研究与知识分享");
        demoEntity.setBy("souyunku");
        demoEntity.setUrl("http://www.souyunku.com");
        mongoService.saveDemo(demoEntity);
        return demoEntity;
    }
}
```

# Elasticsearch

## 依赖

```xml
        <!--Elasticsearch相关依赖-->
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-elasticsearch -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
            <version>2.3.2.RELEASE</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.elasticsearch.client/transport -->
<!--        <dependency>-->
<!--            <groupId>org.elasticsearch.client</groupId>-->
<!--            <artifactId>transport</artifactId>-->
<!--            <version>7.6.2</version>-->
<!--        </dependency>-->
```

## 配置

```java
package com.security.learn.config;

import org.apache.http.Header;
import org.apache.http.HttpHost;
import org.apache.http.message.BasicHeader;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import org.elasticsearch.client.RestHighLevelClient;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.security.KeyManagementException;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;

@Configuration

public class RestConfig {

    @Bean
    public RestHighLevelClient esRestHighLevelClient() {
        RestHighLevelClient client = new RestHighLevelClient(
                // 这里可以配置多个 es服务，我当前服务不是集群，所以目前只配置一个
                RestClient.builder(
                        new HttpHost("127.0.0.1", 9200, "http")
                ));
        return client;
    }

    @Bean
    public RestClient getClient() throws KeyStoreException, NoSuchAlgorithmException, KeyManagementException {

        // 如果有多个从节点可以持续在内部new多个HttpHost，参数1是ip,参数2是HTTP端口，参数3是通信协议

        RestClientBuilder clientBuilder = RestClient.builder(
                new HttpHost("127.0.0.1", 9200, "http")
        );
        // 添加其他配置，返回来的还是RestClientBuilder对象，这些配置都是可选的
        //【1. 设置请求头】 设置请求头，每个请求都会带上这个请求头
        //Header[] defaultHeaders = {new BasicHeader("header", "value")};
        //clientBuilder.setDefaultHeaders(defaultHeaders);
        Header[] defaultHeaders = {new BasicHeader("charset", "utf-8"),
                new BasicHeader("content-type", "application/json")};
        clientBuilder.setDefaultHeaders(defaultHeaders);
        //【2. 设置超时时间】 设置超时时间，多次尝试同一请求时应该遵守的超时。默认值为30秒，与默认套接字超时相同。若自定义套接字超时，则应相应地调整最大重试超时
        //clientBuilder.setMaxRetryTimeoutMillis(60000);
        //【3. 设置节点失败监听器】设置监听器，每次节点失败都可以监听到，可以作额外处理
        /*clientBuilder.setFailureListener(new RestClient.FailureListener() {
            @Override
            public void onFailure(Node node) {
                super.onFailure(node);

                System.out.println(node.getName() + "==节点失败了");

            }

        });*/
        /*【4. 设置节点选择器】 配置节点选择器，客户端以循环方式将每个请求发送到每一个配置的节点上，
        发送请求的节点，用于过滤客户端，将请求发送到这些客户端节点，默认向每个配置节点发送，
        这个配置通常是用户在启用嗅探时向专用主节点发送请求（即只有专用的主节点应该被HTTP请求命中）
        */
       // clientBuilder.setNodeSelector(NodeSelector.SKIP_DEDICATED_MASTERS);
        // 进行详细的配置
       /* clientBuilder.setNodeSelector(new NodeSelector() {
            // 设置分配感知节点选择器，允许选择本地机架中的节点（如果有），否则转到任何机架中的任何其他节点。
            @Override
            public void select(Iterable<Node> nodes) {
                boolean foundOne = false;
                for (Node node: nodes) {
                    String rackId = node.getAttributes().get("rack_id").get(0);
                    if ("rack_one".equals(rackId)) {
                        foundOne = true;
                        break;
                    }
                }
                if (foundOne) {
                    Iterator<Node> nodesIt = nodes.iterator();
                    while (nodesIt.hasNext()) {
                        Node node = nodesIt.next();
                        String rackId = node.getAttributes().get("rack_id").get(0);
                        if ("rack_one".equals(rackId) == false) {
                            nodesIt.remove();
                        }
                    }
                }
            }
        });*/
       /* 【5. 配置HTTP异步请求ES的线程数】
       配置异步请求的线程数量，Apache Http Async Client默认启动一个调度程序线程，以及由连接管理器使用的许多工作线程
        （与本地检测到的处理器数量一样多，取决于Runtime.getRuntime().availableProcessors()返回的数量）。线程数可以修改如下,
        这里是修改为1个线程，即默认情况
        */
        /*clientBuilder.setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
            @Override
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpAsyncClientBuilder) {
                return httpAsyncClientBuilder.setDefaultIOReactorConfig(
                        IOReactorConfig.custom().setIoThreadCount(1).build()
                );
            }
        });*/
        /*【6. 配置连接超时和套接字超时】
        配置请求超时，将连接超时（默认为1秒）和套接字超时（默认为30秒）增加，
        这里配置完应该相应地调整最大重试超时（默认为30秒），即上面的setMaxRetryTimeoutMillis，一般于最大的那个值一致即60000
        */
       /* clientBuilder.setRequestConfigCallback(new RestClientBuilder.RequestConfigCallback() {
            @Override
            public RequestConfig.Builder customizeRequestConfig(RequestConfig.Builder requestConfigBuilder) {
                // 连接5秒超时，套接字连接60s超时
                return requestConfigBuilder.setConnectTimeout(5000).setSocketTimeout(60000);
            }
        });*/
       /*【7. 配置ES安全认证】
        如果ES设置了密码，那这里也提供了一个基本的认证机制，下面设置了ES需要基本身份验证的默认凭据提供程序
            */
        /*final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
        credentialsProvider.setCredentials(AuthScope.ANY,
                new UsernamePasswordCredentials("user", "password"));
        clientBuilder.setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
            @Override
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
            }
        });*/
        /*
        上面采用异步机制实现抢先认证，这个功能也可以禁用，这意味着每个请求都将在没有授权标头的情况下发送，然后查看它是否被接受，
        并且在收到HTTP 401响应后，它再使用基本认证头重新发送完全相同的请求，这个可能是基于安全、性能的考虑
         */
        /*clientBuilder.setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
            @Override
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                // 禁用抢先认证的方式
                httpClientBuilder.disableAuthCaching();
                return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
            }
        });*/
        /* 【8. 配置通信加密】
        配置通信加密，有多种方式：setSSLContext、setSSLSessionStrategy和setConnectionManager(它们的重要性逐渐递增)
         */
        /*KeyStore truststore = KeyStore.getInstance("jks");
        try (InputStream is = Files.newInputStream(keyStorePath)) {
            truststore.load(is, keyStorePass.toCharArray());
        }
        SSLContextBuilder sslBuilder = SSLContexts.custom().loadTrustMaterial(truststore, null);
        final SSLContext sslContext = sslBuilder.build();
        clientBuilder.setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
            @Override
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                return httpClientBuilder.setSSLContext(sslContext);
            }
        });*/
        // 最后配置好的clientBuilder再build一下即可得到真正的Client
        return clientBuilder.build();
    }
}
```

# Email

## 依赖

```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

## 配置

```xml
spring:
  mail:
    #smtp服务主机
    host: smtp.163.com
    #服务协议
    protocol: smtp
    # 编码集
    default-encoding: UTF-8
    #发送邮件的账户
    username: lufeifana@163.com
    #授权码
    password: FOSPTGXAQPGXDUGS
    to: 1714004230@qq.com
    test-connection: true
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true
            required: true
```

## 实例

### entity

```java
package com.leilei.entity;

import java.io.Serializable;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class ToEmail implements Serializable {

  /**
   * 邮件接收方，可多人
   */
  private String[] tos;
  /**
   * 邮件主题
   */
  private String subject;
  /**
   * 邮件内容
   */
  private String content;

}
```

### service

```java
package com.leilei.support;

import com.leilei.entity.ToEmail;
import com.leilei.util.response.JsonReturn;
import javax.mail.MessagingException;
import org.springframework.web.multipart.MultipartFile;

public interface ToEmailService {


  /**
   * @param toEmail 发送对象
   * @return 统一返回ajax
   * @desc 发送普通邮件 （无其他资源 无html 无附件）
   */
  JsonReturn commonEmail(ToEmail toEmail);

  /**
   * @param toEmail 发送对象
   * @return 统一返回ajax
   * @Desc 发送html形式的邮件
   */
  JsonReturn htmlEmail(ToEmail toEmail) throws MessagingException;

  /**
   * 带附件 邮件发送
   */
  JsonReturn enclosureEmail(ToEmail toEmail, MultipartFile multipartFile);

  /**
   * 一同发送静态资源 图片等
   *
   * @param resId 每个资源对饮给一个Id
   */
  JsonReturn staticEmail(ToEmail toEmail, MultipartFile multipartFile, String resId);
}
```

### serviceImpl

```java
package com.leilei.support.impl;

import com.leilei.entity.ToEmail;
import com.leilei.support.ToEmailService;
import com.leilei.util.response.JsonReturn;
import java.io.File;
import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.MailException;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

@Service
@Slf4j
public class ToEmailServiceImpl implements ToEmailService {

  @Autowired
  private JavaMailSender mailSender;

  @Value("${spring.mail.username}")
  private String from;

  /**
   * 发送普通邮件
   *
   * @param toEmail 发送对象
   * @return 统一ajax
   */
  @Override
  public JsonReturn commonEmail(ToEmail toEmail) {
    //创建简单邮件消息
    SimpleMailMessage message = new SimpleMailMessage();
    //谁发的
    message.setFrom(from);
    //谁要接收
    message.setTo(toEmail.getTos());
    //邮件标题
    message.setSubject(toEmail.getSubject());
    //邮件内容
    message.setText(toEmail.getContent());
    try {
      mailSender.send(message);
      return JsonReturn.buildSuccess(toEmail.getContent(), "发送普通邮件成功");
    } catch (MailException e) {
      e.printStackTrace();
      return JsonReturn.buildFailure("普通邮件方失败");
    }
  }

  /**
   * 发送Html 有机胺
   *
   * @param toEmail 发送对象
   */
  @Override
  public JsonReturn htmlEmail(ToEmail toEmail) throws MessagingException {
    //创建一个MINE消息
    MimeMessage message = mailSender.createMimeMessage();
    MimeMessageHelper minehelper = new MimeMessageHelper(message, true);
    //谁发
    minehelper.setFrom(from);
    //谁要接收
    minehelper.setTo(toEmail.getTos());
    //邮件主题
    minehelper.setSubject(toEmail.getSubject());
    //邮件内容   true 表示带有附件或html
    minehelper.setText(toEmail.getContent(), true);
    try {
      mailSender.send(message);
      return JsonReturn.buildSuccess(toEmail.getContent(), "HTML邮件成功");
    } catch (MailException e) {
      e.printStackTrace();
      return JsonReturn.buildFailure("HTML邮件失败");
    }
  }


  /**
   * 带附件邮件发送
   */
  @Override
  public JsonReturn enclosureEmail(ToEmail toEmail, MultipartFile multipartFile) {
    //创建一个MINE消息
    MimeMessage message = mailSender.createMimeMessage();
    try {
      MimeMessageHelper helper = new MimeMessageHelper(message, true);
      //谁发
      helper.setFrom(from);
      //谁接收
      helper.setTo(toEmail.getTos());
      //邮件主题
      helper.setSubject(toEmail.getSubject());
      //邮件内容   true 表示带有附件或html
      helper.setText(toEmail.getContent(), true);
      File multipartFileToFile = MultipartFileToFile(multipartFile);
      FileSystemResource file = new FileSystemResource(multipartFileToFile);
      String filename = file.getFilename();
      helper.addAttachment(filename, file);//添加附件
      mailSender.send(message);
      return JsonReturn.buildSuccess(toEmail.getTos() + toEmail.getContent(), "附件邮件成功");
    } catch (MessagingException e) {
      e.printStackTrace();
      return JsonReturn.buildFailure("附件邮件发送失败" + e.getMessage());
    }
  }

  /**
   * 发送静态资源
   *
   * @param resId 每个资源对饮给一个Id
   */
  @Override
  public JsonReturn staticEmail(ToEmail toEmail, MultipartFile multipartFile, String resId) {
    //创建一个MINE消息
    MimeMessage message = mailSender.createMimeMessage();
    try {
      MimeMessageHelper helper = new MimeMessageHelper(message, true);
      //谁发
      helper.setFrom(from);
      //谁接收
      helper.setTo(toEmail.getTos());
      //邮件主题
      helper.setSubject(toEmail.getSubject());
      //邮件内容   true 表示带有附件或html
      //邮件内容拼接
      String content =
          "<html><body><img width='250px' src=\'cid:" + resId + "\'>" + toEmail.getContent()
              + "</body></html>";
      helper.setText(content, true);
      //蒋 multpartfile 转为file
      File multipartFileToFile = MultipartFileToFile(multipartFile);
      FileSystemResource res = new FileSystemResource(multipartFileToFile);

      //添加内联资源，一个id对应一个资源，最终通过id来找到该资源
      helper.addInline(resId, res);
      mailSender.send(message);
      return JsonReturn.buildSuccess(toEmail.getTos() + toEmail.getContent(), "嵌入静态资源的邮件已经发送");
    } catch (MessagingException e) {
      return JsonReturn.buildFailure("嵌入静态资源的邮件发送失败");
    }
  }


  /**
   * 将 multpartfile 转为file
   *
   * @return file
   */
  private File MultipartFileToFile(MultipartFile multiFile) {
    // 获取文件名
    String fileName = multiFile.getOriginalFilename();
    // 获取文件后缀
    String prefix = fileName.substring(fileName.lastIndexOf("."));
    // 若需要防止生成的临时文件重复,可以在文件名后添加随机码

    try {
      File file = File.createTempFile(fileName, prefix);
      multiFile.transferTo(file);
      return file;
    } catch (Exception e) {
      e.printStackTrace();
    }
    return null;
  }
}
```

### 接口封装

```java
package com.leilei.util.response;

/**
 * @ClassName JSONMgrReturn @Author leilei @Date 2019-08-28 09:21 @Description
 */
public class JsonReturn {

  private Boolean head;
  private Integer status;
  private String message;
  private Object content;

  private static boolean SUCCESS = true;
  private static boolean FAILURE = false;

  public JsonReturn() {
  }

  public JsonReturn(Boolean head, Object content, Integer status, String message) {
    try {
      this.head = head;
      this.status = status;
      this.message = message;
      this.content = content;
    } catch (Exception e) {
      e.printStackTrace();
    }
  }

  public static JsonReturn build(boolean head, Object content, Integer status, String message) {
    return new JsonReturn(head, content, status, message);
  }

  public static JsonReturn buildSuccess(Object content, Integer status) {
    return build(SUCCESS, content, status, "操作成功");
  }

  public static JsonReturn buildSuccess(Object content) {
    return buildSuccess(content, ServerCode.DEFAULT);
  }

  public static JsonReturn buildSuccess(Object content, String message) {
    return buildSuccess(content, ServerCode.DEFAULT, message);
  }

  public static JsonReturn buildSuccess(Object content, Integer status, String message) {
    return build(SUCCESS, content, status, message);
  }

  public static JsonReturn buildSuccessEmptyBody() {
    return buildSuccess("");
  }

  public static JsonReturn buildFailure(String message, Integer status) {
    return build(FAILURE, "", status, message);
  }

  public static JsonReturn buildFailure(String message) {
    return buildFailure(message, ServerCode.FAILURE);
  }

  public static JsonReturn buildFailureContent(Object content, int state, String message) {
    return build(FAILURE, content, state, message);
  }

  public Boolean getHead() {
    return head;
  }

  public void setHead(Boolean head) {
    this.head = head;
  }

  public Object getContent() {
    return content;
  }

  public void setContent(Object content) {
    this.content = content;
  }

  public Integer getStatus() {
    return status;
  }

  public void setStatus(Integer status) {
    this.status = status;
  }

  public String getMessage() {
    return message;
  }

  public void setMessage(String message) {
    this.message = message;
  }

  @Override
  public String toString() {
    return "JSONMgrReturn{"
        + "head="
        + head
        + ", status="
        + status
        + ", message='"
        + message
        + '\''
        + ", content="
        + content
        + '}';
  }
}
```

### 状态码

```java
package com.leilei.util.response;

/**
 * 服务端返回码 Created by leilei on 2019/8/28.
 */
public class ServerCode {

  /**
   * 成功
   */
  public static final int SUCCESS = 200;

  /**
   * 默认状态
   */
  public static final int DEFAULT = SUCCESS;

  /**
   * 失败
   */
  public static final int FAILURE = -1;

  /**
   * 错误
   */
  public static final int ERROR = -2;

  /**
   * 登录失效
   */
  public static final int EXPIRE = 1000;

  /**
   * 需要支付
   */
  public static final Integer NEED_PAY = 8100;
}
```

### controller

```
package com.leilei.controller;

import com.leilei.entity.ToEmail;
import com.leilei.support.ToEmailService;
import com.leilei.util.response.JsonReturn;
import javax.mail.MessagingException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

/**
 * @author leilei
 * @version 1.0
 * @date 2020/3/12 15:07
 * @desc:
 */
@RestController
@RequestMapping("email")
public class EmailController {

  @Autowired
  private ToEmailService toEmailService;

  /**
   * 测试普通邮件发送
   */
  @RequestMapping("common")
  public JsonReturn common(ToEmail toEmail) {
    toEmail.setTos(new String[]{"1714004230@qq.com"});
    toEmail.setSubject("subject");
    toEmail.setContent("content");
    return toEmailService.commonEmail(toEmail);
  }

  /**
   * html 类型邮件发送
   */
  @RequestMapping("/html")
  public JsonReturn html(ToEmail toEmail) throws MessagingException {
    toEmail.setTos(new String[]{"1714004230@qq.com","1302883853@qq.com"});
    toEmail.setSubject("subject");
    toEmail.setContent("<h1>然后就是写一个返回的工具类ResultUtil</h1>");
    return toEmailService.htmlEmail(toEmail);
  }

  /**
   * 发送附件 邮件发送
   */
  @RequestMapping("enclosure")
  public JsonReturn enclosureEmail(ToEmail toEmail, MultipartFile multipartFile) {
    return toEmailService.enclosureEmail(toEmail, multipartFile);
  }

  /**
   * 发送包含静态资源的文件
   *
   * @param toEmail 接收方
   * @param multipartFile 静态资源
   * @param resId 资源唯一Id(随意传，唯一即可）
   */
  @RequestMapping("static")
  public JsonReturn Static(ToEmail toEmail, MultipartFile multipartFile, String resId) {
    return toEmailService.staticEmail(toEmail, multipartFile, resId);
  }
}
```

# 流量控制

## 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.google.guava/guava -->
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>29.0-jre</version>
</dependency>
```

## 装饰器

```java
package com.security.learn.config;

import java.lang.annotation.*;

@Inherited
@Documented
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RateLimit {
    double limitNum() default 20;  //默认每秒放入桶中的token
}
```

## 结果封装

```java
package com.security.learn.dao;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.List;

@Data
public class MyResult {
        private Integer status;
        private String msg;
        private List<Object> data;
 
        public MyResult(Integer status, String msg, List<Object> data) {
            this.status = status;
            this.msg = msg;
            this.data = data;
        }
 
        public static MyResult OK(String msg, List<Object> data) {
            return new MyResult(200, msg, data);
        }
 
        public static MyResult Error(Integer status, String msg) {
            return new MyResult(status, msg, null);
        }
}
```

## 配置

```java
package com.security.learn.dao;

import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.google.common.util.concurrent.RateLimiter;
import com.security.learn.config.RateLimit;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.lang.reflect.Method;
import java.util.concurrent.ConcurrentHashMap;


@Component
@Scope
@Aspect
public class RateLimitAspect {
    private Logger log = LoggerFactory.getLogger(this.getClass());
    //用来存放不同接口的RateLimiter(key为接口名称，value为RateLimiter)
    private ConcurrentHashMap<String, RateLimiter> map = new ConcurrentHashMap<>();
 
    private static ObjectMapper objectMapper = new ObjectMapper();
 
    private RateLimiter rateLimiter;
 
    @Resource
    private HttpServletResponse response;
 
    @Pointcut("@annotation(com.security.learn.config.RateLimit)")
    public void serviceLimit() {
    }
 
    @Around("serviceLimit()")
    public Object around(ProceedingJoinPoint joinPoint) throws NoSuchMethodException {
        Object obj = null;
        //获取拦截的方法名
        Signature sig = joinPoint.getSignature();
        //获取拦截的方法名
        MethodSignature msig = (MethodSignature) sig;
        //返回被织入增加处理目标对象
        Object target = joinPoint.getTarget();
        //为了获取注解信息
        Method currentMethod = target.getClass().getMethod(msig.getName(), msig.getParameterTypes());
        //获取注解信息
        RateLimit annotation = currentMethod.getAnnotation(RateLimit.class);
        double limitNum = annotation.limitNum(); //获取注解每秒加入桶中的token
        String functionName = msig.getName(); // 注解所在方法名区分不同的限流策略
 
        //获取rateLimiter
         if(map.containsKey(functionName)){
             rateLimiter = map.get(functionName);
         }else {
             map.put(functionName, RateLimiter.create(limitNum));
             rateLimiter = map.get(functionName);
         }
 
        try {
            if (rateLimiter.tryAcquire()) {
                //执行方法
                obj = joinPoint.proceed();
            } else {
                //拒绝了请求（服务降级）
                String result = objectMapper.writeValueAsString(MyResult.Error(500, "系统繁忙！"));
                log.info("拒绝了请求：" + result);
                outErrorResult(result);
            }
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        return obj;
    }
    //将结果返回
    public void outErrorResult(String result) {
        response.setContentType("application/json;charset=UTF-8");
        try (ServletOutputStream outputStream = response.getOutputStream()) {
            outputStream.write(result.getBytes("utf-8"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static {
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
    }
 
}
```

## 实例

### controller

```java
package com.security.learn.controller;

import com.security.learn.config.RateLimit;
import com.security.learn.dao.MyResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class LimitController {
//    2个接口设定没秒限流5个和美妙限流10个

    @RateLimit(limitNum = 1.0)
    @RequestMapping("xian")
    public MyResult getResults() {
        return MyResult.OK("调用了方法", null);
    }
    @RequestMapping("xian1")
    @RateLimit(limitNum = 10.0)
    public MyResult getResultTwo() {
        return MyResult.OK("调用了方法getResultTwo", null);
    }
}
```

# springboot admin

## server端

### 依赖

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>2.2.4</version>
</dependency>
<!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-server-ui -->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-server-ui</artifactId>
    <version>2.2.4</version>
</dependency>
```

### 配置

```
server.port=8888
spring.application.name=admin-server
spring.boot.admin.probed-endpoints=info
```

### 启用

在启动类添加@EnableAdminServer修饰器

```java
@EnableAdminServer
```

![image-20200809231311404](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200809231311404.png)

## client端

### 依赖

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.2.4</version>
</dependency>
```

### 配置

```
server.port=8002
spring.application.name=admin-client
spring.boot.admin.client.url=http://localhost:8888
#spring.boot.admin.client.instance.service-url=http://localhost:8888
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=ALWAYS
spring.boot.admin.client.auto-registration=true
```

# shiro

> **SecurityManager****：**相当于SpringMVC中的DispatcherServlet或者Struts2中的FilterDispatcher；是Shiro的心脏；所有具体的交互都通过SecurityManager进行控制；它管理着所有Subject、且负责进行认证和授权、及会话、缓存的管理。
>
> **Authenticator****：**认证器，负责主体认证的，这是一个扩展点，如果用户觉得Shiro默认的不好，可以自定义实现；其需要认证策略（Authentication Strategy），即什么情况下算用户认证通过了；
>
> **Authrizer****：**授权器，或者访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；
>
> **Realm****：**可以有1个或多个Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是JDBC实现，也可以是LDAP实现，或者内存实现等等；由用户提供；注意：Shiro不知道你的用户/权限存储在哪及以何种格式存储；所以我们一般在应用中都需要实现自己的Realm；
>
> **SessionManager****：**如果写过Servlet就应该知道Session的概念，Session呢需要有人去管理它的生命周期，这个组件就是SessionManager；而Shiro并不仅仅可以用在Web环境，也可以用在如普通的JavaSE环境、EJB等环境；所有呢，Shiro就抽象了一个自己的Session来管理主体与应用之间交互的数据；这样的话，比如我们在Web环境用，刚开始是一台Web服务器；接着又上了台EJB服务器；这时想把两台服务器的会话数据放到一个地方，这个时候就可以实现自己的分布式会话（如把数据放到Memcached服务器）；
>
> **SessionDAO****：**DAO大家都用过，数据访问对象，用于会话的CRUD，比如我们想把Session保存到数据库，那么可以实现自己的SessionDAO，通过如JDBC写到数据库；比如想把Session放到Memcached中，可以实现自己的Memcached SessionDAO；另外SessionDAO中可以使用Cache进行缓存，以提高性能；
>
> **CacheManager****：**缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能
>
> **Cryptography****：**密码模块，Shiro提高了一些常见的加密组件用于如密码加密/解密的。

## 依赖

```
<!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-spring -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.5.3</version>
</dependency>
```

## 配置

### 配置类

```java
package com.example.demo;

import com.example.demo.shiro.CustomRealm;
import com.example.demo.shiro.ShiroFilterMapFactory;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.cache.CacheManager;
import org.apache.shiro.cache.MemoryConstrainedCacheManager;
import org.apache.shiro.mgt.RememberMeManager;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.realm.Realm;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.CookieRememberMeManager;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.apache.shiro.web.servlet.Cookie;
import org.apache.shiro.web.servlet.SimpleCookie;
import org.apache.shiro.web.session.mgt.DefaultWebSessionManager;
import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ShiroConfig {

    /**
     * 这是shiro的大管家，相当于mybatis里的SqlSessionFactoryBean
     * @param securityManager
     * @return
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(org.apache.shiro.mgt.SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //登录
        shiroFilterFactoryBean.setLoginUrl("/login");
        //首页
        shiroFilterFactoryBean.setSuccessUrl("/index");
        //错误页面，认证不通过跳转
        shiroFilterFactoryBean.setUnauthorizedUrl("/403");
        //页面权限控制
        shiroFilterFactoryBean.setFilterChainDefinitionMap(ShiroFilterMapFactory.shiroFilterMap());
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        return shiroFilterFactoryBean;
    }

    /**
     * web应用管理配置
     * @param shiroRealm
     * @param cacheManager
     * @param manager
     * @return
     */
    @Bean
    public DefaultWebSecurityManager securityManager(Realm shiroRealm, CacheManager cacheManager, RememberMeManager manager) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setCacheManager(cacheManager);
        securityManager.setRememberMeManager(manager);//记住Cookie
        securityManager.setRealm(shiroRealm);
        securityManager.setSessionManager(sessionManager());
        return securityManager;
    }
    /**
     * session过期控制
     * @return
     * @author fuce
     * @Date 2019年11月2日 下午12:49:49
     */
    @Bean
    public DefaultWebSessionManager sessionManager() {
        DefaultWebSessionManager defaultWebSessionManager=new DefaultWebSessionManager();
        // 设置session过期时间3600s
        Long timeout=60L*1000*60;//毫秒级别
        defaultWebSessionManager.setGlobalSessionTimeout(timeout);
        return defaultWebSessionManager;
    }
    /**
     * 加密算法
     * @return
     */
    @Bean
    public HashedCredentialsMatcher hashedCredentialsMatcher() {
        HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
        hashedCredentialsMatcher.setHashAlgorithmName("MD5");//采用MD5 进行加密
        hashedCredentialsMatcher.setHashIterations(1);//加密次数
        return hashedCredentialsMatcher;
    }

    /**
     * 记住我的配置
     * @return
     */
    @Bean
    public RememberMeManager rememberMeManager() {
        Cookie cookie = new SimpleCookie("rememberMe");
        cookie.setHttpOnly(true);//通过js脚本将无法读取到cookie信息
        cookie.setMaxAge(60 * 60 * 24);//cookie保存一天
        CookieRememberMeManager manager=new CookieRememberMeManager();
        manager.setCookie(cookie);
        return manager;
    }
    /**
     * 缓存配置
     * @return
     */
    @Bean
    public CacheManager cacheManager() {
        MemoryConstrainedCacheManager cacheManager=new MemoryConstrainedCacheManager();//使用内存缓存
        return cacheManager;
    }

    /**
     * 配置realm，用于认证和授权
     * @param hashedCredentialsMatcher
     * @return
     */
    @Bean
    public AuthorizingRealm shiroRealm(HashedCredentialsMatcher hashedCredentialsMatcher) {
        CustomRealm shiroRealm = new CustomRealm();
        //校验密码用到的算法
//        shiroRealm.setCredentialsMatcher(hashedCredentialsMatcher);
        return shiroRealm;
    }

    /**
     * 启用shiro方言，这样能在页面上使用shiro标签
     * @return
     */
//    @Bean
//    public ShiroDialect shiroDialect() {
//        return new ShiroDialect();
//    }

    /**
     * 启用shiro注解
     *加入注解的使用，不加入这个注解不生效
     */
    @Bean
    public AuthorizationAttributeSourceAdvisor getAuthorizationAttributeSourceAdvisor(org.apache.shiro.mgt.SecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor advisor = new AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager);
        return advisor;
    }

    @Bean
    public DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator(){
        DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        advisorAutoProxyCreator.setProxyTargetClass(true);
        return advisorAutoProxyCreator;
    }
    
}
```

## Realm

```java
package com.example.demo.shiro;


import com.example.demo.entity.Permissions;
import com.example.demo.entity.Role;
import com.example.demo.entity.User;
import com.example.demo.service.LoginService;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;

public class CustomRealm extends AuthorizingRealm {

    @Autowired
    private LoginService loginService;

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        //获取登录用户名
        String name = (String) principalCollection.getPrimaryPrincipal();
        //查询用户名称
        User user = loginService.getUserByName(name);
        //添加角色和权限
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        for (Role role : user.getRoles()) {
            //添加角色
            simpleAuthorizationInfo.addRole(role.getRoleName());
            //添加权限
            for (Permissions permissions : role.getPermissions()) {
                simpleAuthorizationInfo.addStringPermission(permissions.getPermissionsName());
            }
        }
        return simpleAuthorizationInfo;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        //加这一步的目的是在Post请求的时候会先进认证，然后在到请求
        if (authenticationToken.getPrincipal() == null) {
            return null;
        }
        //获取用户信息
        String name = authenticationToken.getPrincipal().toString();
        User user = loginService.getUserByName(name);
        if (user == null) {
            //这里返回后会报出对应异常
            return null;
        } else {
            //这里验证authenticationToken和simpleAuthenticationInfo的信息
            SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(name, user.getPassword().toString(), getName());
            return simpleAuthenticationInfo;
        }
    }
}
```

## 全局异常捕获

```
package com.example.demo.hand;

import org.apache.shiro.authz.AuthorizationException;
import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

//全局异常捕获
@ControllerAdvice
@Order(value = Ordered.HIGHEST_PRECEDENCE)
public class GlobalExceptionHandler {
    @ExceptionHandler(value = AuthorizationException.class)
    public String handleAuthorizationException() {
        return "403";
    }
}
```

# SpringBoot cache 整合 Redis

## 依赖

```
<!--redis依赖配置-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



## 配置

### 启动类添加

```
@EnableCaching 
```

![image-20200817214312280](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200817214312280.png)

### 配置类

```
package com.cache.demo.config;
 
import java.time.Duration;
 
import org.springframework.cache.CacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.cache.RedisCacheWriter;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;
 
@Configuration
public class RedisConfig {
 
   @Bean
   public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
 
      RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
      redisTemplate.setConnectionFactory(redisConnectionFactory);

      redisTemplate.setKeySerializer(keySerializer());
      redisTemplate.setHashKeySerializer(keySerializer());

      redisTemplate.setValueSerializer(valueSerializer());
      redisTemplate.setHashValueSerializer(valueSerializer());

      return redisTemplate;
   }

// 缓存管理
   @Bean
   public CacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
      // 缓存配置对象
      RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();
 
      redisCacheConfiguration = redisCacheConfiguration.entryTtl(Duration.ofMinutes(30L)) // 设置缓存的默认超时时间：30分钟
            .disableCachingNullValues() // 如果是空值，不缓存
            .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(keySerializer())) // 设置key序列化器
            .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer((valueSerializer()))); // 设置value序列化器
 
      return RedisCacheManager.builder(RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory))
            .cacheDefaults(redisCacheConfiguration).build();
   }

// 序列化
   private RedisSerializer<String> keySerializer() {
      return new StringRedisSerializer();
   }
 
   private RedisSerializer<Object> valueSerializer() {
      return new GenericJackson2JsonRedisSerializer();
   }
}
```

### 常用配置

```
server:
  port: 8080

spring:
  datasource:
    name: test
    url: jdbc:mysql://127.0.0.1:3307/test?useUnicode=true&useSSL=false&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: root

  redis:
    host: localhost
    database: 0
    port: 6379
    password:
    jedis:
      pool:
        max-active: 8
        max-wait: -1ms
        max-idle: 8
        min-idle: 0
    timeout: 3000ms

    key:
      prefix:
        authCode: "portal:authCode:"
      expire:
        authCode: 120

# mybatis config
mybatis-plus:
  mapper-locations: classpath:mappers/*.xml
  type-aliases-package: com.cache.demo.model
  global-config:
    db-config:
      id-type: auto
      #日志
#  configuration:
#    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## 使用

控制类添加注解

![image-20200817214505805](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200817214505805.png)