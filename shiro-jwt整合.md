# 环境依赖包

```xml
<!--        jwt-->
        <!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.10.3</version>
        </dependency>

        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.6.0</version>
        </dependency>
<!--mybatis plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.3.2</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.3.2</version>
        </dependency>
        <!--        模板引擎需要-->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.0</version>
        </dependency>
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

# shiro配置

```java
package com.shirojwt.demo.config;

import com.shirojwt.demo.jwt.JwtFilter;
import com.shirojwt.demo.shiro.MyRealm;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.mgt.DefaultSessionStorageEvaluator;
import org.apache.shiro.mgt.DefaultSubjectDAO;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;
import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {

    //   自定义验证方式
    @Bean
    public AuthorizingRealm myRealm(){
        MyRealm myRealm = new MyRealm();
        return myRealm;
    }
// 权限管理
    @Bean
    public SecurityManager mysecurityManager(){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(myRealm());
        return securityManager;
    }
    /**
     * Shiro内置过滤器，可以实现权限相关的拦截器
     *    常用的过滤器：
     *       anon: 无需认证（登录）可以访问
     *       authc: 必须认证才可以访问
     *       user: 如果使用rememberMe的功能可以直接访问
     *       perms： 该资源必须得到资源权限才可以访问
     *       roles: 该资源必须得到角色权限才可以访问
     */
    @Bean
    public ShiroFilterFactoryBean filterFactoryBean(){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        bean.setSecurityManager(mysecurityManager());

        Map<String, Filter> filters = new HashMap<>();
        filters.put("jwt", new JwtFilter());
        bean.setFilters(filters);

        Map<String, String> map = new LinkedHashMap<>();
        //登出
        map.put("/logout", "logout");
        map.put("/hello", "jwt");
        //对所有用户认证
//        map.put("/**", "authc");
//        map.put("/get", "perms[user:add]");
//        map.put("/get", "roles[admin]");
        //登录
        bean.setLoginUrl("/login");
//        //首页
//        bean.setSuccessUrl("/get1");
//        //错误页面，认证不通过跳转
//        bean.setUnauthorizedUrl("/error");
        bean.setFilterChainDefinitionMap(map);
        return bean;
    }

    /**
     *  开启Shiro的注解(如@RequiresRoles,@RequiresPermissions),需借助SpringAOP扫描使用Shiro注解的类,并在必要时进行安全逻辑验证
     * 配置以下两个bean(DefaultAdvisorAutoProxyCreator和AuthorizationAttributeSourceAdvisor)即可实现此功能
     * @return
     */
    @Bean
    public DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator(){
        DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        advisorAutoProxyCreator.setProxyTargetClass(true);
        return advisorAutoProxyCreator;
    }

    /**
     * 开启aop注解支持
     * @return
     */
    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor() {
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        authorizationAttributeSourceAdvisor.setSecurityManager(mysecurityManager());
        return authorizationAttributeSourceAdvisor;
    }

}
```

# 自定义Realm

```java
package com.shirojwt.demo.shiro;

import com.auth0.jwt.interfaces.Claim;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;

import com.shirojwt.demo.entity.User;
import com.shirojwt.demo.jwt.JwtUtil;
import com.shirojwt.demo.service.UserService;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;

public class MyRealm extends AuthorizingRealm {

    @Autowired
    private JwtUtil jwtUtil;
    @Autowired
    private UserService userService;

    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof JwtToken;
    }

//    授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
//获取登录用户名
        return null;
    }

    //    认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {

        JwtToken jwtToken = (JwtToken) authenticationToken;

        String token = (String) jwtToken.getPrincipal();
        /*从 token 中获取用户名*/
        String username = jwtUtil.getUsername(token);
        /*到数据库查询用户名是否存在*/
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("name",username);
        User user = userService.getOne(queryWrapper);
        /*用户不存在返回空，否则返回用户信息*/
        if (user==null){
            return null;
        }else {
            return new SimpleAuthenticationInfo(username, jwtToken.getCredentials(), getName());
        }
    }
}
```

# 自定义Jwt过滤器

```java
package com.shirojwt.demo.jwt;

import com.shirojwt.demo.shiro.JwtToken;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.ExpiredCredentialsException;
import org.apache.shiro.web.filter.authc.AuthenticatingFilter;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;

@Component
public class JwtFilter extends AuthenticatingFilter {
    /**
     * 第二步
     * @param servletRequest
     * @param servletResponse
     * @return
     * @throws Exception
     */
    @Override
    protected AuthenticationToken createToken(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
//        获取客户端的jwt
        String jwt = request.getHeader("Authorization");
        System.out.println("createToken jwt:"+jwt);
        if(StringUtils.isEmpty(jwt)) {
            return null;
        }
//        返回jwt
        return new JwtToken(jwt);
    }
//进行信息效验，效验成功执行登录

    /**
     * 第一步
     * @param servletRequest
     * @param servletResponse
     * @return
     * @throws Exception
     */

