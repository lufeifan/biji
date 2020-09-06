# ShiroConfig

```java
@Configuration
public class SpringShiroConfig {
    @Bean
    public Realm customRealm() {
        return new CustomRealm();
    }
    @Bean
    public DefaultWebSecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(customRealm());
        // 关闭 ShiroDAO 功能
        DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
        DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
        // 不需要将 Shiro Session 中的东西存到任何地方（包括 Http Session 中）
        defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
        subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
        securityManager.setSubjectDAO(subjectDAO);
        return securityManager;
    }
    @Bean
    public ShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition chain = new DefaultShiroFilterChainDefinition();
        // 哪些请求可以匿名访问
        chain.addPathDefinition("/login", "anon");      // 登录接口
        chain.addPathDefinition("/notLogin", "anon");   // 未登录错误提示接口
        chain.addPathDefinition("/403", "anon");    // 权限不足错误提示接口
        // 除了以上的请求外，其它请求都需要登录
        chain.addPathDefinition("/**", "authc");
        return chain;
    }
    // Shiro 和 Spring AOP 整合时的特殊设置
    @Bean
    public DefaultAdvisorAutoProxyCreator getDefaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator creator = new DefaultAdvisorAutoProxyCreator();
        creator.setProxyTargetClass(true);
        return creator;
    }
}

//还有关闭ShiroDao功能
```

# JWT

https://www.jianshu.com/p/e88d3f8151db

```java
package cn.coderymy.utils;

import java.util.*;
import com.auth0.jwt.*;
import com.auth0.jwt.algorithms.Algorithm;
import io.jsonwebtoken.*;
import org.apache.commons.codec.binary.Base64;

import java.util.*;


public class JwtUtil {

    // 生成签名是所使用的秘钥
    private final String base64EncodedSecretKey;

    // 生成签名的时候所使用的加密算法
    private final SignatureAlgorithm signatureAlgorithm;

    public JwtUtil(String secretKey, SignatureAlgorithm signatureAlgorithm) {
        this.base64EncodedSecretKey = Base64.encodeBase64String(secretKey.getBytes());
        this.signatureAlgorithm = signatureAlgorithm;
    }

    /**
     * 生成 JWT Token 字符串
     *
     * @param iss       签发人名称
     * @param ttlMillis jwt 过期时间
     * @param claims    额外添加到荷部分的信息。
     *                  例如可以添加用户名、用户ID、用户（加密前的）密码等信息
     */
    public String encode(String iss, long ttlMillis, Map<String, Object> claims) {
        if (claims == null) {
            claims = new HashMap<>();
        }

        // 签发时间（iat）：荷载部分的标准字段之一
        long nowMillis = System.currentTimeMillis();
        Date now = new Date(nowMillis);

        // 下面就是在为payload添加各种标准声明和私有声明了
        JwtBuilder builder = Jwts.builder()
                // 荷载部分的非标准字段/附加字段，一般写在标准的字段之前。
                .setClaims(claims)
                // JWT ID（jti）：荷载部分的标准字段之一，JWT 的唯一性标识，虽不强求，但尽量确保其唯一性。
                .setId(UUID.randomUUID().toString())
                // 签发时间（iat）：荷载部分的标准字段之一，代表这个 JWT 的生成时间。
                .setIssuedAt(now)
                // 签发人（iss）：荷载部分的标准字段之一，代表这个 JWT 的所有者。通常是 username、userid 这样具有用户代表性的内容。
                .setSubject(iss)
                // 设置生成签名的算法和秘钥
                .signWith(signatureAlgorithm, base64EncodedSecretKey);

        if (ttlMillis >= 0) {
            long expMillis = nowMillis + ttlMillis;
            Date exp = new Date(expMillis);
            // 过期时间（exp）：荷载部分的标准字段之一，代表这个 JWT 的有效期。
            builder.setExpiration(exp);
        }

        return builder.compact();
    }


    /**
     * JWT Token 由 头部 荷载部 和 签名部 三部分组成。签名部分是由加密算法生成，无法反向解密。
     * 而 头部 和 荷载部分是由 Base64 编码算法生成，是可以反向反编码回原样的。
     * 这也是为什么不要在 JWT Token 中放敏感数据的原因。
     *
     * @param jwtToken 加密后的token
     * @return claims 返回荷载部分的键值对
     */
    public Claims decode(String jwtToken) {

        // 得到 DefaultJwtParser
        return Jwts.parser()
                // 设置签名的秘钥
                .setSigningKey(base64EncodedSecretKey)
                // 设置需要解析的 jwt
                .parseClaimsJws(jwtToken)
                .getBody();
    }


    /**
     * 校验 token
     * 在这里可以使用官方的校验，或，
     * 自定义校验规则，例如在 token 中携带密码，进行加密处理后和数据库中的加密密码比较。
     *
     * @param jwtToken 被校验的 jwt Token
     */
    public boolean isVerify(String jwtToken) {
        Algorithm algorithm = null;

        switch (signatureAlgorithm) {
            case HS256:
                algorithm = Algorithm.HMAC256(Base64.decodeBase64(base64EncodedSecretKey));
                break;
            default:
                throw new RuntimeException("不支持该算法");
        }

        JWTVerifier verifier = JWT.require(algorithm).build();
        verifier.verify(jwtToken);  // 校验不通过会抛出异常


        /*
            // 得到DefaultJwtParser
            Claims claims = decode(jwtToken);

            if (claims.get("password").equals(user.get("password"))) {
                return true;
            }
        */

        return true;
    }

    public static void main(String[] args) {
        JwtUtil util = new JwtUtil("tom", SignatureAlgorithm.HS256);

        Map<String, Object> map = new HashMap<>();
        map.put("username", "tom");
        map.put("password", "123456");
        map.put("age", 20);

        String jwtToken = util.encode("tom", 30000, map);

        System.out.println(jwtToken);
        /*
        util.isVerify(jwtToken);
        System.out.println("合法");
        */

        util.decode(jwtToken).entrySet().forEach((entry) -> {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        });
    }
}
```