    @Override
    protected boolean onAccessDenied(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        String jwt = request.getHeader("Authorization");
        System.out.println("onAccessDenied jwt:"+jwt);
        /**
         * jwt 为空抛出异常
         */
        if(StringUtils.isEmpty(jwt)) {
            throw new ExpiredCredentialsException("token为空");
//            return false;
        } else {
        /**
         * 校验jwt是否正确，是否过期
         */
            boolean claim = new JwtUtil().verify(jwt, new JwtUtil().getUsername(jwt));
            if (claim){
                System.out.println("true");
                return executeLogin(servletRequest, servletResponse);
            }else {
                throw new ExpiredCredentialsException("token不合法");
            }
            // 执行登录

        }
    }
}
```

# 自定义AuthenticationToken

（用于收集用户提交的身份（如用户名）及凭据（如密码）

```java
package com.shirojwt.demo.shiro;

import org.apache.shiro.authc.AuthenticationToken;

public class JwtToken implements AuthenticationToken {

    private String token;

    public JwtToken(String token) {
        this.token = token;
    }

    /**
     * principals:身份即主体的标识属性,可以是任何东西.如用户名,邮箱等.唯一即可.
     * 一个主体可以有多个principals，但只有一个Primary principals，一般是用户名/密码/手机号。
     * @return
     */

    @Override
    public Object getPrincipal() {

        System.out.println("++++++++++++++++++++getPrincipal+++++++++++++++++++++++");
        return token;
    }

    /**
     * credentials:证明/凭证，即只有主体知道的安全值。如密码
     * @return
     */
    @Override
    public Object getCredentials() {
        System.out.println("====================getCredentials==================");
        return token;
    }
}
```

# Token生成工具类

```java
package com.shirojwt.demo.jwt;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTDecodeException;
import com.auth0.jwt.interfaces.DecodedJWT;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * @author huwen
 */
@Component
public class JwtUtil {
    /**
     * 设置过期时间24小时
     */
    private static final long EXPIRE_TIME = 1000*60;
    /**
     * 设置密钥
     */
    private static final String SECRET = "shiro+jwt";

    /**
     * 根据用户名创建一个token
     * @param username 用户名
     * @return 返回的token字符串
     */
    public String createToken(String username){
        try {
            //将当前时间的毫秒数和设置的过期时间相加生成一个新的时间
            Date date = new Date(System.currentTimeMillis()+EXPIRE_TIME);
            //由密钥创建一个指定的算法
            return JWT.create()
                    //附带username信息
                    .withClaim("username",username)
                    //附带过期时间
                    .withExpiresAt(date)
                    //使用指定的算法进行标记
                    .sign(Algorithm.HMAC256(SECRET));
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 验证token是否正确
     * @param token 前端传过来的token
     * @param username 用户名
     * @return 返回boolean
     */
    public boolean verify(String token,String username){
        try {
            //获取算法
            Algorithm algorithm = Algorithm.HMAC256(SECRET);
            //生成JWTVerifier
            JWTVerifier verifier = JWT.require(algorithm)
                    .withClaim("username",username)
                    .build();
            //验证token
            verifier.verify(token);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 从token中获得username，无需secret
     * @param token token
     * @return username
     */
    public String getUsername(String token){
        try {
            DecodedJWT jwt = JWT.decode(token);
            return jwt.getClaim("username").asString();
        } catch (JWTDecodeException e) {
            return null;
        }
    }
}
```

# 登录控制controller

```java
package com.shirojwt.demo.controller;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.shirojwt.demo.entity.User;
import com.shirojwt.demo.jwt.JwtUtil;
import com.shirojwt.demo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;

@RestController
public class TestController {

    @Autowired
    private UserService userService;

    @RequestMapping("/hello")
    public String hello(){
        return "hello world";
    }

    @RequestMapping("/logintest/{username}/{password}")
    public String loginTest(@PathVariable String username,@PathVariable String password, HttpServletResponse response){
//       1. 到数据库查找相关的用户
        User user = userService.getOne(new QueryWrapper<User>().eq("name", username));
        if (user==null){
            return "不存在改用户";
        }
//        匹配密码是否正确
        if (!user.getPassword().equals(password)){
            return "密码错误";
        }
//       2.生成jwt
        String jwt = new JwtUtil().createToken(username);
//       3.返回携带有jwt 的请求信息给客户端
        response.setHeader("Authorization", jwt);
        response.setHeader("Access-control-Expose-Headers", "Authorization");
//       4.返回封装请求结果
        return "ok";
    }
}
```

# 代码生成

```java
package com.shirojwt.demo.mybatisplusgener;

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
        globalConfig.setAuthor("LuLuLua");
        globalConfig.setServiceName("%sService");
//        globalConfig.setControllerName("%sController");
//        globalConfig.setEntityName("%sEntity");
//        globalConfig.setMapperName("%sMapper");
//        globalConfig.setServiceImplName("%sServiceImpl");
        globalConfig.setSwagger2(true);
        globalConfig.setOpen(false);
        globalConfig.setBaseResultMap(true);
        globalConfig.setBaseColumnList(true);
        autoGenerator.setGlobalConfig(globalConfig);

        // 数据源配置 需配置
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setUrl("jdbc:mysql://127.0.0.1:3307/shiro?serverTimezone=UTC");
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
        packageConfig.setParent("com.shirojwt.demo");
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

# application.yml

```yml
server:
  port: 8081

spring:
  datasource:
    name: test
    url: jdbc:mysql://127.0.0.1:3307/shiro?useUnicode=true&useSSL=false&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: root

# mybatis config
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
```