# 网站

https://www.cnblogs.com/red-star/p/12121941.html

# shiro控制流程图

![](https://img2018.cnblogs.com/blog/1752718/201908/1752718-20190828030006043-608121871.png)

# DefaultSecurityManager



![image-20200818141731634](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200818141731634.png)



## setRealm 

实现认证授权

## setRememberMeManager 

注入Cookie(记住我)管理器

```java
/**
* cookie管理器;
* @return
*/
@Bean
public CookieRememberMeManager rememberMeManager(){
    logger.info("注入Shiro的记住我(CookieRememberMeManager)管理器-->rememberMeManager", CookieRememberMeManager.class);
    CookieRememberMeManager cookieRememberMeManager = new CookieRememberMeManager();
    //rememberme cookie加密的密钥 建议每个项目都不一样 默认AES算法 密钥长度（128 256 512 位），通过以下代码可以获取
    //KeyGenerator keygen = KeyGenerator.getInstance("AES");
    //SecretKey deskey = keygen.generateKey();
    //System.out.println(Base64.encodeToString(deskey.getEncoded()));
    byte[] cipherKey = Base64.decode("wGiHplamyXlVB11UXWol8g==");
    cookieRememberMeManager.setCipherKey(cipherKey);
    cookieRememberMeManager.setCookie(rememberMeCookie());
    return cookieRememberMeManager;
}
@Bean
public SimpleCookie rememberMeCookie(){
    //这个参数是cookie的名称，对应前端的checkbox的name = rememberMe
    SimpleCookie simpleCookie = new SimpleCookie("rememberMe");
    //如果httyOnly设置为true，则客户端不会暴露给客户端脚本代码，使用HttpOnly cookie有助于减少某些类型的跨站点脚本攻击；
    simpleCookie.setHttpOnly(true);
    //记住我cookie生效时间,默认30天 ,单位秒：60 * 60 * 24 * 30
    simpleCookie.setMaxAge(259200);

    return simpleCookie;
}
```

## setSubjectDAO

> SubjectDAO做的事情很少，没有存储Subject，只是将当前Subject的authenticated（是否校验成功）和principal（身份）放入到session中。

**Subject的生命周期：**

1. 每一次访问都会创建一个新的subject对象。
2. 将创建的subject对象绑定到ThreadContext上，之后通过SecurityUtils便可以在程序的任何地方获取到Subject对象。
3. 每一次创建完成subject，都会通过SubjectDAO将subject属性存放到session中去，但是只存放principal（身份）和authenticated（是否校验成功）。

https://blog.csdn.net/Linchack/article/details/106998443

## setSubjectFactory

将SubjectContext 传递值 createSubject

## setAuthenticator

Authenticator就是认证器，在Shiro中负责认证用户提交的信息，在Shiro中我们用AuthenticationToken来表示提交的信息。

https://blog.csdn.net/qh870754310/article/details/83059657

## setCacheManager

缓存管理

https://www.cnblogs.com/woxbwo/p/11421696.html

## setSessionManager

session管理

https://www.jianshu.com/p/a8ab2d1fb61a

# 测试

http://localhost:8080/login?name=b&id=5

# 条件选择器

## QueryWrapper

<table>
<thead>
<tr>
<th>查询方法</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>eq</td>
<td>等于=</td>
</tr>
<tr>
<td>ne</td>
<td>不等与&lt;&gt;</td>
</tr>
<tr>
<td>gt</td>
<td>大于&gt;</td>
</tr>
<tr>
<td>ge</td>
<td>大于等于&gt;=</td>
</tr>
<tr>
<td>lt</td>
<td>小于&lt;</td>
</tr>
<tr>
<td>le</td>
<td>小于等于&lt;=</td>
</tr>
<tr>
<td>like</td>
<td>模糊查询 LIKE</td>
</tr>
<tr>
<td>notLike</td>
<td>模糊查询 NOT LIKE</td>
</tr>
<tr>
<td>in</td>
<td>IN 查询</td>
</tr>
<tr>
<td>notIn</td>
<td>NOT IN 查询</td>
</tr>
<tr>
<td>isNull</td>
<td>NULL 值查询</td>
</tr>
<tr>
<td>isNotNull</td>
<td>IS NOT NULL</td>
</tr>
</tbody>
</table>

```java
QueryWrapper<User> queryWrapper = new QueryWrapper<>();
queryWrapper.eq("name",name);
User user = userService.getOne(queryWrapper);
```

# Shiro模板

## 配置类

```java
package com.cache.demo.config;

import com.cache.demo.shiro.MySRealm;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {

//   自定义验证方式
    @Bean
    public AuthorizingRealm mySRealm(){
        MySRealm mySRealm = new MySRealm();
        return mySRealm;
    }
// 权限管理
    @Bean
    public DefaultSecurityManager mysecurityManager(){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(mySRealm());
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

        Map<String, String> map = new LinkedHashMap<>();
        //登出
        map.put("/logout", "logout");
        //对所有用户认证
        map.put("/get", "authc");
//        map.put("/get", "perms[user:add]");
        map.put("/get", "roles[admin]");
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

## MySRealm类

```java
package com.cache.demo.shiro;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.cache.demo.entity.User;
import com.cache.demo.service.UserService;
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
    private UserService userService;

//    授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
//获取登录用户名
        String name = (String) principalCollection.getPrimaryPrincipal();
        //根据用户名去数据库查询用户信息
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("name",name);
        User user = userService.getOne(queryWrapper);
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

//    认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        if (authenticationToken.getPrincipal() == null){
            return null;
        }
        String name = authenticationToken.getPrincipal().toString();
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("name",name);
        User user = userService.getOne(queryWrapper);
        if (user==null){
            return null;
        }else {
            SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(name,user.getId().toString(),getName());
            return simpleAuthenticationInfo;
        }
    }
}
```



# ShiroFilterFactoryBean

    /**
     * Shiro过滤器配置
     */
    @Bean(name = "shiroFilter")
    public ShiroFilterFactoryBean shiroFilter() {
        ShiroFilterFactoryBean shiroFilter = new ShiroFilterFactoryBean();
        // Shiro的核心安全接口,这个属性是必须的
        shiroFilter.setSecurityManager(securityManager());
//        shiroFilter.setLoginUrl("");//身份认证失败，则跳转到登录页面的配置 没有登录的用户请求需要登录的页面时自动跳转到登录页面，不是必须的属性，不输入地址的话会自动寻找项目web项目的根目录下的”/login.jsp”页面。
//        shiroFilter.setSuccessUrl("");//登录成功默认跳转页面，不配置则跳转至”/”。如果登陆前点击的一个需要登录的页面，则在登录自动跳转到那个需要登录的页面。不跳转到此。
//        shiroFilter.setUnauthorizedUrl("");//没有权限默认跳转的页面
//        shiroFilter.setFilterChainDefinitions("");//filterChainDefinitions的配置顺序为自上而下,以最上面的为准

        //自定义过滤
        //oauth2
        Map<String, Filter> filters = new HashMap<>(16);
        filters.put("oauth2", new Oauth2Filter());
        shiroFilter.setFilters(filters);
     
        //当运行一个Web应用程序时,Shiro将会创建一些有用的默认Filter实例,并自动地在[main]项中将它们置为可用自动地可用的默认的Filter实例是被DefaultFilter枚举类定义的,枚举的名称字段就是可供配置的名称
        /**
         * anon---------------org.apache.shiro.web.filter.authc.AnonymousFilter 没有参数，表示可以匿名使用。
         * authc--------------org.apache.shiro.web.filter.authc.FormAuthenticationFilter 表示需要认证(登录)才能使用，没有参数
         * authcBasic---------org.apache.shiro.web.filter.authc.BasicHttpAuthenticationFilter 没有参数表示httpBasic认证
         * logout-------------org.apache.shiro.web.filter.authc.LogoutFilter
         * noSessionCreation--org.apache.shiro.web.filter.session.NoSessionCreationFilter
         * perms--------------org.apache.shiro.web.filter.authz.PermissionAuthorizationFilter 参数可以写多个，多个时必须加上引号，并且参数之间用逗号分割，例如/admins/user/**=perms["user:add:*,user:modify:*"]，当有多个参数时必须每个参数都通过才通过，想当于isPermitedAll()方法。
         * port---------------org.apache.shiro.web.filter.authz.PortFilter port[8081],当请求的url的端口不是8081是跳转到schemal://serverName:8081?queryString,其中schmal是协议http或https等，serverName是你访问的host,8081是url配置里port的端口，queryString是你访问的url里的？后面的参数。
         * rest---------------org.apache.shiro.web.filter.authz.HttpMethodPermissionFilter 根据请求的方法，相当于/admins/user/**=perms[user:method] ,其中method为post，get，delete等。
         * roles--------------org.apache.shiro.web.filter.authz.RolesAuthorizationFilter 参数可以写多个，多个时必须加上引号，并且参数之间用逗号分割，当有多个参数时，例如admins/user/**=roles["admin,guest"],每个参数通过才算通过，相当于hasAllRoles()方法。
         * ssl----------------org.apache.shiro.web.filter.authz.SslFilter 没有参数，表示安全的url请求，协议为https
         * user---------------org.apache.shiro.web.filter.authz.UserFilter 没有参数表示必须存在用户，当登入操作时不做检查
         */
     
        /**
         * 通常可将这些过滤器分为两组
         * anon,authc,authcBasic,user是第一组认证过滤器
         * perms,port,rest,roles,ssl是第二组授权过滤器
         * 注意user和authc不同：当应用开启了rememberMe时,用户下次访问时可以是一个user,但绝不会是authc,因为authc是需要重新认证的
         * user表示用户不一定已通过认证,只要曾被Shiro记住过登录状态的用户就可以正常发起请求,比如rememberMe 说白了,以前的一个用户登录时开启了rememberMe,然后他关闭浏览器,下次再访问时他就是一个user,而不会authc
         *
         *
         * 举几个例子
         *  /admin=authc,roles[admin]      表示用户必需已通过认证,并拥有admin角色才可以正常发起'/admin'请求
         *  /edit=authc,perms[admin:edit]  表示用户必需已通过认证,并拥有admin:edit权限才可以正常发起'/edit'请求
         *  /home=user     表示用户不一定需要已经通过认证,只需要曾经被Shiro记住过登录状态就可以正常发起'/home'请求
         */

 




        /**
         * 各默认过滤器常用如下(注意URL Pattern里用到的是两颗星,这样才能实现任意层次的全匹配)
         * /admins/**=anon             无参,表示可匿名使用,可以理解为匿名用户或游客
         *  /admins/user/**=authc       无参,表示需认证才能使用
         *  /admins/user/**=authcBasic  无参,表示httpBasic认证
         *  /admins/user/**=ssl         无参,表示安全的URL请求,协议为https
         *  /admins/user/**=perms[user:add:*]  参数可写多个,多参时必须加上引号,且参数之间用逗号分割,如/admins/user/**=perms["user:add:*,user:modify:*"]。当有多个参数时必须每个参数都通过才算通过,相当于isPermitedAll()方法
         *  /admins/user/**=port[8081] 当请求的URL端口不是8081时,跳转到schemal://serverName:8081?queryString。其中schmal是协议http或https等,serverName是你访问的Host,8081是Port端口,queryString是你访问的URL里的?后面的参数
         *  /admins/user/**=rest[user] 根据请求的方法,相当于/admins/user/**=perms[user:method],其中method为post,get,delete等
         *  /admins/user/**=roles[admin]  参数可写多个,多个时必须加上引号,且参数之间用逗号分割,如：/admins/user/**=roles["admin,guest"]。当有多个参数时必须每个参数都通过才算通过,相当于hasAllRoles()方法
         *
         */

 




        //Shiro验证URL时,URL匹配成功便不再继续匹配查找(所以要注意配置文件中的URL顺序,尤其在使用通配符时)
        // 配置不会被拦截的链接 顺序判断
        Map<String, String> filterMap = new LinkedHashMap<>();
        filterMap.put("/favicon.ico", "anon");
        filterMap.put("/webjars/**", "anon");
        filterMap.put("/web/**", "anon");
        filterMap.put("/login", "anon");
     
        //所有请求需要oauth2认证
        filterMap.put("/**", "oauth2");
        shiroFilter.setFilterChainDefinitionMap(filterMap);
        return shiroFilter;
    }
# 添加注解

https://www.cnblogs.com/bbthome/p/8688849.html

# 自定义过滤器

https://www.cnblogs.com/woxbwo/p/11421685.html

```java
@Bean
public ShiroFilterChainDefinition shiroFilterChainDefinition() {
    DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();

    Map<String, String> filterMap = new LinkedHashMap<>();

    filterMap.put("/**", "jwt");
    chainDefinition.addPathDefinitions(filterMap);
    return chainDefinition;
}

@Bean("shiroFilterFactoryBean")
public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager,
                                                     ShiroFilterChainDefinition shiroFilterChainDefinition) {
    ShiroFilterFactoryBean shiroFilter = new ShiroFilterFactoryBean();
    shiroFilter.setSecurityManager(securityManager);

    Map<String, Filter> filters = new HashMap<>();
    filters.put("jwt", jwtFilter);
    shiroFilter.setFilters(filters);

    Map<String, String> filterMap = shiroFilterChainDefinition.getFilterChainMap();

    shiroFilter.setFilterChainDefinitionMap(filterMap);
    return shiroFilter;
}
```

# shiro 整合 jwt

学习项目地址：

https://github.com/sunnj/story-admin

https://github.com/dolyw/ShiroJwt

https://github.com/febsteam/FEBS-Shiro

讲解：

https://www.sundayfine.com/shiro-jwt/

